---
tags:
  - ccsa
  - crowdstrike
  - siem
  - detections
  - mitre
  - alerts
---

# CCSA - Detection Logic and Alert Analysis

> [!info] Dominio oficial
> Este tema cubre el dominio **Detection Logic and Alert Analysis** del CCSA.

## Que entra en este tema

Segun el Exam Guide, aqui te pueden pedir:

- explicar para que sirven las correlation rules
- diferenciar tipos de deteccion
- aplicar MITRE ATT&CK dentro de Falcon Next-Gen SIEM
- distinguir falsos positivos de detecciones legitimas
- entender metadata como severidad, tactica y confidence

## Lo mas importante para examen

### 1. Tipos de deteccion

Debes poder diferenciar al menos:

- **first-party detections**
- **third-party passthrough detections**
- **correlation rule detections**

> [!tip] Regla mental
> No todas las alertas nacen igual. Algunas vienen nativas de CrowdStrike, otras pasan desde terceros y otras se generan por logica de correlacion.

### 2. Correlation rules

Las correlation rules sirven para:

- detectar patrones multi-evento
- combinar contexto de diferentes fuentes
- reducir dependencia de eventos aislados
- generar detecciones con mayor significado operativo

Esto conecta directo con [[correlate() en SIEM - CrowdStrike LogScale]].

### 3. MITRE ATT&CK

No necesitas memorizar todo ATT&CK, pero si:

- entender tacticas y tecnicas
- ubicar una deteccion en una fase del ataque
- usar MITRE como contexto para priorizar e investigar

### 4. Metadata del alert

Debes leer bien:

- severidad
- tactic
- confidence
- contexto del evento

Eso ayuda a decidir:

- que tan urgente es
- si parece falso positivo
- si requiere pivot inmediato

## Como pensar este dominio

> [!important] Idea clave
> Este dominio no te pregunta solo "que detecto la plataforma", sino **si entiendes por que alerto, que tipo de alerta es y que tan confiable o prioritaria parece**.

## Senales que te pueden ayudar a separar falso positivo de alerta valida

- contexto coherente con el comportamiento del entorno
- multiples indicadores apuntando a la misma historia
- tacticas o TTPs que encajan con la actividad
- metadata de alta prioridad o alto confidence
- evidencia correlacionada entre varias fuentes

## Mini resumen tipo examen

> [!success] Respuesta rapida
> Detection Logic and Alert Analysis evalua si entiendes de donde salen las detecciones, como priorizarlas, como usar MITRE ATT&CK para dar contexto y como separar un falso positivo de una alerta legitima usando metadata y evidencia.

## Preguntas de practica

1. Cual es la diferencia general entre una deteccion first-party y una correlation rule detection?
2. Para que sirve MITRE ATT&CK dentro del analisis de alertas?
3. Que metadata del alert deberias revisar para priorizar una investigacion?
4. Por que una correlation rule puede aportar mas contexto que un solo evento?
5. Que elementos te ayudan a decidir si una alerta puede ser falso positivo?

## Respuestas rapidas

1. La first-party nace de capacidades nativas de CrowdStrike; la correlation rule detection nace de una regla que relaciona eventos o condiciones.
2. Sirve para ubicar la actividad dentro de tacticas y tecnicas del atacante y asi entender mejor el contexto.
3. Severidad, tactic, confidence y la evidencia relacionada.
4. Porque combina varios eventos y relaciones, no solo una observacion aislada.
5. El contexto del entorno, la calidad de la evidencia, la consistencia con TTPs y la presencia o ausencia de correlacion adicional.

## Relacionadas

- [[correlate() en SIEM - CrowdStrike LogScale]]
- [[CCSA - Querying and Analytics]]
- [[CCSA - Incident Investigation]]
- [[Mapa CCSA CrowdStrike]]

## Fuentes

- CCSA Exam Guide: https://www.crowdstrike.com/crowdstrike-university-ccsa-certification-exam-guide.pdf
- Training Catalog - SIEM 107: https://www.crowdstrike.com/content/dam/crowdstrike/marketing/en-us/documents/pdfs/crowdstrike-university/CSU-Training-Catalog.pdf
