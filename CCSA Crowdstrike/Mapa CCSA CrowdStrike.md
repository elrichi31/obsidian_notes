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
- [[Chuleta CQL para CCSA - CrowdStrike LogScale]]
- [[defineTable() y match() en SIEM - CrowdStrike LogScale]]
- [[CPS - CrowdStrike Parsing Standard para CCSA]]
- [[MITRE ATT&CK para CCSA]]
- [[Tipos de deteccion y metadata de alertas - CCSA]]
- [[Falcon Next-Gen SIEM - Workbench Case Management y Fusion SOAR]]
- [[Preguntas tipo escenario - CCSA CrowdStrike]]
- [[Neighbor en SIEM - CrowdStrike LogScale]]
- [[correlate() en SIEM - CrowdStrike LogScale]]
- [[groupBy() en SIEM - CrowdStrike LogScale]]

## Como se relacionan

- [[CCSA - Querying and Analytics]] es la base para leer, buscar y pivotear datos.
- [[CCSA - Detection Logic and Alert Analysis]] te ayuda a entender por que una alerta importa.
- [[CCSA - Incident Investigation]] conecta alertas, observables y respuesta.
- [[CCSA - Reporting and Communication]] convierte la investigacion en accion y comunicacion clara.
- [[Chuleta CQL para CCSA - CrowdStrike LogScale]] concentra funciones y decisiones practicas de CQL.
- [[defineTable() y match() en SIEM - CrowdStrike LogScale]] explica pivots con tablas temporales y joins practicos.
- [[CPS - CrowdStrike Parsing Standard para CCSA]] explica normalizacion de campos entre fuentes.
- [[MITRE ATT&CK para CCSA]] resume tacticas, tecnicas y como usarlas en alertas.
- [[Tipos de deteccion y metadata de alertas - CCSA]] separa first-party, third-party passthrough y correlation rule detections.
- [[Falcon Next-Gen SIEM - Workbench Case Management y Fusion SOAR]] conecta investigacion, documentacion y automatizacion.
- [[Preguntas tipo escenario - CCSA CrowdStrike]] sirve para practicar como podria venir el examen.
- [[Neighbor en SIEM - CrowdStrike LogScale]] sirve para mirar el evento anterior o siguiente dentro de una secuencia.
- [[correlate() en SIEM - CrowdStrike LogScale]] sirve para encontrar patrones multi-evento con links, `within` y `sequence`.
- [[groupBy() en SIEM - CrowdStrike LogScale]] sirve para agrupar y resumir datos con agregaciones.
- Idea rapida: `neighbor()` = contexto inmediato.
- Idea rapida: `correlate()` = historia completa entre varios eventos.
- Idea rapida: `groupBy()` = resumen y conteo.
- Idea rapida: `defineTable()` + `match()` = lista temporal y pivot.
- Idea rapida: CPS = campos normalizados.
- Idea rapida: MITRE = contexto de ataque.
- Idea rapida: Fusion SOAR = automatizacion de respuesta.

## Temas que podemos profundizar despues

- mas ejemplos de `join()`
- mas ejemplos de `regex()`
- mas ejemplos de `parseJson()`
- mas ejemplos de `lookup`
- mas ejemplos de `session`
- mas ejemplos de `bucket()`
- simulacro de 60 preguntas
- laboratorio de queries por escenario

## Consejo de estudio

Cuando agreguemos una nota nueva, la conectamos siempre con:

- esta nota indice
- al menos otra nota relacionada
- una diferencia corta tipo examen

## Ruta rapida de repaso antes del examen

1. [[CCSA - Temario oficial y plan de estudio]]
2. [[Chuleta CQL para CCSA - CrowdStrike LogScale]]
3. [[defineTable() y match() en SIEM - CrowdStrike LogScale]]
4. [[Tipos de deteccion y metadata de alertas - CCSA]]
5. [[MITRE ATT&CK para CCSA]]
6. [[Falcon Next-Gen SIEM - Workbench Case Management y Fusion SOAR]]
7. [[Preguntas tipo escenario - CCSA CrowdStrike]]
