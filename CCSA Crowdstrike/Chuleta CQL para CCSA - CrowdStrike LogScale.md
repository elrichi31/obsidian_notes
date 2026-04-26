---
tags:
  - ccsa
  - crowdstrike
  - siem
  - cql
  - logscale
  - cheat-sheet
---

# Chuleta CQL para CCSA - CrowdStrike LogScale

> [!info] Para que sirve esta nota
> Esta es una chuleta practica de **CQL / LogScale** para repasar antes del CCSA. No reemplaza la documentacion oficial, pero te ayuda a reconocer que funcion usar segun el escenario.

## Mentalidad de CQL

CQL se piensa como una tuberia:

```logscale
filtro inicial
| transformacion
| agregacion
| visualizacion
```

La idea para examen:

- primero reduces el ruido con filtros
- despues transformas o extraes campos
- luego agregas, correlacionas o visualizas
- finalmente interpretas si hay comportamiento sospechoso

## Filtros basicos

```logscale
event_simpleName=ProcessRollup2
```

```logscale
user.name="admin"
```

```logscale
source.ip=10.10.10.5
```

## Operadores logicos

```logscale
event_simpleName=UserLogon AND user.name="admin"
```

```logscale
event_simpleName=UserLogon OR event_simpleName=UserLogoff
```

```logscale
event_simpleName=UserLogon AND NOT user.name="service_account"
```

> [!tip] Idea de examen
> Si una pregunta pide bajar falsos positivos, casi siempre conviene agregar contexto o exclusion controlada, no borrar la deteccion completa.

## Campos y normalizacion

En Falcon Next-Gen SIEM puedes ver campos originales del vendor y tambien campos normalizados por CPS.

Ejemplos de campos que suelen ser utiles para pivotear:

- `user.name`
- `host.name`
- `source.ip`
- `destination.ip`
- `process.name`
- `process.command_line`
- `file.hash.sha256`
- `url.domain`

No memorices solo nombres exactos: entiende que el examen puede evaluar **que observable usar para pivotear**.

## Agregaciones comunes

### Conteo general

```logscale
count()
```

### Conteo por usuario

```logscale
groupBy(user.name)
```

### Conteo por host y usuario

```logscale
groupBy([host.name, user.name])
```

### Tendencia en el tiempo

```logscale
timeChart()
```

> [!important] Diferencia rapida
> `groupBy()` responde "cuantos por campo"; `timeChart()` ayuda a ver "como cambia en el tiempo".

## Regex y extraccion

Usa regex cuando necesitas extraer o validar patrones dentro de texto, por ejemplo command lines, URLs o mensajes de log.

Ejemplo conceptual:

```logscale
process.command_line=/powershell/i
```

Para examen, lo importante no es escribir regex perfecta, sino saber cuando usarla:

- buscar patrones dentro de command line
- detectar dominios o rutas sospechosas
- extraer valores cuando el parser no los dejo como campo

## parseJson()

`parseJson()` sirve cuando el evento trae contenido JSON en un campo y necesitas convertirlo en campos consultables.

Usalo mentalmente cuando veas:

- logs cloud en formato JSON
- payloads dentro de un campo
- eventos donde la informacion esta anidada

## lookup

Un lookup sirve para enriquecer datos contra una tabla externa o lista de referencia.

Casos tipicos:

- comparar IPs contra una lista de allowlist o blocklist
- mapear asset criticality
- agregar owner del sistema
- enriquecer dominios o hashes con contexto

> [!tip] Idea de examen
> Un lookup no prueba por si solo que algo es malicioso; agrega contexto para decidir mejor.

## join

`join` se usa cuando necesitas combinar resultados con otra fuente o subquery por un campo comun.

Ejemplo mental:

- eventos de autenticacion unidos con inventario de usuarios
- conexiones de red unidas con lista de activos criticos
- alertas unidas con informacion de owner o unidad de negocio

## session

Piensa en `session` cuando quieras agrupar actividad relacionada dentro de una ventana temporal, por ejemplo:

- actividad de un usuario durante una sesion
- secuencia de eventos de un host
- comportamiento antes y despues de un login

## bucket()

`bucket()` ayuda a agrupar eventos en intervalos de tiempo.

Sirve para:

- ver picos por minuto, hora o dia
- crear resumenes temporales
- preparar datos para visualizaciones

## Como elegir la funcion correcta

| Escenario | Funcion probable |
|---|---|
| Contar eventos por usuario | `groupBy(user.name)` |
| Ver tendencia temporal | `timeChart()` o `bucket()` |
| Comparar evento actual con anterior | `neighbor()` |
| Buscar patron multi-evento | `correlate()` |
| Enriquecer con tabla externa | `lookup` |
| Crear tabla temporal desde subquery | `defineTable()` |
| Comparar contra tabla temporal o lookup | `match()` |
| Unir datos por campo comun | `join` |
| Extraer datos de JSON | `parseJson()` |
| Buscar patron dentro de texto | regex |

## Preguntas tipo examen

1. Si tienes demasiados eventos de login y quieres saber que usuarios aparecen mas, que usas?
2. Si quieres ver si los eventos subieron de golpe durante una hora especifica, que usas?
3. Si quieres relacionar login sospechoso + conexion a host critico + descarga de datos, que concepto usas?
4. Si un campo contiene JSON crudo y necesitas consultar un valor interno, que funcion te sirve?
5. Si necesitas agregar criticidad de activos desde una tabla, que mecanismo usas?
6. Si necesitas encontrar otros usuarios activos en los hosts de un usuario sospechoso, que patron CQL te sirve?

## Respuestas rapidas

1. `groupBy(user.name)` o agregacion equivalente.
2. `timeChart()` o `bucket()` con agregacion.
3. Correlacion multi-evento, por ejemplo correlation rule o `correlate()` segun el contexto.
4. `parseJson()`.
5. `lookup`.
6. `defineTable()` para guardar los hosts del usuario sospechoso y `match()` para buscar eventos de otros usuarios en esos hosts.

## Relacionadas

- [[CCSA - Querying and Analytics]]
- [[defineTable() y match() en SIEM - CrowdStrike LogScale]]
- [[groupBy() en SIEM - CrowdStrike LogScale]]
- [[Neighbor en SIEM - CrowdStrike LogScale]]
- [[correlate() en SIEM - CrowdStrike LogScale]]
- [[CPS - CrowdStrike Parsing Standard para CCSA]]

## Fuentes

- LogScale Query Language: https://library.humio.com/data-analysis/syntax-query-language.html
- LogScale Functions: https://library.humio.com/data-analysis/functions.html
- LogScale `groupBy()`: https://library.humio.com/data-analysis/functions-groupby.html
- LogScale `correlate()`: https://library.humio.com/data-analysis-1.219/functions-correlate.html
