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
- [[Neighbor en SIEM - CrowdStrike LogScale]]
- [[correlate() en SIEM - CrowdStrike LogScale]]
- [[groupBy() en SIEM - CrowdStrike LogScale]]

### Semana 2

- [[CCSA - Detection Logic and Alert Analysis]]
- repasar MITRE ATT&CK, severidad, tacticas y confidence

### Semana 3

- [[CCSA - Incident Investigation]]
- practicar pivots entre IP, usuario, host y proceso

### Semana 4

- [[CCSA - Reporting and Communication]]
- responder preguntas de autoevaluacion de todas las notas

## Ideas clave para memorizar

- `Querying` = encontrar y entender datos
- `Detection Logic` = entender por que algo alerto
- `Investigation` = reconstruir la historia del incidente
- `Reporting` = resumir y comunicar con claridad

## Relacionadas

- [[Mapa CCSA CrowdStrike]]
- [[CCSA - Querying and Analytics]]
- [[CCSA - Detection Logic and Alert Analysis]]
- [[CCSA - Incident Investigation]]
- [[CCSA - Reporting and Communication]]

## Fuentes

- CCSA Exam Guide: https://www.crowdstrike.com/crowdstrike-university-ccsa-certification-exam-guide.pdf
- Certification Guide: https://www.crowdstrike.com/wp-content/uploads/2020/11/cfcp-certification-guide.pdf
- Training Catalog: https://www.crowdstrike.com/content/dam/crowdstrike/marketing/en-us/documents/pdfs/crowdstrike-university/CSU-Training-Catalog.pdf
