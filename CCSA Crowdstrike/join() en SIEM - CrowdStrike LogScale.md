---
tags:
  - ccsa
  - crowdstrike
  - siem
  - logscale
  - cql
  - join
---

# join() en SIEM - CrowdStrike LogScale

> [!info] Idea clave
> `join()` combina dos busquedas de LogScale cuando ambas tienen un valor comun. Sirve para enriquecer o filtrar eventos de la busqueda principal usando datos de una subquery.

## Explicacion simple

Piensalo asi:

1. La query principal trae el conjunto base de eventos.
2. La subquery dentro de `join()` trae el segundo conjunto de eventos.
3. `field=` dice que campo de la query principal se usa para comparar.
4. `key=` dice que campo de la subquery se usa para comparar.
5. `include=[...]` dice que campos de la subquery quieres agregar al resultado final.

Ejemplo mental:

```text
eventos de proceso -> TargetProcessId
eventos de red     -> ContextProcessId

Si TargetProcessId y ContextProcessId tienen el mismo valor, pertenecen al mismo proceso.
```

## Sintaxis basica

```logscale
busqueda_principal
| join(
    {subquery},
    field=campo_en_query_principal,
    key=campo_en_subquery,
    include=[campos_de_subquery],
    mode=inner
  )
```

Traduccion:

- `busqueda_principal`: los eventos que quieres conservar, enriquecer o filtrar.
- `{subquery}`: la segunda busqueda que se ejecuta para encontrar datos relacionados.
- `field`: campo de la query principal.
- `key`: campo equivalente dentro de la subquery.
- `include`: campos que quieres traer desde la subquery.
- `mode`: tipo de join; normalmente `inner` o `left`.

## `field` vs `key` vs `include`

> [!important] Esto es lo que mas confunde
> `field` y `key` no son los campos que quieres ver. Son las llaves de comparacion.

| Parametro | Significa |
|---|---|
| `field` | campo de la query principal |
| `key` | campo de la subquery |
| `include` | campos de la subquery que quieres agregar al output |
| `mode=inner` | solo deja eventos que tienen match |
| `mode=left` | conserva eventos de la query principal aunque no tengan match |

Si los dos datasets usan el mismo nombre de campo, `key` puede omitirse en algunos casos porque por defecto usa el valor de `field`. Para estudiar, es mejor escribir ambos cuando estes aprendiendo:

```logscale
| join({user_id=*}, field=user_id, key=user_id, include=[department])
```

## Ejemplo 1: proceso que abrio un puerto privilegiado

Escenario:

> Tienes eventos `NetworkListenIP4` con puertos menores a 1024 y quieres saber que proceso creo el listener.

```logscale
#event_simpleName=ProcessRollup2
| join(
    {#event_simpleName=NetworkListenIP4 LocalPort<1024 LocalPort!=0},
    field=TargetProcessId,
    key=ContextProcessId,
    include=[LocalAddressIP4, LocalPort],
    mode=inner
  )
| table([@timestamp, ComputerName, UserName, FileName, CommandLine, TargetProcessId, LocalAddressIP4, LocalPort])
```

### Paso por paso

1. La query principal busca procesos (`ProcessRollup2`).
2. La subquery busca listeners de red en puertos privilegiados (`NetworkListenIP4`).
3. `field=TargetProcessId` toma el proceso de la query principal.
4. `key=ContextProcessId` toma el proceso que creo el listener en la subquery.
5. Si los valores coinciden, `join()` agrega `LocalAddressIP4` y `LocalPort`.
6. El resultado responde: "que proceso abrio que puerto".

### Por que es util para CCSA

Este tipo de pregunta evalua pivot entre datos de host y red:

```text
proceso -> process id -> listener de red -> puerto/local address
```

## Ejemplo 2: agregar usuario a eventos de proceso usando `UserSid`

Escenario:

> Un evento de proceso trae `UserSid`, pero necesitas ver `UserName`. Otra fuente tiene `UserSid` y `UserName`.

```logscale
#event_simpleName=ProcessRollup2
| UserSid=*
| join(
    {#event_simpleName=UserIdentity UserSid=*},
    field=UserSid,
    key=UserSid,
    include=[UserName],
    mode=left
  )
| table([@timestamp, ComputerName, FileName, CommandLine, UserSid, UserName])
```

### Por que `mode=left`

`mode=left` conserva los procesos aunque no encuentre `UserName`. Esto es bueno cuando no quieres perder evidencia por falta de enriquecimiento.

Si usaras `mode=inner`, solo verias procesos con match en `UserIdentity`.

## Ejemplo 3: enriquecer autenticaciones con datos de usuario

Escenario:

> Tienes eventos de autenticacion con `user_id` y quieres traer `department`, `role` y `location` desde un repo o dataset de referencia.

