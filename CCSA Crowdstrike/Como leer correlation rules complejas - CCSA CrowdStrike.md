---
tags:
  - ccsa
  - crowdstrike
  - siem
  - cql
  - correlation-rules
  - exam
---

# Como leer correlation rules complejas - CCSA CrowdStrike

> [!info] Idea clave
> En el examen, una correlation rule compleja se entiende como una tuberia. No preguntes solo "que hace esta funcion"; pregunta en que orden la query filtra, excluye, compara con baseline, agrupa y aplica thresholds.

## Metodo de lectura en 7 pasos

Cuando veas una query larga, leela asi:

1. **Comentarios**: que dice que intenta detectar?
2. **Baseline**: hay `defineTable()` con datos historicos?
3. **Main query**: que eventos actuales analiza?
4. **Tuning**: que usuarios, hosts, procesos o tools excluye?
5. **Comparacion**: usa `match()`, `NOT match()`, `join()` o `ioc:lookup()`?
6. **Agregacion**: usa `groupBy()`, `count()`, `collect()` o distinct?
7. **Threshold**: que condicion final dispara la deteccion?

> [!tip] Regla mental
> Si una cuenta no aparece en el resultado, no asumas una sola causa. Puede haber sido eliminada por exclusion, baseline, falta de match, threshold o ventana temporal.

## Forma general de una rule con baseline

```logscale
defineTable(
  start=7d,
  end=70m,
  query={
    eventos_historicos
    | groupBy([campos_de_baseline], limit=max)
  },
  include=[*],
  name="baseline_name"
)

| eventos_actuales
| exclusiones_de_tuning
| NOT match(file="baseline_name", field=[campos_de_comparacion])
| groupBy([campos_de_resumen], function=[collect([...]), count(..., as=conteo)])
| conteo >= threshold
```

Traduccion:

- `defineTable()` crea una tabla historica.
- `start=7d, end=70m` mira desde hace 7 dias hasta hace 70 minutos.
- La main query mira eventos actuales.
- Las exclusiones quitan actividad conocida permitida.
- `NOT match()` deja pasar solo lo que **no** estaba en el baseline.
- `groupBy()` resume y calcula conteos.
- El threshold decide si se dispara.

## Diferencia entre baseline y threshold

| Concepto | Pregunta que responde |
|---|---|
| Baseline | Esto ya habia pasado antes de forma parecida? |
| Exclusion | Esto es una herramienta/cuenta/host aprobado? |
| Threshold | Paso suficientes veces para importar? |
| Aggregation | Como resumo muchos eventos en una alerta legible? |

## Ejemplo explicado: acceso SMB a cookies de navegador

Objetivo:

```text
Detectar acceso inusual a archivos de cookies de otros usuarios por SMB.
```

La rule busca:

- eventos `ClassifiedSensitiveFileAccess`
- Windows
- archivos clasificados como sensibles
- acceso remoto por SMB (`RemoteAddressIP4=*`)
- archivos tipo cookie mediante `$cookie_lookup()`

Despues excluye scanners aprobados:

```logscale
| UserName =~ NOT in(values=["corp_nessus_scan", "security_scanner", "tenable_svc"])
| ClientComputerName =~ NOT in(values=["scanner-host-01", "scanner-host-02", "nessus-scanner-01"])
```

Luego elimina actividad ya vista en baseline:

```logscale
| NOT match(file="baseline_browser_cookie_smb_access", field=[TargetFileName, UserName, UserSid])
```

Finalmente agrupa y aplica threshold:

```logscale
| groupBy([#event_simpleName, UserName, ClientComputerName],
    function=[
      collect([aid, ComputerName, LocalAddressIP4, RemoteAddressIP4, LogonDomain, TargetFileName]),
      count(TargetFileName, distinct=true, as=_distinct_cookie_files)
    ]
)
| _distinct_cookie_files >= 3
```

## Como responder "por que no disparo"

Escenario:

| UserName | ClientComputerName | Distinct files |
|---|---|---|
| `security_audit_svc` | `scanner-host-02` | 6 |
| `backup_service` | `backup-server-01` | 2 |
| `it_admin_svc` | `admin-workstation-05` | 4 |

Analisis:

```text
security_audit_svc -> scanner-host-02 esta excluido por ClientComputerName.
backup_service -> solo 2 archivos, no cumple _distinct_cookie_files >= 3.
it_admin_svc -> no esta excluido y cumple threshold; probablemente matcheo baseline, pero hay que confirmarlo.
```

Respuesta correcta:

```text
Cada uno pudo ser eliminado por una etapa distinta del pipeline.
```

## Como confirmar el caso de baseline

Si sospechas que `it_admin_svc` fue eliminado por baseline, revisa si existe la combinacion historica:

```logscale
// Buscar en la logica que construye el baseline
#repo="base_sensor" #event_simpleName="ClassifiedSensitiveFileAccess"
event_platform="Win" SensitiveFileClassification=1 RemoteAddressIP4=*
| $cookie_lookup()
| UserName="it_admin_svc"
| groupBy([TargetFileName, UserName, UserSid], limit=max)
```

Si aparece en el baseline, entonces `NOT match()` lo elimina de la deteccion actual.

## Tipos de rules que aparecen en preguntas

| Patron | Funcion mental |
|---|---|
| password spraying | `groupBy(source.ip)` + distinct users + threshold |
| impossible travel | auth success + geo + previous event + speed |
| VPN extranjero -> SMB -> upload | sequential correlation |
| actividad nueva vs historica | baseline + `NOT match()` |
| IOC enrichment | `ioc:lookup()` |
| pass-through falso positivo repetido | detection exclusion |
| multi-source custom detection | correlation rule |

