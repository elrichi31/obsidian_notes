---
tags:
  - ccsa
  - crowdstrike
  - siem
  - exam
  - practice
  - review
---

# Lecciones del simulacro CCSA - CrowdStrike

> [!info] Resultado
> Simulacro: **23/25 = 92%**. El nivel esta fuerte. Las fallas no fueron por desconocer CQL basico, sino por interpretar escenarios complejos donde se mezclan MITRE, lateral movement, persistence, C2, baselines, exclusions y thresholds.

## Diagnostico rapido

Fortalezas claras:

- entiendes CPS y por que evita queries especificas por vendor
- reconoces arrays CPS como `event.category[]` y `event.type[]`
- sabes usar `cidr()` para rangos IP
- entiendes `ioc:lookup()` y parametros como `type`, `confidenceThreshold` y `strict`
- identificas MITRE ATT&CK en escenarios directos
- entiendes correlation rules, secuencia y reduccion de falsos positivos
- sabes que un espacio en CQL implica `AND`
- reconoces scope multi-host cuando un usuario comprometido toca varios sistemas

Area principal a reforzar:

- leer una historia completa de ataque sin quedarte con una sola tecnica llamativa
- leer una correlation rule compleja como pipeline, en orden
- distinguir por que un evento no disparo: exclusion, baseline, threshold o falta de match

## Regla mental para el examen

> [!tip] Lectura de preguntas largas
> Cuando la pregunta tiene mucho texto, no empieces por las opciones. Primero dibuja la historia.

Usa este orden:

1. Quien actua?
2. Desde donde?
3. Hacia donde?
4. Que tecnica se usa?
5. Que evidencia confirma o descarta?
6. Hay filtros, exclusiones, baseline o threshold?
7. La pregunta pide la primera causa, la tecnica principal o el resultado final?

## Tema 1: CPS y queries agnosticas a vendor

Si logs de varios firewalls estan normalizados con CPS, evita campos como:

```logscale
pan.action
cisco.fw_action
fortinet.action
```

Prefiere campos normalizados:

```logscale
network.direction="outbound"
| array:contains("event.category[]", value="network")
| array:contains("event.type[]", value="denied")
| cidr("destination.ip", value=["192.0.2.0/24"])
```

### Por que esta es la respuesta correcta

- `network.direction="outbound"` filtra trafico saliente.
- `event.category[]` puede ser array, por eso se usa `array:contains()`.
- `event.type[]` tambien puede contener varios valores.
- `cidr()` valida si `destination.ip` cae dentro del rango `192.0.2.0/24`.
- La query funciona aunque cambie el vendor, siempre que el parser respete CPS.

### Trampa comun

```logscale
destination.ip="192.0.2.0/24"
```

Eso no es lo mismo que evaluar un rango CIDR. Para rangos usa `cidr()`.

## Tema 2: MITRE en una cadena de eventos

Ejemplo del simulacro:

```text
PowerShell dumpea LSASS
Run key apunta a svchost.exe en Public
WMI ejecuta cmd.exe en file server remoto
Usuario agregado a Administrators local
```

Mapeo correcto:

| Evento | Evidencia | Tactica MITRE |
|---|---|---|
| LSASS dump | `Get-Process lsass` + dump | Credential Access |
| Run key | `CurrentVersion\Run` | Persistence |
| WMI remoto | autenticacion + proceso remoto | Lateral Movement |
| Local Administrators | usuario agregado a admins | Privilege Escalation |

### Regla mental

- LSASS, browser cookies, credential stores = **Credential Access**
- Run keys, scheduled tasks, services auto-start = **Persistence**
- WMI, SMB, RDP, WinRM hacia otro host = **Lateral Movement**
- agregar privilegios, admin group, SYSTEM, UAC bypass = **Privilege Escalation**
- conexion saliente a infraestructura sospechosa = **Command and Control**

## Tema 3: correlation rules vs saved searches simples

Para password spraying:

```text
muchos fallos
muchos usuarios distintos
misma source.ip
ventana corta
```

Mejor enfoque:

```logscale
#event.category="authentication"
#event.outcome="failure"
| groupBy(source.ip, function=count(user.name, distinct=true, as=unique_users))
| unique_users >= 10
```

La ventaja no es que bloquee automaticamente ni que ingiera mas rapido. La ventaja real es:

```text
reduce falsos positivos porque alerta solo cuando el patron agregado parece ataque
```

## Tema 4: `ioc:lookup()` para contexto de amenaza

### IPs con medium o mas, sin filtrar eventos

