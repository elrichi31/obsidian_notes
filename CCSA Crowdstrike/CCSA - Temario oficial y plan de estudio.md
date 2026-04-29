---
tags:
  - ccsa
  - crowdstrike
  - siem
  - exam
  - study-guide
---

# CCSA - Temario oficial y plan de estudio

> [!info] Estado actual
> Esta nota esta basada en el **CCSA Certification Exam Guide** de **enero de 2026** y en el **Training Catalog** de CrowdStrike actualizado en **febrero de 2026**.

## Resumen rapido del examen

- Certificacion: **CrowdStrike Certified SIEM Analyst (CCSA)**
- Plataforma: **CrowdStrike Falcon Next-Gen SIEM**
- Formato: **60 preguntas multiple choice**
- Duracion: **90 minutos**
- Perfil objetivo: analista SIEM, SOC analyst, threat detection analyst, incident response analyst
- Experiencia recomendada: **6 meses** usando Falcon / Falcon Next-Gen SIEM

## Los 4 dominios oficiales

1. [[CCSA - Querying and Analytics]]
2. [[CCSA - Detection Logic and Alert Analysis]]
3. [[CCSA - Incident Investigation]]
4. [[CCSA - Reporting and Communication]]

## Notas complementarias importantes

- [[Chuleta CQL para CCSA - CrowdStrike LogScale]]
- [[defineTable() y match() en SIEM - CrowdStrike LogScale]]
- [[join() en SIEM - CrowdStrike LogScale]]
- [[CPS - CrowdStrike Parsing Standard para CCSA]]
- [[Tipos de deteccion y metadata de alertas - CCSA]]
- [[MITRE ATT&CK para CCSA]]
- [[Falcon Next-Gen SIEM - Workbench Case Management y Fusion SOAR]]
- [[Preguntas tipo escenario - CCSA CrowdStrike]]
- [[Lecciones del simulacro CCSA - CrowdStrike]]
- [[Como leer correlation rules complejas - CCSA CrowdStrike]]

## Lo que CrowdStrike espera que sepas hacer

- construir busquedas en **CQL**
- interpretar resultados y detectar comportamiento sospechoso
- correlacionar datos de varias fuentes
- entender tipos de deteccion
- usar MITRE ATT&CK a nivel practico
- investigar un incidente y reconstruir la cadena de eventos
- documentar hallazgos y comunicar resultados

## Cursos que parecen mas utiles para preparar el examen

> [!tip] Nota
> El Exam Guide remite a los cursos de SIEM Analyst en CrowdStrike University. Con base en el catalogo actual, estos son los que mejor se alinean con el examen.

- `SIEM 100` - Next-Gen SIEM Fundamentals
- `SIEM 105` - Falcon Next-Gen SIEM Architecture and Core Concepts
- `SIEM 106` - Case Management Fundamentals
- `SIEM 107` - Creating Correlation Rules with Falcon Next-Gen SIEM
- `SIEM 108` - Managing Automation Workflows with Fusion SOAR in Falcon Next-Gen SIEM
- `SIEM 109` - Workbench Fundamentals
- `CQL 201` - Designing and Optimizing CQL Queries
- `SIEM 211` - Incident Response and Investigation in Falcon Next-Gen SIEM

## Estrategia de estudio recomendada

### Semana 1

- [[CCSA - Querying and Analytics]]
- [[Chuleta CQL para CCSA - CrowdStrike LogScale]]
- [[defineTable() y match() en SIEM - CrowdStrike LogScale]]
- [[join() en SIEM - CrowdStrike LogScale]]
- [[CPS - CrowdStrike Parsing Standard para CCSA]]
- [[Neighbor en SIEM - CrowdStrike LogScale]]
- [[correlate() en SIEM - CrowdStrike LogScale]]
- [[groupBy() en SIEM - CrowdStrike LogScale]]

### Semana 2

- [[CCSA - Detection Logic and Alert Analysis]]
- [[Tipos de deteccion y metadata de alertas - CCSA]]
- [[MITRE ATT&CK para CCSA]]
- repasar MITRE ATT&CK, severidad, tacticas y confidence

