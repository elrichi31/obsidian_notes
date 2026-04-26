---
tags:
  - ccsa
  - crowdstrike
  - siem
  - alerts
  - detections
  - metadata
---

# Tipos de deteccion y metadata de alertas - CCSA

> [!info] Idea clave
> Para el CCSA debes entender **de donde viene una deteccion**, **por que alerto** y **como priorizarla** usando metadata y evidencia.

## Tipos de deteccion

| Tipo | Que significa | Como pensarlo |
|---|---|---|
| First-party detection | Deteccion generada por capacidades nativas de CrowdStrike | "CrowdStrike lo detecto directamente" |
| Third-party passthrough detection | Alerta originada en una herramienta externa e ingerida en Falcon Next-Gen SIEM | "La alerta vino de otro producto" |
| Correlation rule detection | Deteccion creada por una regla que relaciona eventos/condiciones | "La logica combino senales" |

## Por que importa el origen

El origen de la deteccion afecta:

- que evidencia revisar primero
- que tan enriquecido viene el contexto
- si debes validar con la fuente original
- como ajustar la logica si hay falsos positivos
- que equipo podria ser owner de la remediacion

## Metadata clave

| Metadata | Que te dice |
|---|---|
| Severity | Impacto o peligro potencial |
| Confidence | Que tan confiable parece la deteccion |
| Priority | Urgencia operacional combinando riesgo y contexto |
| Tactic | Fase del ataque segun MITRE ATT&CK |
| Technique | Metodo especifico observado |
| Source | De donde vino la alerta o evento |

> [!warning] Confusion comun
> **Severity alta** no siempre significa **confidence alta**. Algo puede ser muy grave si fuera real, pero tener evidencia debil. Tambien puede haber evidencia fuerte de algo de impacto menor.

## Como priorizar una alerta

Una alerta sube de prioridad si:

- afecta activo critico
- involucra usuario privilegiado
- aparece en varias fuentes
- encaja con una cadena ATT&CK coherente
- tiene IOC conocido o reputacion mala
- muestra actividad antes y despues que confirma la historia

Una alerta puede bajar de prioridad si:

- coincide con actividad aprobada
- viene de herramienta administrativa conocida
- afecta ambiente de laboratorio
- no hay evidencia de continuacion
- el usuario/host/contexto explica el comportamiento

## Como analizar un falso positivo

Preguntas utiles:

- El usuario suele hacer esto?
- El host es servidor, workstation o laboratorio?
- La herramienta esta permitida?
- La hora tiene sentido?
- Hay cambio reciente, despliegue o mantenimiento?
- Hay eventos relacionados antes o despues?
- Otras fuentes confirman o contradicen la alerta?

## Escenarios tipo examen

1. Una alerta third-party llega con poca evidencia. Que haces?
   - Validar en la fuente original y buscar contexto adicional en Falcon Next-Gen SIEM.

2. Una correlation rule detecta login imposible + descarga masiva. Que indica?
   - Que varias senales juntas elevan el valor analitico de la alerta.

3. Una alerta tiene severity alta pero confidence baja. Como la tratas?
   - No la ignoras; investigas con contexto para confirmar o descartar.

4. Una deteccion mapea a Credential Access. Que pivots pueden servir?
   - Usuario, host, procesos, acceso a LSASS, eventos de autenticacion, lateral movement posterior.

## Respuesta corta tipo examen

> [!success] Respuesta lista para decir
> En Falcon Next-Gen SIEM, analizar una alerta implica identificar su origen, revisar metadata como severity, confidence, priority y MITRE mapping, y validar la evidencia con contexto para decidir si es falso positivo, sospechosa o incidente real.

## Relacionadas

- [[CCSA - Detection Logic and Alert Analysis]]
- [[MITRE ATT&CK para CCSA]]
- [[CCSA - Incident Investigation]]
- [[Mapa CCSA CrowdStrike]]

## Fuentes

- CCSA Exam Guide: https://assets.crowdstrike.com/is/content/crowdstrikeinc/crowdstrike-university-ccsa-certification-exam-guidepdf
- MITRE ATT&CK Enterprise Matrix: https://attack.mitre.org/matrices/enterprise/
