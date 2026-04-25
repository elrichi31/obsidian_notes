---
tags:
  - ccsa
  - crowdstrike
  - siem
  - logscale
  - cql
  - groupby
---

# groupBy() en SIEM - CrowdStrike LogScale

> [!info] Idea clave
> En CrowdStrike Falcon LogScale, `groupBy()` sirve para **agrupar eventos por campo** y ejecutar **agregaciones** sobre cada grupo.

> [!important] Para examen
> `groupBy()` no busca patrones multi-evento como `correlate()` ni mira el evento vecino como `neighbor()`. Su papel principal es **resumir datos**.

## Que hace `groupBy()`

La documentacion oficial indica que `groupBy()`:

- organiza eventos en grupos por uno o varios campos
- ejecuta funciones de agregacion en cada grupo
- usa `count()` por defecto si no indicas otra funcion

## Ejemplo simple

```logscale
groupBy(status_code)
```

Esto cuenta cuantas veces aparece cada `status_code`.

## Ejemplo para valores unicos

```logscale
groupBy(status_code, function=[])
```

Esto no agrega conteos; solo devuelve los valores unicos.

## Para que sirve en un SIEM

Casos tipicos:

- contar eventos por host
- resumir actividad por usuario
- ver estados mas frecuentes
- detectar volumetria anormal por categoria
- crear visualizaciones o tablas para reportes

## Diferencia con `correlate()`

`groupBy()`:

- agrupa y resume
- produce estadistica o tablas agregadas
- sirve para responder "cuantos", "cuales", "que combinaciones"

`correlate()`:

- busca patrones entre multiples eventos
- devuelve coincidencias que cumplen links y constraints
- sirve para responder "que conjunto de eventos hizo match"

## Diferencia con `neighbor()`

`groupBy()`:

- no mira orden secuencial por si solo
- agrupa por valores comunes

`neighbor()`:

- trabaja sobre una secuencia ordenada
- mira el evento anterior o siguiente

## Dato importante de la documentacion

> [!warning] Limite oficial
> La documentacion advierte que `groupBy()` puede devolver resultados incompletos si se supera el `limit` de grupos, y ademas funciona completamente en memoria.

Eso significa:

- si hay demasiados grupos, puedes no ver todos
- el parametro `limit` importa
- no conviene usarlo sin cuidado en datasets enormes

## Cuando usar `groupBy()`

Usalo cuando necesites:

- conteos
- agrupaciones
- combinaciones unicas
- tablas resumidas
- visualizaciones por categoria

## Respuesta corta tipo examen

> [!success] Respuesta lista para decir
> En CrowdStrike Falcon LogScale, `groupBy()` es una funcion de agregacion que agrupa eventos por uno o varios campos y ejecuta funciones como `count()` sobre cada grupo. Se usa para resumir datos, no para correlacionar patrones multi-evento ni para comparar eventos consecutivos.

## Relacionadas

- [[correlate() en SIEM - CrowdStrike LogScale]]
- [[Neighbor en SIEM - CrowdStrike LogScale]]
- [[CCSA - Querying and Analytics]]
- [[Mapa CCSA CrowdStrike]]

## Fuentes

- LogScale Docs - `groupBy()`: https://library.humio.com/data-analysis/functions-groupby.html
- LogScale Terminology - `groupBy()`: https://library.humio.com/logscale-terminology/terminology-groupby.html
