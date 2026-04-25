---
tags:
  - ccsa
  - crowdstrike
  - siem
  - incident-response
  - investigation
---

# CCSA - Incident Investigation

> [!info] Dominio oficial
> Este tema cubre el dominio **Incident Investigation** del CCSA.

## Que entra en este tema

Segun el Exam Guide, aqui te pueden pedir:

- reconstruir la cadena de eventos usando varias fuentes
- identificar lateral movement, persistence y privilege escalation
- pivotear entre observables
- evaluar severidad y alcance
- recomendar acciones de respuesta
- aprovechar Fusion SOAR existente
- interpretar IOCs
- usar contexto como geolocalizacion, IP reputation o TTPs
- identificar fuentes de datos y retencion

## La mentalidad correcta

> [!important] Idea clave
> Investigar no es solo leer una alerta. Es **armar la historia completa**: que paso, cuando paso, a quien afecto, que tan grave es y que deberia hacerse despues.

## Flujo mental de investigacion

### 1. Entender el disparador

- que detecto?
- de que tipo de alerta se trata?
- cual parece ser el observable principal?

### 2. Reconstruir la historia

- que paso antes?
- que paso despues?
- hay otros eventos con el mismo usuario, IP, host o proceso?

Aqui se relaciona bien con:

- [[Neighbor en SIEM - CrowdStrike LogScale]]
- [[correlate() en SIEM - CrowdStrike LogScale]]

### 3. Medir alcance

- afecto a un solo host o a varios?
- se ve movimiento lateral?
- hay indicios de persistencia?
- hubo escalamiento de privilegios?

### 4. Tomar una postura

- es un evento benigno, sospechoso o claramente malicioso?
- que respuesta recomendarias?
- que automatizacion ya existe en Fusion SOAR?

## Cosas que suenan mucho a examen

- **Pivot** entre IP, usuario, host e indicador
- **IOC** no es solo un hash; puede ser IP, dominio, artefacto o patron observable
- **Contexto** como reputacion IP o geolocalizacion no reemplaza la evidencia, pero la fortalece
- **Retencion** importa porque no siempre tendras todo el historico disponible

## Mini resumen tipo examen

> [!success] Respuesta rapida
> Incident Investigation evalua si puedes reconstruir la cadena de eventos, pivotear entre observables, medir alcance y gravedad, interpretar IOCs y recomendar contencion o remediacion usando la evidencia correlacionada en Falcon Next-Gen SIEM.

## Preguntas de practica

1. Cual es el objetivo principal de reconstruir la cadena de eventos en una investigacion?
2. Que observables suelen servir para pivotear durante una investigacion SIEM?
3. Por que geolocalizacion o IP reputation ayudan pero no deberian ser la unica base de una conclusion?
4. Que preguntas te ayudan a medir el alcance real de un incidente?
5. Cuando tiene sentido aprovechar un workflow existente de Fusion SOAR?

## Respuestas rapidas

1. Entender la historia completa del incidente y no quedarte solo con el evento que disparo la alerta.
2. IP, usuario, host, dominio, hash, proceso u otros indicadores relacionados.
3. Porque son contexto adicional; la conclusion fuerte debe salir de evidencia tecnica y correlacionada.
4. A cuantos activos afecta, si hubo movimiento lateral, persistencia, escalamiento y que tan sensible era el objetivo.
5. Cuando ya existe una automatizacion aprobada para contener, notificar o remediar un tipo de actividad maliciosa.

## Relacionadas

- [[Neighbor en SIEM - CrowdStrike LogScale]]
- [[correlate() en SIEM - CrowdStrike LogScale]]
- [[groupBy() en SIEM - CrowdStrike LogScale]]
- [[CCSA - Detection Logic and Alert Analysis]]
- [[CCSA - Reporting and Communication]]
- [[Mapa CCSA CrowdStrike]]

## Fuentes

- CCSA Exam Guide: https://www.crowdstrike.com/crowdstrike-university-ccsa-certification-exam-guide.pdf
- Training Catalog - SIEM 108 / SIEM 109 / SIEM 211: https://www.crowdstrike.com/content/dam/crowdstrike/marketing/en-us/documents/pdfs/crowdstrike-university/CSU-Training-Catalog.pdf
- Incident Management overview: https://www.crowdstrike.com/en-us/platform/next-gen-siem/incident-management/
