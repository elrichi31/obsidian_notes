---
tags:
  - ccsa
  - crowdstrike
  - siem
  - practice
  - exam
---

# Preguntas tipo escenario - CCSA CrowdStrike

> [!info] Como usar esta nota
> Tapate las respuestas, intenta decidir como analista SOC y luego compara. El examen probablemente mezcla conceptos, no los pregunta siempre como definiciones aisladas.

## Querying and Analytics

### 1. Login sospechoso por usuario

Ves muchos intentos fallidos de autenticacion desde varias IPs contra un mismo usuario. Que query mental conviene?

Respuesta:

- agrupar por `user.name` y `source.ip`
- revisar tendencia en el tiempo
- pivotear a eventos exitosos posteriores
- buscar actividad del mismo usuario en endpoint/cloud

### 2. Mucho ruido de eventos

Una busqueda devuelve miles de eventos y no puedes ver patron. Que haces?

Respuesta:

- filtrar por tiempo
- usar campos relevantes
- agregar con `groupBy()`
- crear visualizacion temporal con `timeChart()` o `bucket()`
- excluir actividad conocida solo si tienes contexto

### 3. Fuente con campos distintos

Dos vendors tienen nombres diferentes para IP origen. Que concepto ayuda?

Respuesta:

- CPS, porque normaliza campos como `source.ip`.

### 3.1 Otros usuarios en hosts tocados por un usuario sospechoso

`jthompson` disparo detecciones de credential access. Necesitas saber que otros usuarios estuvieron activos en los mismos hosts que `jthompson`.

Respuesta:

- usar `defineTable()` para guardar los `host.name` donde aparecio `jthompson`
- usar `match()` para buscar eventos en esos hosts
- agrupar por `user.name`
- excluir `jthompson`

Clave mental:

```text
jthompson -> hosts -> otros usuarios
```

No matchees por `user.name` y `host.name` a la vez si lo que quieres es encontrar **otros usuarios**.

### 3.2 neighbor mal usado

Una query filtra logins exitosos y luego ejecuta `neighbor()` directamente sin `sort()`, `head()`, `bucket()` o `groupBy()`. Que problema hay?

Respuesta:

- `neighbor()` necesita una secuencia ordenada
- sin orden claro, "evento anterior" no tiene significado confiable
- primero debes establecer orden temporal y, si aplica, validar que sea el mismo usuario/host

## Detection Logic and Alert Analysis

### 4. Alerta de origen externo

Una alerta viene de una herramienta third-party y aparece en Falcon Next-Gen SIEM. Que tipo es?

Respuesta:

- third-party passthrough detection, salvo que haya sido generada por una correlation rule dentro de Falcon Next-Gen SIEM.

### 5. Severity alta, confidence baja

Que haces con una alerta de alto impacto potencial pero baja confianza?

Respuesta:

- investigarla con contexto
- buscar evidencia correlacionada
- no asumir confirmacion solo por severity
- no descartarla solo por confidence baja

### 6. MITRE Discovery

Una alerta mapea a Discovery. Que buscas despues?

Respuesta:

- lateral movement
- credential access
- privilege escalation
- conexiones internas inusuales
- cambios de comportamiento del usuario o host

## Incident Investigation

### 7. Posible lateral movement

Que evidencia buscarias?

Respuesta:

- logins remotos
- RDP, SMB, WinRM, SSH segun entorno
- origen y destino internos
- uso de credenciales privilegiadas
- ejecucion remota
- multiples hosts afectados

### 8. Posible persistence

Que evidencia buscarias?

Respuesta:

- servicios nuevos
- scheduled tasks
- run keys
- nuevos usuarios o claves
- binarios en rutas sospechosas
- procesos que sobreviven reinicio o vuelven a ejecutarse

### 9. IOC con mala reputacion

Una IP tiene mala reputacion. Es suficiente para confirmar incidente?

Respuesta:

- No. Es contexto fuerte, pero necesitas evidencia del evento, host, usuario, actividad relacionada y posible impacto.

## Reporting and Communication

### 10. Resumen ejecutivo

Que no puede faltar?

Respuesta:

- que paso
- impacto
- alcance
- evidencia principal
- acciones tomadas
- recomendacion o siguiente paso

### 11. Caso para otro analista

Que debe quedar documentado?

Respuesta:

- timeline
- observables
- queries o evidencia
- decision analitica
- tareas pendientes
- owner y estado

## Preguntas rapidas de memoria

1. `groupBy()` sirve para?
2. `neighbor()` sirve para?
3. `correlate()` sirve para?
4. CPS sirve para?
5. MITRE ATT&CK sirve para?
6. Case Management sirve para?
7. Fusion SOAR sirve para?
8. `defineTable()` sirve para?
9. `match()` sirve para?

## Respuestas rapidas de memoria

1. Agrupar y resumir eventos.
2. Ver evento anterior o siguiente dentro de una secuencia.
3. Encontrar patrones multi-evento relacionados por links y constraints.
4. Normalizar campos entre fuentes.
5. Dar contexto tactico/tecnico a comportamientos observados.
6. Documentar, coordinar y dar seguimiento a investigaciones.
7. Automatizar y orquestar acciones de respuesta aprobadas.
8. Crear una tabla temporal desde una subquery.
9. Comparar eventos contra una tabla temporal o lookup para filtrar/enriquecer.

## Relacionadas

- [[CCSA - Temario oficial y plan de estudio]]
- [[CCSA - Querying and Analytics]]
- [[CCSA - Detection Logic and Alert Analysis]]
- [[CCSA - Incident Investigation]]
- [[CCSA - Reporting and Communication]]
- [[defineTable() y match() en SIEM - CrowdStrike LogScale]]
- [[Mapa CCSA CrowdStrike]]