```logscale
event_type=auth user_id=*
| join(
    {user_id=*},
    field=user_id,
    key=user_id,
    repo="user_details",
    include=[department, role, location],
    mode=left
  )
| groupBy([department, action, status], function=count())
```

### Que responde

Esta query ayuda a responder:

- que departamentos tienen mas fallos de login
- que roles aparecen en actividad sospechosa
- desde que ubicaciones se concentra el comportamiento
- si un usuario privilegiado aparece en eventos de riesgo

> [!tip] Para examen
> Si la pregunta habla de "agregar contexto" sin perder eventos, piensa en `mode=left`. Si la pregunta habla de "solo quiero eventos que existan en ambos lados", piensa en `mode=inner`.

## Ejemplo 4: encontrar sesiones que existen en A pero no en B

Escenario:

> Quieres encontrar sesiones registradas en una fuente A que no aparecen en una fuente B.

```logscale
#repo=A session_id=*
| !join(
    {#repo=B session_id=*},
    field=session_id,
    key=session_id
  )
```

Esto se llama mentalmente un anti-join:

```text
dejame lo que esta en A pero no esta en B
```

> [!warning] Recomendacion practica
> Para casos de exclusion, la documentacion de LogScale recomienda considerar `defineTable()` con `!match()` como alternativa a `!join()`, porque suele ser mas claro para joins complejos.

## Ejemplo 5: llave compuesta con usuario + host

Escenario:

> Quieres unir eventos donde no basta con `user.name`, porque el mismo usuario aparece en muchos hosts. Necesitas comparar `user.name` y `host.name`.

```logscale
#event.category="authentication"
| user.name=* host.name=*
| join(
    {#event.category="process" process.name=/powershell|cmd/i user.name=* host.name=*},
    field=[user.name, host.name],
    key=[user.name, host.name],
    include=[process.name, process.command_line],
    mode=inner
  )
```

### Por que usar dos campos

La llave compuesta reduce falsos matches.

```text
solo user.name  -> puede mezclar actividad de muchos hosts
user + host     -> relacion mas precisa
```

## `inner` vs `left`

| Modo | Que devuelve | Uso mental |
|---|---|---|
| `mode=inner` | solo eventos con match en ambos lados | filtrar a lo que esta relacionado |
| `mode=left` | todos los eventos de la query principal, tengan match o no | enriquecer sin perder evidencia |

Ejemplo:

```logscale
// inner: solo procesos que tuvieron listener
#event_simpleName=ProcessRollup2
| join({#event_simpleName=NetworkListenIP4}, field=TargetProcessId, key=ContextProcessId, mode=inner)
```

```logscale
// left: todos los procesos, y agrega datos de listener cuando existan
#event_simpleName=ProcessRollup2
| join({#event_simpleName=NetworkListenIP4}, field=TargetProcessId, key=ContextProcessId, include=[LocalPort], mode=left)
```

## Parametros que debes reconocer

| Parametro | Para que sirve |
|---|---|
| `field` | llave en la query principal |
| `key` | llave en la subquery |
| `include` | campos a traer desde la subquery |
| `mode` | `inner` o `left` |
| `repo` | ejecutar subquery contra otro repositorio |
| `start` / `end` | acotar el tiempo de la subquery |
| `limit` | maximo de filas/eventos de la subquery |
| `max` | cuantos eventos tomar si varios comparten la misma llave |
| `live` | controla si la subquery corre como live o estatica |

## Cuando usar `join()`

Usalo cuando:

- tienes dos datasets con una llave comun
- necesitas enriquecer eventos con contexto de otra fuente
- necesitas cruzar host, red, usuario, proceso o inventario
- necesitas comparar repositorios o fuentes distintas
- quieres traer campos de una subquery al resultado principal

Frases tipo examen:

- "combine query results"
- "enrich authentication events with user details"
- "match process events to network listener events"
- "pivot between host and network datasets"
- "join across repositories"

## Cuando NO es lo ideal

No es lo ideal cuando:

- solo necesitas contar o resumir: usa `groupBy()`
- necesitas una secuencia temporal exacta: usa `correlate()` con `sequence=true` o `series()`
- necesitas evento anterior/siguiente: usa `neighbor()`
- quieres comparar contra una tabla temporal grande o exclusion clara: considera `defineTable()` + `match()`
- estas en una live query sensible al rendimiento: `join()` puede ser costoso porque hace mas de una pasada

## Diferencia con otras funciones

| Necesidad | Funcion |
|---|---|
| Agrupar conteos por campo | `groupBy()` |
| Ver evento anterior o siguiente | `neighbor()` |
| Relacionar varios pasos de una historia | `correlate()` |
| Crear una lista temporal y filtrar contra ella | `defineTable()` + `match()` |
| Combinar dos busquedas por llave comun | `join()` |
| Unir eventos del mismo dataset que comparten una llave | `selfJoin()` o `selfJoinFilter()` |

## `join()` vs `defineTable()` + `match()`

