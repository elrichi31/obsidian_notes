---
tags:
  - ccsa
  - crowdstrike
  - moc
  - index
---

# Mapa CCSA CrowdStrike

> [!info] Para que sirve esta nota
> Esta es tu nota indice para navegar rapido entre conceptos del examen y ver como se conectan entre si.

## Conceptos base

- [[CCSA - Temario oficial y plan de estudio]]
- [[CCSA - Querying and Analytics]]
- [[CCSA - Detection Logic and Alert Analysis]]
- [[CCSA - Incident Investigation]]
- [[CCSA - Reporting and Communication]]
- [[Neighbor en SIEM - CrowdStrike LogScale]]
- [[correlate() en SIEM - CrowdStrike LogScale]]
- [[groupBy() en SIEM - CrowdStrike LogScale]]

## Como se relacionan

- [[CCSA - Querying and Analytics]] es la base para leer, buscar y pivotear datos.
- [[CCSA - Detection Logic and Alert Analysis]] te ayuda a entender por que una alerta importa.
- [[CCSA - Incident Investigation]] conecta alertas, observables y respuesta.
- [[CCSA - Reporting and Communication]] convierte la investigacion en accion y comunicacion clara.
- [[Neighbor en SIEM - CrowdStrike LogScale]] sirve para mirar el evento anterior o siguiente dentro de una secuencia.
- [[correlate() en SIEM - CrowdStrike LogScale]] sirve para encontrar patrones multi-evento con links, `within` y `sequence`.
- [[groupBy() en SIEM - CrowdStrike LogScale]] sirve para agrupar y resumir datos con agregaciones.
- Idea rapida: `neighbor()` = contexto inmediato.
- Idea rapida: `correlate()` = historia completa entre varios eventos.
- Idea rapida: `groupBy()` = resumen y conteo.

## Temas que podemos agregar despues

- `join()`
- `regex()`
- `parseJson()`
- `lookup`
- `session`
- `bucket()`
- MITRE ATT&CK
- tipos de deteccion
- Fusion SOAR
- Workbench

## Consejo de estudio

Cuando agreguemos una nota nueva, la conectamos siempre con:

- esta nota indice
- al menos otra nota relacionada
- una diferencia corta tipo examen
