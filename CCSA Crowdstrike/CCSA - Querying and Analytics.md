---
tags:
  - ccsa
  - crowdstrike
  - siem
  - cql
  - analytics
---

# CCSA - Querying and Analytics

> [!info] Dominio oficial
> Este tema cubre el dominio **Querying and Analytics** del CCSA.

## Que entra en este tema

Segun el Exam Guide, aqui te pueden pedir:

- construir busquedas CQL con filtros, operadores logicos y tiempo
- usar dashboards y scripts prebuilt
- interpretar resultados de busqueda
- pivotear y correlacionar entre datasets relacionados
- usar el **CrowdStrike Parsing Standard (CPS)** para consultas agnosticas a la fuente

## Que debes entender de verdad

### 1. CQL basico

Debes saber:

- filtrar por campos
- usar operadores logicos como `AND`, `OR` y negacion
- limitar por ventanas de tiempo
- leer resultados sin quedarte solo con el primer evento

### 2. Analisis, no solo busqueda

El examen no va solo de "escribir una query". Tambien evalua si puedes:

- interpretar lo que estas viendo
- reconocer patrones raros
- pivotear desde un indicador a otro
- conectar eventos de host, red, email u otras fuentes

### 3. CPS

> [!important] Idea de examen
> **CPS** ayuda a normalizar nombres de campos entre distintas fuentes para que puedas consultar datos de forma mas consistente.

Si varias fuentes siguen CPS:

- los campos son mas uniformes
- es mas facil reutilizar queries
- puedes hacer analisis mas agnostico a proveedor

## Conceptos que te conviene dominar

- filtros por campo
- tiempo relativo y acotacion de busquedas
- agregaciones y resumenes visuales
- pivots entre indicadores
- normalizacion con CPS
- dashboards y consultas guardadas

## CQL que deberias reconocer

No hace falta memorizar cada funcion de LogScale, pero si debes reconocer que usar segun el problema:

| Necesidad | Concepto / funcion |
|---|---|
| filtrar eventos por campo | filtros CQL |
| combinar condiciones | `AND`, `OR`, `NOT` |
| contar por usuario, host o IP | `groupBy()` |
| ver tendencia por tiempo | `timeChart()` o `bucket()` |
| mirar evento anterior/siguiente | `neighbor()` |
| detectar patron multi-evento | `correlate()` o correlation rule |
| extraer datos de JSON | `parseJson()` |
| buscar patrones en texto | regex |
| enriquecer con listas o tablas | `lookup` |
| pivotear con una lista temporal | `defineTable()` + `match()` |
| unir dos busquedas por llave comun | `join()` |

> [!tip] Regla mental
> Si la pregunta dice "muchos eventos, necesito resumir", piensa en agregacion. Si dice "varios pasos relacionados", piensa en correlacion. Si dice "antes o despues", piensa en secuencia.

## Pivots que aparecen mucho

Durante una investigacion, un resultado de busqueda casi nunca es el final. Normalmente haces pivot:

| Si empiezas con... | Puedes pivotear a... |
|---|---|
| `user.name` | hosts usados, logins, cloud activity, procesos |
| `host.name` | usuarios, procesos, conexiones, alertas |
| `source.ip` | geolocalizacion, reputacion, usuarios, eventos de red |
| `destination.ip` | conexiones, dominios, volumen, exfiltration |
| `process.name` | command line, parent process, usuario, host |
| `file.hash.sha256` | hosts afectados, reputacion, ejecuciones |

## Como te lo pueden preguntar

Una pregunta de examen puede darte una situacion como:

- "necesitas encontrar usuarios con mas fallos de login"
- "necesitas ver si hubo pico de conexiones"
- "necesitas revisar que paso inmediatamente despues de un evento"
- "necesitas correlacionar autenticacion sospechosa con descarga de datos"

La respuesta no siempre sera una query exacta. A veces quieren que elijas el **enfoque analitico correcto**.

## Como conecta con tus otras notas

- Para chuleta practica de CQL: [[Chuleta CQL para CCSA - CrowdStrike LogScale]]
- Para pivots con tablas temporales: [[defineTable() y match() en SIEM - CrowdStrike LogScale]]
- Para unir dos busquedas por una llave comun: [[join() en SIEM - CrowdStrike LogScale]]
- Para normalizacion de campos: [[CPS - CrowdStrike Parsing Standard para CCSA]]
- Para contexto inmediato entre eventos: [[Neighbor en SIEM - CrowdStrike LogScale]]
- Para unir varios eventos relacionados: [[correlate() en SIEM - CrowdStrike LogScale]]
- Para la vista global del examen: [[CCSA - Temario oficial y plan de estudio]]

## Mini resumen tipo examen

> [!success] Respuesta rapida
> Querying and Analytics evalua si puedes buscar, interpretar y relacionar datos en Falcon Next-Gen SIEM usando CQL, dashboards y CPS para detectar comportamientos sospechosos a traves de multiples fuentes.

## Preguntas de practica

1. Que busca evaluar el dominio Querying and Analytics en el CCSA?
2. Por que CPS ayuda a hacer consultas agnosticas a la fuente de datos?
3. Cual es la diferencia entre solo ejecutar una query y realmente hacer analisis?
4. Cuando te conviene pivotear entre datasets de host, red y email?
5. Por que los dashboards y visual summaries son utiles para un analista SIEM?
6. Que funcion usarias para contar eventos por usuario?
7. Que harias si una busqueda devuelve demasiado ruido?
8. Por que `lookup` puede ayudar durante una investigacion?

## Respuestas rapidas

1. Evalua si puedes construir queries, interpretar resultados y correlacionar datos utiles para detectar actividad sospechosa.
2. Porque estandariza nombres y estructura de campos, lo que facilita reutilizar busquedas entre varias fuentes.
3. Ejecutar una query solo trae datos; analizar implica interpretar contexto, identificar patrones y decidir si hay riesgo.
4. Cuando un mismo incidente deja huellas en varias fuentes y necesitas reconstruir mejor el comportamiento.
5. Porque ayudan a ver tendencias, anomalias y volumen de actividad mas rapido que leyendo eventos uno por uno.
6. `groupBy(user.name)` o una agregacion equivalente.
7. Acotar tiempo, filtrar por campos relevantes, agregar resultados y excluir ruido conocido solo con contexto.
8. Porque enriquece eventos con informacion externa como criticidad de activos, owners, allowlists o blocklists.

## Relacionadas

- [[Chuleta CQL para CCSA - CrowdStrike LogScale]]
- [[defineTable() y match() en SIEM - CrowdStrike LogScale]]
- [[join() en SIEM - CrowdStrike LogScale]]
- [[CPS - CrowdStrike Parsing Standard para CCSA]]
- [[Neighbor en SIEM - CrowdStrike LogScale]]
- [[correlate() en SIEM - CrowdStrike LogScale]]
- [[groupBy() en SIEM - CrowdStrike LogScale]]
- [[CCSA - Detection Logic and Alert Analysis]]
- [[Mapa CCSA CrowdStrike]]

## Fuentes

- CCSA Exam Guide: https://assets.crowdstrike.com/is/content/crowdstrikeinc/crowdstrike-university-ccsa-certification-exam-guidepdf
- CPS Docs: https://library.humio.com/logscale-parsing-standard/pasta.html
- Data Ingestion Overview: https://library.humio.com/training/training-ingestion-overview.html