`join()` es directo cuando quieres combinar dos busquedas por una llave y traer campos de la subquery.

`defineTable()` + `match()` suele ser mas claro cuando primero quieres construir una lista temporal y luego reutilizarla para filtrar o enriquecer.

Ejemplo mental:

```text
join()
proceso + listener -> output combinado

defineTable() + match()
primero guardo hosts sospechosos -> luego busco cualquier actividad en esos hosts
```

## `join()` vs `selfJoin()`

`join()` cruza la query principal con una subquery.

`selfJoin()` se usa cuando los eventos estan en el mismo conjunto y comparten una llave, especialmente cuando `groupBy()` podria quedarse corto por demasiadas llaves.

Ejemplo mental:

```text
join()
auth events + user inventory

selfJoin()
eventos de email con mismo email_id donde existe from=peter y to=anders
```

`selfJoinFilter()` es parecido, pero actua mas como filtro: deja pasar eventos que comparten una llave con eventos que cumplen las condiciones.

## Errores comunes

- creer que `field` es el campo que quieres mostrar
- confundir `field` con `key`
- olvidar `include` y luego no ver campos de la subquery
- usar `inner` cuando querias conservar eventos sin match
- usar `left` y asumir que todos los eventos enriquecidos tienen match
- unir por una llave demasiado generica, como solo `user.name`
- no acotar tiempo en la subquery y traer ruido o resultados pesados
- usar `join()` para una secuencia temporal cuando lo correcto era `correlate()` o `series()`
- meter una subquery enorme cuando podias filtrar antes

## Checklist para construir un `join()`

Antes de escribirlo, preguntate:

1. Cual es mi query principal?
2. Cual es mi subquery?
3. Que campo conecta ambos lados?
4. Ese campo tiene el mismo nombre en ambos lados?
5. Que campos quiero traer desde la subquery?
6. Necesito solo matches (`inner`) o conservar todo (`left`)?
7. Debo limitar el tiempo de la subquery con `start` / `end`?

## Mini resumen tipo examen

> [!success] Respuesta lista para decir
> `join()` une dos busquedas de LogScale por una llave comun. `field` es la llave en la query principal, `key` es la llave en la subquery, `include` trae campos de la subquery, `inner` filtra solo coincidencias y `left` conserva la query principal aunque no haya match.

## Preguntas de practica

1. Que hace `join()`?
2. En `join()`, que significa `field`?
3. En `join()`, que significa `key`?
4. Para que sirve `include`?
5. Cuando usarias `mode=inner`?
6. Cuando usarias `mode=left`?
7. Que funcion podria ser mejor que `!join()` para exclusiones complejas?
8. Que diferencia hay entre `join()` y `correlate()`?
9. Que diferencia hay entre `join()` y `defineTable()` + `match()`?
10. Por que una llave compuesta puede bajar falsos matches?

## Respuestas rapidas

1. Une o relaciona dos busquedas usando una llave comun.
2. Es el campo de la query principal que se compara contra la subquery.
3. Es el campo de la subquery que se compara contra `field`.
4. Trae campos de la subquery al resultado final.
5. Cuando solo quieres eventos que tienen match en ambos lados.
6. Cuando quieres conservar todos los eventos principales y enriquecer solo los que tengan match.
7. `defineTable()` con `!match()`.
8. `join()` combina datasets por llave; `correlate()` busca patrones multi-evento con relacion/tiempo.
9. `join()` combina directamente; `defineTable()` + `match()` crea una lista temporal reusable para filtrar o enriquecer.
10. Porque obliga a que coincidan varios campos, no solo un valor demasiado comun.

## Relacionadas

- [[Chuleta CQL para CCSA - CrowdStrike LogScale]]
- [[CCSA - Querying and Analytics]]
- [[defineTable() y match() en SIEM - CrowdStrike LogScale]]
- [[correlate() en SIEM - CrowdStrike LogScale]]
- [[Neighbor en SIEM - CrowdStrike LogScale]]
- [[groupBy() en SIEM - CrowdStrike LogScale]]
- [[Preguntas tipo escenario - CCSA CrowdStrike]]

## Fuentes

- LogScale `join()`: https://library.humio.com/data-analysis/functions-join.html
- LogScale Join Methods: https://library.humio.com/data-analysis/query-joins-methods.html
- LogScale Using `join()`: https://library.humio.com/data-analysis/query-joins-methods-join.html
- LogScale `defineTable()`: https://library.humio.com/data-analysis/functions-definetable.html
- LogScale `match()`: https://library.humio.com/data-analysis/functions-match.html
- LogScale `selfJoin()`: https://library.humio.com/data-analysis/functions-selfjoin.html
- LogScale `selfJoinFilter()`: https://library.humio.com/data-analysis/functions-selfjoinfilter.html
- CrowdStrike LogScale Community Building Blocks: https://github.com/CrowdStrike/logscale-community-content/wiki/LogScale-Query-Building-Blocks