### Semana 3

- [[CCSA - Incident Investigation]]
- [[Falcon Next-Gen SIEM - Workbench Case Management y Fusion SOAR]]
- practicar pivots entre IP, usuario, host y proceso

### Semana 4

- [[CCSA - Reporting and Communication]]
- [[Preguntas tipo escenario - CCSA CrowdStrike]]
- [[Lecciones del simulacro CCSA - CrowdStrike]]
- [[Como leer correlation rules complejas - CCSA CrowdStrike]]
- responder preguntas de autoevaluacion de todas las notas

## Ideas clave para memorizar

- `Querying` = encontrar y entender datos
- `Detection Logic` = entender por que algo alerto
- `Investigation` = reconstruir la historia del incidente
- `Reporting` = resumir y comunicar con claridad
- `CPS` = consultar campos normalizados entre fuentes
- `MITRE` = clasificar comportamiento por tactica y tecnica
- `Fusion SOAR` = automatizar respuesta aprobada
- `Workbench` = revisar e investigar actividad relevante
- `Case Management` = documentar y coordinar el caso
- `join()` = combinar dos busquedas por una llave comun
- `mode=inner` = solo coincidencias entre ambos lados
- `mode=left` = conserva la query principal y agrega contexto si hay match
- `strict=false` en `ioc:lookup()` = enriquece sin descartar eventos sin match
- rule compleja = baseline -> filtros -> exclusions -> `NOT match()` -> `groupBy()` -> threshold

## Revision de huecos en las notas

Ya esta bien cubierto:

- dominios oficiales del CCSA
- CQL basico y mentalidad de tuberia
- `groupBy()`, `neighbor()`, `correlate()`, `defineTable()` + `match()` y `join()`
- CPS y campos normalizados
- tipos de deteccion, metadata de alertas y MITRE ATT&CK
- Workbench, Case Management y Fusion SOAR
- preguntas tipo escenario
- lecciones del simulacro y lectura de correlation rules complejas

Temas que conviene convertir en notas propias despues:

- `timeChart()` y `bucket()` para tendencias y visual summaries
- `regex()`, `parseJson()`, `parseCsv()` y extraccion de campos
- `readFile()`, `match()` con lookup files e `ioc:lookup()`
- `session()`, `series()` y patrones de autenticacion
- data sources, retencion y problemas de timestamp/ingestion
- dashboards, prebuilt scripts y como elegir visualizaciones
- geolocalizacion, reputacion IP, IOCs y contexto de amenaza
- criterios de respuesta/remediacion segun evidencia

## Como estudiar para preguntas de escenario

Cuando leas una pregunta larga, separala asi:

1. Cual es el observable principal?
2. Que tipo de deteccion es?
3. Que metadata importa?
4. Que pivot haria despues?
5. Que evidencia confirmaria o descartaria?
6. Que respuesta o comunicacion corresponde?

## Relacionadas

- [[Mapa CCSA CrowdStrike]]
- [[CCSA - Querying and Analytics]]
- [[CCSA - Detection Logic and Alert Analysis]]
- [[CCSA - Incident Investigation]]
- [[CCSA - Reporting and Communication]]
- [[join() en SIEM - CrowdStrike LogScale]]
- [[Preguntas tipo escenario - CCSA CrowdStrike]]
- [[Lecciones del simulacro CCSA - CrowdStrike]]
- [[Como leer correlation rules complejas - CCSA CrowdStrike]]

## Fuentes

- CCSA Exam Guide: https://assets.crowdstrike.com/is/content/crowdstrikeinc/crowdstrike-university-ccsa-certification-exam-guidepdf
- Certification Guide: https://www.crowdstrike.com/wp-content/uploads/2020/11/cfcp-certification-guide.pdf
- Training Catalog: https://www.crowdstrike.com/content/dam/crowdstrike/marketing/en-us/documents/pdfs/crowdstrike-university/CSU-Training-Catalog.pdf