## Pattern 1: password spraying

Logica:

```text
misma source.ip
muchos user.name distintos
muchos fallos
ventana corta
```

Query conceptual:

```logscale
#event.category="authentication"
#event.outcome="failure"
| groupBy(source.ip, function=[
    count(as=failures),
    count(user.name, distinct=true, as=unique_users)
  ])
| unique_users >= 10
```

Lo importante:

- no alertar por cada fallo individual
- alertar cuando el patron agregado parece ataque
- reducir falsos positivos con threshold y ventana temporal

## Pattern 2: secuencia multi-source

Ejemplo:

```text
VPN desde pais extranjero
-> SMB a file server dentro de 10 minutos
-> upload externo dentro de 5 minutos
```

Tipo correcto:

```text
sequential correlation rule con `correlate()` o `neighbor()` segun el caso
```

Por que:

- deben ocurrir todos los eventos
- deben ocurrir en orden
- deben relacionarse por usuario
- deben respetar ventanas temporales
- cruzan varias fuentes

## Pattern 3: IOC enrichment

Para enriquecer sin perder eventos:

```logscale
| ioc:lookup(field=destination.ip, type="ip_address", confidenceThreshold="medium", strict=false)
```

Para que solo pasen matches:

```logscale
| ioc:lookup(field=destination.ip, type="ip_address", confidenceThreshold="high", strict=true)
```

Para revisar varios campos:

```logscale
| ioc:lookup(field=[source.ip, destination.ip], type="ip_address", confidenceThreshold="high", strict=false)
```

## Pattern 4: CPS arrays

CPS puede usar arrays:

```logscale
event.category[]
event.type[]
```

Por eso, para buscar un valor dentro del array:

```logscale
| array:contains("event.category[]", value="network")
| array:contains("event.type[]", value="denied")
```

Si usas igualdad simple, puedes fallar cuando el campo trae multiples valores.

## Pattern 5: detection exclusion vs auto-resolve

Si una deteccion third-party se repite y ya fue validada como falso positivo para un host:

```text
detection exclusion rule especifica
```

No es lo mismo que:

- auto-resolver con Fusion
- abrir caso con vendor
- modificar CQL de una deteccion que no controlas

La exclusion reduce alert fatigue porque evita que esa combinacion vuelva a generar ruido.

## Preguntas que debes hacerte ante cada opcion

Cuando leas opciones multiple choice, prueba cada una:

1. Esta opcion conserva o elimina eventos?
2. El parametro `strict` coincide con lo que pide?
3. La funcion trabaja con arrays o campos escalares?
4. La tactica MITRE representa el "por que" o el "como"?
5. La opcion confunde tecnica secundaria con tecnica principal?
6. Hay un threshold que una entrada no cumple?
7. Hay una exclusion exacta por usuario, host, titulo o herramienta?
8. Hay baseline que puede explicar por que algo no aparece?

## Errores comunes

- ver `LocalSystem` y contestar privilege escalation aunque la historia sea lateral movement
- ignorar el host origen y el host destino
- olvidar que `event.category[]` y `event.type[]` pueden ser arrays
- usar `strict=true` cuando la pregunta dice que todos los eventos deben pasar
- creer que `NOT match()` es una exclusion manual; en realidad compara contra una tabla o lookup
- asumir que todos los no-resultados fueron filtrados por la misma razon
- no leer la condicion final del threshold
- confundir tactic con technique

## Mini resumen tipo examen

> [!success] Respuesta lista para decir
> Una correlation rule compleja se lee de arriba hacia abajo: baseline, query principal, tuning, comparacion, agregacion y threshold. Para explicar por que algo disparo o no disparo, identifica exactamente en que etapa del pipeline fue incluido o eliminado.

## Preguntas de practica

1. Que hace `defineTable()` al inicio de una correlation rule?
2. Que significa `start=7d, end=70m` en un baseline?
3. Que hace `NOT match()` contra un baseline?
4. Que diferencia hay entre exclusion y baseline?
5. Por que una cuenta con 2 archivos no dispara si el threshold es `>= 3`?
6. Por que una cuenta con 4 archivos podria no disparar?
7. Que ventaja tiene una correlation rule para password spraying?
8. Cuando usarias `strict=false` en `ioc:lookup()`?
9. Por que WMI remoto suele apuntar a lateral movement?
10. Que funcion mental cumple `groupBy()` antes de un threshold?

## Respuestas rapidas

1. Construye una tabla historica o temporal para comparar contra eventos actuales.
2. Usa datos desde hace 7 dias hasta hace 70 minutos, dejando fuera lo mas reciente.
3. Deja pasar solo eventos que no coinciden con el baseline.
4. Exclusion quita actividad permitida conocida; baseline quita actividad ya observada historicamente.
5. Porque no cumple la condicion final.
6. Porque pudo haber coincidido con baseline o haber sido excluida antes.
7. Reduce falsos positivos al alertar por patron agregado, no por cada fallo.
8. Cuando quieres enriquecer sin eliminar eventos sin match.
9. Porque implica ejecucion remota en otro host.
10. Resume muchos eventos y calcula valores como conteos, distinct y colecciones.

## Relacionadas

- [[Lecciones del simulacro CCSA - CrowdStrike]]
- [[Preguntas tipo escenario - CCSA CrowdStrike]]
- [[defineTable() y match() en SIEM - CrowdStrike LogScale]]
- [[correlate() en SIEM - CrowdStrike LogScale]]
- [[Neighbor en SIEM - CrowdStrike LogScale]]
- [[CCSA - Detection Logic and Alert Analysis]]
- [[CCSA - Incident Investigation]]
