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

## Como conecta con tus otras notas

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

## Respuestas rapidas

1. Evalua si puedes construir queries, interpretar resultados y correlacionar datos utiles para detectar actividad sospechosa.
2. Porque estandariza nombres y estructura de campos, lo que facilita reutilizar busquedas entre varias fuentes.
3. Ejecutar una query solo trae datos; analizar implica interpretar contexto, identificar patrones y decidir si hay riesgo.
4. Cuando un mismo incidente deja huellas en varias fuentes y necesitas reconstruir mejor el comportamiento.
5. Porque ayudan a ver tendencias, anomalias y volumen de actividad mas rapido que leyendo eventos uno por uno.

## Relacionadas

- [[Neighbor en SIEM - CrowdStrike LogScale]]
- [[correlate() en SIEM - CrowdStrike LogScale]]
- [[groupBy() en SIEM - CrowdStrike LogScale]]
- [[CCSA - Detection Logic and Alert Analysis]]
- [[Mapa CCSA CrowdStrike]]

## Fuentes

- CCSA Exam Guide: https://www.crowdstrike.com/crowdstrike-university-ccsa-certification-exam-guide.pdf
- CPS Docs: https://library.humio.com/logscale-parsing-standard/pasta.html
- Data Ingestion Overview: https://library.humio.com/training/training-ingestion-overview.html