```logscale
| ioc:lookup(field=destination.ip, type="ip_address", confidenceThreshold="medium", strict=false)
```

Traduccion:

- `type="ip_address"`: estas consultando IPs.
- `confidenceThreshold="medium"`: medium, high y superiores segun aplique.
- `strict=false`: los eventos pasan aunque no haya match.

### IP origen y destino en la misma llamada

```logscale
| ioc:lookup(field=[source.ip, destination.ip], type="ip_address", confidenceThreshold="high", strict=false)
```

Por que sirve:

- revisa ambos campos
- no obliga a que ambos sean maliciosos
- con `strict=false`, no elimina eventos sin match
- para una regla, luego filtras por los campos de deteccion que devuelva el lookup

### Dominios

```logscale
| ioc:lookup(field=[domain], basetype="domain", type="domain", include=["labels", "type"])
```

### Como leer labels

Ejemplo:

```text
KillChain/C2
Malware/AsyncRAT
ThreatType/Commodity
MaliciousConfidence/High
```

Interpretacion:

```text
dominio asociado a command and control para malware commodity, con alta confianza
```

## Tema 5: `correlate()` y secuencia

Si ves:

```logscale
correlate(
  AccountEnabled: {...},
  IDPDetection: {...},
  AccountDisabled: {...},
  sequence=true,
  within=24h
)
```

Entonces:

- `sequence=true` exige orden exacto.
- `within=24h` limita el tiempo total.
- el orden de los bloques importa.

Respuesta mental:

```text
cuenta habilitada -> alerta de identity protection -> cuenta deshabilitada, en ese orden, dentro de 24h
```

Si `sequence=false` o no existiera secuencia, podria encontrar eventos relacionados sin exigir ese orden.

## Tema 6: detecciones passthrough y exclusion rules

Si una deteccion de un producto externo se repite y ya se comprobo falso positivo para un host especifico:

```text
detection title + host parcheado + falso positivo repetido
```

Mejor accion:

```text
crear una Next-Gen SIEM detection exclusion rule especifica
```

### Por que no Fusion Workflow

Un workflow que auto-resuelve baja el trabajo visible, pero la deteccion igual se genera. Para reducir alert fatigue desde la raiz, la exclusion es mejor si el caso esta validado.

### Por que no modificar CQL

Si es una third-party passthrough detection, no necesariamente controlas la logica original de deteccion como si fuera una correlation rule propia.

## Tema 7: lateral movement con servicio remoto

Escenario del fallo:

```text
WORKSTATION-5521 -> SMB auth to SERVER-2847
wmiprvse.exe -> sc.exe create service
service auto-start -> LocalSystem
update.exe -> outbound 443
```

Respuesta correcta:

```text
Lateral Movement + Persistence + Command and Control
```

### Por que no era principalmente privilege escalation

El servicio corre como `LocalSystem`, eso puede dar privilegios altos en el host. Pero la historia completa empieza con acceso remoto desde una workstation no autorizada usando `svc_backup`, seguido por WMI y creacion remota de servicio.

La tecnica central del escenario es:

```text
compromised credentials -> remote execution via WMI/service creation -> persistence -> outbound C2
```

### Claves que gritaban lateral movement

- host origen distinto del host destino
- cuenta de dominio usada fuera de su patron normal
- SMB hacia servidor
- WMI / `wmiprvse.exe`
- `sc.exe \\SERVER create ...`
- multiples sistemas o ejecucion remota

## Tema 8: leer una correlation rule con baseline

Orden mental:

```text
defineTable baseline
-> main query
-> tuning exclusions
-> NOT match contra baseline
-> groupBy
-> threshold
```

Ejemplo del simulacro:

```logscale
defineTable(
  start=7d,
  end=70m,
  query={ ... | groupBy([TargetFileName, UserName, UserSid], limit=max) },
  include=[*],
  name="baseline_browser_cookie_smb_access"
)

| main query
| UserName =~ NOT in(values=["corp_nessus_scan", "security_scanner", "tenable_svc"])
| ClientComputerName =~ NOT in(values=["scanner-host-01", "scanner-host-02", "nessus-scanner-01"])
| NOT match(file="baseline_browser_cookie_smb_access", field=[TargetFileName, UserName, UserSid])
| groupBy(... count(TargetFileName, distinct=true, as=_distinct_cookie_files))
| _distinct_cookie_files >= 3
```

### Como decidir por que alguien no aparecio

| Entrada | Motivo probable |
|---|---|
| `security_audit_svc` desde `scanner-host-02`, 6 files | excluido por `ClientComputerName` |
| `backup_service`, 2 files | no cumple threshold `>= 3` |
| `it_admin_svc`, 4 files | podria haber matcheado baseline; hay que confirmarlo |

