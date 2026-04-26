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
- que metadata trae: severity, confidence, tactic, technique?

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
- hay datos, sistemas o usuarios criticos involucrados?

### 4. Tomar una postura

- es un evento benigno, sospechoso o claramente malicioso?
- que respuesta recomendarias?
- que automatizacion ya existe en Fusion SOAR?

## Checklist de investigacion

| Pregunta | Para que sirve |
|---|---|
| Que paso primero? | Encontrar posible initial access o evento raiz |
| Que paso despues? | Ver progresion del ataque |
| Que usuario estuvo involucrado? | Medir identidad y privilegios |
| Que host fue afectado? | Medir alcance tecnico |
| Que procesos se ejecutaron? | Validar comportamiento |
| Que conexiones hubo? | Buscar C2, lateral movement o exfiltration |
| Hay otros hosts con el mismo IOC? | Medir expansion |
| Hay actividad normal que lo explique? | Reducir falsos positivos |

## Cosas que suenan mucho a examen

- **Pivot** entre IP, usuario, host e indicador
- **IOC** no es solo un hash; puede ser IP, dominio, artefacto o patron observable
- **Contexto** como reputacion IP o geolocalizacion no reemplaza la evidencia, pero la fortalece
- **Retencion** importa porque no siempre tendras todo el historico disponible

## Senales por tipo de actividad

| Actividad | Evidencia que buscarias |
|---|---|
| Lateral movement | RDP, SMB, WinRM, SSH, autenticaciones remotas, ejecucion remota |
| Persistence | servicios nuevos, scheduled tasks, run keys, usuarios nuevos |
| Privilege escalation | cambios de grupo, token elevado, uso de cuenta privilegiada |
| Credential access | acceso a LSASS, dumping, herramientas de credenciales, logins anormales |
| Command and Control | beaconing, dominios raros, conexiones recurrentes externas |
| Exfiltration | transferencias grandes, destinos externos, compresion previa |

## Retencion y fuentes de datos

Antes de concluir, piensa:

- la fuente necesaria esta conectada?
- el dato esta parseado correctamente?
- la ventana de retencion cubre el periodo investigado?
- hay gaps entre endpoint, identidad, red, cloud o email?

Si no hay datos, no significa automaticamente que no paso nada. Puede significar que no hay visibilidad suficiente.

## Mini resumen tipo examen

> [!success] Respuesta rapida
> Incident Investigation evalua si puedes reconstruir la cadena de eventos, pivotear entre observables, medir alcance y gravedad, interpretar IOCs y recomendar contencion o remediacion usando la evidencia correlacionada en Falcon Next-Gen SIEM.

## Preguntas de practica

1. Cual es el objetivo principal de reconstruir la cadena de eventos en una investigacion?
2. Que observables suelen servir para pivotear durante una investigacion SIEM?
3. Por que geolocalizacion o IP reputation ayudan pero no deberian ser la unica base de una conclusion?
4. Que preguntas te ayudan a medir el alcance real de un incidente?
5. Cuando tiene sentido aprovechar un workflow existente de Fusion SOAR?
6. Que evidencia buscarias para lateral movement?
7. Por que importa la retencion de datos?
8. Como diferenciarias IOC de evidencia confirmatoria?

## Respuestas rapidas

1. Entender la historia completa del incidente y no quedarte solo con el evento que disparo la alerta.
2. IP, usuario, host, dominio, hash, proceso u otros indicadores relacionados.
3. Porque son contexto adicional; la conclusion fuerte debe salir de evidencia tecnica y correlacionada.
4. A cuantos activos afecta, si hubo movimiento lateral, persistencia, escalamiento y que tan sensible era el objetivo.
5. Cuando ya existe una automatizacion aprobada para contener, notificar o remediar un tipo de actividad maliciosa.
6. Logins remotos, conexiones internas, uso de protocolos administrativos y ejecucion remota entre hosts.
7. Porque si el evento ocurrio fuera de la ventana disponible, no podras reconstruir toda la historia.
8. Un IOC es un observable; la evidencia confirmatoria muestra comportamiento, relacion temporal e impacto.

## Relacionadas

- [[Falcon Next-Gen SIEM - Workbench Case Management y Fusion SOAR]]
- [[Tipos de deteccion y metadata de alertas - CCSA]]
- [[MITRE ATT&CK para CCSA]]
- [[Neighbor en SIEM - CrowdStrike LogScale]]
- [[correlate() en SIEM - CrowdStrike LogScale]]
- [[groupBy() en SIEM - CrowdStrike LogScale]]
- [[CCSA - Detection Logic and Alert Analysis]]
- [[CCSA - Reporting and Communication]]
- [[Mapa CCSA CrowdStrike]]

## Fuentes

- CCSA Exam Guide: https://assets.crowdstrike.com/is/content/crowdstrikeinc/crowdstrike-university-ccsa-certification-exam-guidepdf
- Training Catalog - SIEM 108 / SIEM 109 / SIEM 211: https://www.crowdstrike.com/content/dam/crowdstrike/marketing/en-us/documents/pdfs/crowdstrike-university/CSU-Training-Catalog.pdf
- Incident Management overview: https://www.crowdstrike.com/en-us/platform/next-gen-siem/incident-management/
