---
tags:
  - ccsa
  - crowdstrike
  - siem
  - logscale
  - cql
  - definetable
  - match
---

# defineTable() y match() en SIEM - CrowdStrike LogScale

> [!info] Idea clave
> `defineTable()` crea una tabla temporal con una subquery. `match()` usa esa tabla para filtrar o enriquecer la busqueda principal. Juntos sirven para hacer pivots tipo "primero encuentra A, despues busca B relacionado con A".

## Explicacion simple

Piensalo asi:

1. `defineTable()` = "guarda esta lista temporal".
2. Query principal = "ahora busca eventos normales".
3. `match()` = "dejame pasar solo los eventos que coincidan con la lista temporal".

Ejemplo mental:

- primero guardo los hosts donde estuvo `jthompson`
- despues busco todos los procesos de esos hosts
- finalmente saco que otros usuarios estuvieron activos ahi

## Estructura basica

```logscale
defineTable(
  query={
    condicion_para_crear_la_tabla
    | groupBy(campo_que_quiero_guardar)
  },
  include=[campo_que_quiero_guardar],
  name="_mi_tabla"
)
| busqueda_principal
| match("_mi_tabla", field="campo_evento", column="campo_tabla")
```

Traduccion:

- `query={...}` define que datos se guardan.
- `include=[...]` dice que columnas tendra la tabla temporal.
- `name="_mi_tabla"` le pone nombre a esa tabla.
- `match()` compara un campo del evento actual contra una columna de la tabla.

## Diferencia entre `field` y `column`

> [!important] Esto es lo que mas confunde
> En `match()`, `field` es el campo del evento actual. `column` es la columna de la tabla temporal.

```logscale
| match("_host_match_table", field="host.name", column="host.name")
```

Lee eso asi:

- toma el `host.name` del evento que estoy viendo ahora
- comparalo contra la columna `host.name` de `_host_match_table`
- si coincide, deja pasar el evento

## Ejemplo 1: otros usuarios en los mismos hosts que jthompson

Escenario:

> `jthompson` disparo detecciones de credential access. Necesitas saber que otros usuarios estuvieron activos en los mismos hosts en los ultimos 7 dias.

Query:

```logscale
defineTable(
  query={
    user.name="jthompson"
    | groupBy(host.name)
  },
  start=7d,
  end=1d,
  include=[host.name],
  name="_host_match_table"
)
| #event_simpleName="ProcessRollup2"
| match("_host_match_table", field="host.name", column="host.name")
| groupBy("user.name")
| user.name!="jthompson"
```

### Paso por paso

1. La subquery busca eventos de `jthompson`.
2. `groupBy(host.name)` deja una lista unica de hosts donde aparecio.
3. `defineTable()` guarda esa lista como `_host_match_table`.
4. La query principal busca eventos `ProcessRollup2`.
5. `match()` deja pasar solo eventos cuyo `host.name` este en la tabla.
6. `groupBy("user.name")` resume que usuarios estuvieron en esos hosts.
7. `user.name!="jthompson"` excluye al usuario original.

### Por que esta es la respuesta correcta

Porque el pivot real es:

```text
jthompson -> hosts donde estuvo -> otros usuarios en esos hosts
```

No quieres matchear `user.name` y `host.name` al mismo tiempo, porque eso solo encontraria eventos del mismo usuario en el mismo host. Para descubrir **otros usuarios**, la llave correcta del pivot es `host.name`.

## Ejemplo 2: procesos que abrieron puertos privilegiados

Escenario:

> Encuentras eventos de escucha en puertos menores a 1024 y quieres saber que procesos los crearon.

```logscale
defineTable(
  query={
    #event_simpleName="NetworkListenIP4"
    LocalPort < 1024
    LocalPort != 0
  },
  include=[ContextProcessId, LocalAddressIP4, LocalPort],
  name="_network_listener"
)
| #event_simpleName="ProcessRollup2"
| match("_network_listener", field="TargetProcessId", column="ContextProcessId")
```

### Paso por paso

