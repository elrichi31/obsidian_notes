---
tags:
  - ccsa
  - crowdstrike
  - siem
  - workbench
  - case-management
  - fusion-soar
---

# Falcon Next-Gen SIEM - Workbench Case Management y Fusion SOAR

> [!info] Idea clave
> Para el CCSA, estas capacidades importan porque conectan la investigacion con operacion real: revisar alertas, documentar casos, colaborar y automatizar respuesta.

## Workbench

Piensa en Workbench como el lugar donde el analista revisa actividad relevante y empieza a trabajar una investigacion.

Puede ayudarte a:

- ver alertas o detecciones priorizadas
- revisar contexto de seguridad
- entrar al detalle de una investigacion
- conectar eventos relacionados
- pasar de alerta a accion

## Case Management

Case Management sirve para documentar y coordinar la investigacion.

Un caso deberia capturar:

- resumen del hallazgo
- evidencia principal
- timeline
- activos y usuarios afectados
- IOCs
- severidad/prioridad
- decisiones tomadas
- acciones de contencion o remediacion
- estado final

> [!tip] Regla mental
> Workbench ayuda a investigar; Case Management ayuda a dejar trazabilidad y colaboracion.

## Fusion SOAR

Fusion SOAR permite automatizar workflows de respuesta y orquestacion.

Casos comunes:

- notificar a un canal o equipo
- crear o actualizar caso
- enriquecer indicadores
- contener host
- bloquear indicador
- asignar tareas
- escalar segun severidad

## Cuando usar automatizacion

Usa un workflow cuando:

- el proceso esta aprobado
- la accion es repetible
- el riesgo esta entendido
- hay suficiente confianza
- quieres reducir tiempo de respuesta

Ten cuidado cuando:

- la evidencia es debil
- la accion puede interrumpir negocio
- el activo es critico
- el playbook no esta validado

## Como lo pueden preguntar

1. Tienes una alerta confirmada con proceso malicioso en endpoint. Que ayuda a responder rapido?
   - Un workflow de Fusion SOAR aprobado para contencion/notificacion.

2. Necesitas dejar evidencia y seguimiento para otros analistas. Que usas?
   - Case Management.

3. Quieres revisar el contexto inicial de detecciones y priorizacion. Que concepto aparece?
   - Workbench.

4. Una accion automatica podria cortar un servicio critico. Que haces?
   - Validar contexto, seguir aprobaciones y evitar automatizacion riesgosa sin confirmacion.

## Mini resumen tipo examen

> [!success] Respuesta rapida
> Workbench ayuda a revisar e investigar actividad relevante, Case Management documenta y coordina la investigacion, y Fusion SOAR automatiza workflows de respuesta cuando hay logica aprobada y suficiente confianza.

## Relacionadas

- [[CCSA - Incident Investigation]]
- [[CCSA - Reporting and Communication]]
- [[Tipos de deteccion y metadata de alertas - CCSA]]
- [[Mapa CCSA CrowdStrike]]

## Fuentes

- CCSA Exam Guide: https://assets.crowdstrike.com/is/content/crowdstrikeinc/crowdstrike-university-ccsa-certification-exam-guidepdf
- CrowdStrike Incident Management: https://www.crowdstrike.com/en-us/platform/next-gen-siem/incident-management/
- Falcon Fusion SOAR: https://www.crowdstrike.com/en-us/platform/falcon-fusion-soar/
