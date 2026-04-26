---
tags:
  - ccsa
  - crowdstrike
  - siem
  - mitre
  - attack
  - detections
---

# MITRE ATT&CK para CCSA

> [!info] Idea clave
> En el CCSA, MITRE ATT&CK sirve para dar contexto a una alerta: ubicar el comportamiento en una **tactica** y una **tecnica**, priorizar mejor y orientar la investigacion.

## Tactica vs tecnica

| Concepto | Pregunta que responde |
|---|---|
| Tactica | Para que lo hace el atacante? |
| Tecnica | Como lo hace el atacante? |

Ejemplo:

- Tactica: **Persistence**
- Tecnica: crear tarea programada, servicio, run key u otro mecanismo persistente

## Tacticas que debes reconocer

No necesitas memorizar todo ATT&CK, pero si reconocer la historia:

- Initial Access: como entro
- Execution: como ejecuto codigo
- Persistence: como se mantiene
- Privilege Escalation: como sube privilegios
- Defense Evasion: como intenta ocultarse
- Credential Access: como obtiene credenciales
- Discovery: como enumera el entorno
- Lateral Movement: como se mueve a otros sistemas
- Collection: que intenta reunir
- Command and Control: como se comunica
- Exfiltration: como saca informacion
- Impact: como afecta disponibilidad o integridad

## Como usar MITRE en una alerta

Cuando veas una alerta, preguntate:

- que comportamiento se detecto?
- en que fase del ataque encaja?
- que tecnica podria explicar ese comportamiento?
- que evento anterior o posterior deberia buscar?
- que evidencia adicional confirmaria o bajaria la prioridad?

## Ejemplos tipo examen

| Senal | Tactica probable |
|---|---|
| Ejecucion de PowerShell sospechosa | Execution / Defense Evasion |
| Creacion de servicio persistente | Persistence |
| Dumping de LSASS o acceso a credenciales | Credential Access |
| Escaneo interno de hosts | Discovery |
| Uso de RDP/SMB/WinRM hacia otros hosts | Lateral Movement |
| Conexion a dominio raro recurrente | Command and Control |
| Transferencia grande hacia destino externo | Exfiltration |

## Como NO usar MITRE

MITRE no debe usarse como prueba unica.

MITRE ayuda a:

- clasificar
- priorizar
- explicar
- decidir siguientes pivots

MITRE no reemplaza:

- evidencia tecnica
- contexto del entorno
- validacion de falsos positivos
- analisis de alcance

## Preguntas tipo examen

1. Una alerta indica uso sospechoso de credenciales en varios hosts. Que tactica podria ser?
2. Para que sirve mapear una alerta a MITRE ATT&CK?
3. Si una deteccion tiene tactica `Discovery`, que buscarias despues?
4. Por que MITRE no basta para declarar un incidente confirmado?

## Respuestas rapidas

1. Podria indicar Lateral Movement, Credential Access o ambas segun la evidencia.
2. Para entender fase del ataque, priorizar e investigar con mejor contexto.
3. Actividad posterior como lateral movement, privilege escalation o acceso a datos sensibles.
4. Porque MITRE clasifica comportamiento; la conclusion requiere evidencia y contexto.

## Relacionadas

- [[CCSA - Detection Logic and Alert Analysis]]
- [[CCSA - Incident Investigation]]
- [[Tipos de deteccion y metadata de alertas - CCSA]]
- [[Mapa CCSA CrowdStrike]]

## Fuentes

- MITRE ATT&CK Enterprise Matrix: https://attack.mitre.org/matrices/enterprise/
- MITRE ATT&CK Tactics: https://attack.mitre.org/tactics/enterprise/