### Trampa clave

No todos los eventos que no aparecen fueron excluidos por la misma razon.

El examen quiere que leas cada etapa del pipeline:

- primero exclusiones exactas
- despues anti-baseline
- despues agregacion
- despues threshold

## Tema 9: impossible travel con `neighbor()`

La idea de impossible travel:

```text
login exitoso actual
vs login exitoso anterior del mismo usuario
distancia geografica / tiempo transcurrido
velocidad imposible
```

Problema del query del simulacro:

```logscale
#event.category="authentication" #event.outcome="success"
| ipLocation(source.ip)
| neighbor(...)
```

Falta establecer orden y particion mental por usuario.

### Regla mental

Antes de `neighbor()` pregunta:

1. Los eventos estan ordenados por tiempo?
2. Estoy comparando el evento anterior del mismo usuario?
3. El campo anterior realmente representa el evento previo que quiero?

`neighbor()` sin orden claro puede comparar contra un evento anterior que no significa nada para la investigacion.

## Tema 10: scope de incidente

Si la deteccion original esta en un host, pero el usuario comprometido autentica a 15 hosts mas:

```text
1 host inicial + 15 hosts adicionales = 16 sistemas en scope
```

Respuesta correcta:

```text
multi-host incident requiring consolidated investigation and escalation
```

No abras 16 historias aisladas si el mismo usuario y misma ventana temporal conectan todo.

## Mini chuleta del simulacro

| Pregunta mental | Respuesta |
|---|---|
| Espacio entre filtros CQL | `AND` |
| Retencion default de ingested data | 7 dias |
| Correlation rules | custom detections multi-source/event |
| Password spraying | distinct users por source IP en ventana corta |
| MITRE tactic | por que lo hace el adversario |
| MITRE technique | como lo hace |
| Case SLAs | expectativas de resolucion por tiempo |
| Community ID | hash abierto para correlacionar sesiones de red |
| Rule library filters | encontrar tactics/techniques unassigned |
| Detection exclusion | reducir alert fatigue validada |

## Preguntas de practica

1. Por que CPS permite escribir una sola query para varios firewalls?
2. Por que `cidr()` es mejor que `destination.ip="192.0.2.0/24"`?
3. Que diferencia hay entre `strict=true` y `strict=false` en `ioc:lookup()`?
4. Que indica `sequence=true` en `correlate()`?
5. Si una cuenta toca 15 hosts despues de PowerShell malicioso, cual es el scope?
6. Por que WMI remoto + service creation suele indicar lateral movement?
7. En una rule con baseline, que hace `NOT match()`?
8. Como distingues exclusion, baseline y threshold?
9. Por que `neighbor()` necesita orden claro?
10. Cuando conviene una detection exclusion rule?

## Respuestas rapidas

1. Porque los campos normalizados como `destination.ip`, `network.direction` y `event.type[]` abstraen diferencias de vendor.
2. Porque `cidr()` evalua pertenencia a un rango IP; una igualdad compara texto/valor exacto.
3. `strict=true` filtra solo matches; `strict=false` conserva eventos aunque no tengan IOC match.
4. Que los bloques deben ocurrir en el orden escrito.
5. Multi-host incident con 16 sistemas en scope.
6. Porque implica acceso remoto y ejecucion en otro host usando credenciales o mecanismo remoto.
7. Elimina eventos que ya aparecen en el baseline historico.
8. Siguiendo el pipeline: filtros/exclusiones primero, anti-match contra baseline despues, threshold al final.
9. Porque "evento anterior" solo tiene sentido si la secuencia esta ordenada y relacionada.
10. Cuando una deteccion validada como falso positivo repetido ya no aporta valor para un host/condicion especifica.

## Relacionadas

- [[Como leer correlation rules complejas - CCSA CrowdStrike]]
- [[Preguntas tipo escenario - CCSA CrowdStrike]]
- [[Chuleta CQL para CCSA - CrowdStrike LogScale]]
- [[CCSA - Querying and Analytics]]
- [[CCSA - Detection Logic and Alert Analysis]]
- [[CCSA - Incident Investigation]]
- [[CPS - CrowdStrike Parsing Standard para CCSA]]
- [[MITRE ATT&CK para CCSA]]
- [[correlate() en SIEM - CrowdStrike LogScale]]
- [[defineTable() y match() en SIEM - CrowdStrike LogScale]]
- [[Neighbor en SIEM - CrowdStrike LogScale]]