1. La tabla temporal guarda procesos que escuchan en puertos privilegiados.
2. La query principal busca eventos de proceso.
3. `match()` une el `TargetProcessId` del proceso con el `ContextProcessId` del listener.
4. El resultado te ayuda a responder que proceso abrio el puerto.

## Ejemplo 3: excluir IPs conocidas

Escenario:

> Quieres ver conexiones externas excepto IPs ya conocidas o permitidas.

```logscale
defineTable(
  query={
    list_type="allowlist"
    | groupBy(destination.ip)
  },
  include=[destination.ip],
  name="_allowed_ips"
)
| network.direction="outbound"
| destination.ip=*
| !match("_allowed_ips", field="destination.ip", column="destination.ip")
```

`!match()` significa:

- deja pasar lo que **no** coincida con la tabla
- util para encontrar lo desconocido, no permitido o inesperado

## Ejemplo 4: enriquecer sin filtrar todo

`match()` tambien puede enriquecer eventos. Si se usa con `strict=false`, los eventos sin match pueden seguir pasando.

Idea conceptual:

```logscale
| match(file="critical_assets.csv", field="host.name", column="hostname", include=["owner", "criticality"], strict=false)
```

Esto sirve cuando quieres:

- conservar todos los eventos
- agregar metadata solo cuando exista match
- no perder eventos por no estar en la tabla

## Cuando usar `defineTable()` + `match()`

Usalo cuando la pregunta diga algo como:

- "encuentra otros usuarios relacionados con los hosts de X"
- "busca eventos que coincidan con una lista generada por otra query"
- "primero identifica activos/usuarios/IPs y luego pivotea"
- "enriquece eventos con una tabla temporal"
- "excluye elementos ya conocidos"

## Cuando NO es lo ideal

No es lo ideal cuando:

- necesitas una secuencia exacta de varios pasos: usa `correlate()` con `sequence=true`
- necesitas evento anterior/siguiente: usa `neighbor()`
- solo necesitas conteos simples: usa `groupBy()`
- la tabla temporal seria enorme y poco selectiva

## Errores comunes

- matchear por el campo equivocado
- incluir `user.name` en la tabla cuando lo que querias era pivotear por `host.name`
- olvidar `include=[...]`
- creer que `field` y `column` son lo mismo
- usar `match()` cuando necesitas una secuencia temporal
- no filtrar antes de crear la tabla y terminar con demasiados datos

## Mini resumen tipo examen

> [!success] Respuesta lista para decir
> `defineTable()` crea una tabla temporal a partir de una subquery y `match()` compara los eventos de la query principal contra esa tabla. Es util para pivots como "encuentra los hosts de un usuario y luego busca otros usuarios en esos hosts".

## Preguntas de practica

1. Que hace `defineTable()`?
2. Que hace `match()`?
3. En `match(table, field=..., column=...)`, que significa `field`?
4. Si quieres encontrar otros usuarios en los hosts de `jthompson`, cual deberia ser la llave de match?
5. Cuando usarias `!match()`?
6. Cuando preferirias `correlate()` en vez de `defineTable()` + `match()`?

## Respuestas rapidas

1. Ejecuta una subquery y crea una tabla temporal en memoria.
2. Filtra o enriquece eventos comparandolos contra una tabla o lookup.
3. Es el campo del evento actual en la query principal.
4. `host.name`, porque primero pivoteas de usuario a hosts y despues de hosts a otros usuarios.
5. Cuando quieres excluir eventos que aparecen en una lista conocida.
6. Cuando necesitas validar una secuencia de eventos relacionados con orden o ventana temporal.

## Relacionadas

- [[Chuleta CQL para CCSA - CrowdStrike LogScale]]
- [[CCSA - Querying and Analytics]]
- [[CCSA - Incident Investigation]]
- [[correlate() en SIEM - CrowdStrike LogScale]]
- [[Neighbor en SIEM - CrowdStrike LogScale]]
- [[Preguntas tipo escenario - CCSA CrowdStrike]]

## Fuentes

- LogScale `defineTable()`: https://library.humio.com/data-analysis/functions-definetable.html
- LogScale `match()`: https://library.humio.com/data-analysis/functions-match.html
- LogScale Join Methods: https://library.humio.com/data-analysis/query-joins-methods.html
