---
tags:
  - ccsa
  - crowdstrike
  - siem
  - reporting
  - case-management
---

# CCSA - Reporting and Communication

> [!info] Dominio oficial
> Este tema cubre el dominio **Reporting and Communication** del CCSA.

## Que entra en este tema

Segun el Exam Guide, aqui te pueden pedir:

- documentar y resumir resultados de investigacion usando **Case Management**
- usar agregaciones y resumenes visuales para mostrar tendencias y anomalias

## Lo importante de este dominio

Este tema parece pequeno en el blueprint, pero en realidad es muy practico.

Un buen analista no solo investiga. Tambien debe:

- dejar el caso claro para otros analistas
- resumir que paso sin ruido innecesario
- comunicar impacto, alcance y acciones
- convertir datos tecnicos en algo entendible para liderazgo

## Case Management

Segun CrowdStrike, el enfoque de case management busca:

- centralizar el seguimiento del incidente
- alinear investigacion y colaboracion
- servir como fuente unica de verdad
- integrarse con Workbench y Fusion SOAR

## Agregaciones y visual summaries

Debes sentirte comodo con la idea de:

- contar eventos
- resumir volumen y tendencias
- mostrar picos o anomalias
- transformar muchos eventos en una historia clara

> [!tip] Regla mental
> El dashboard no reemplaza la investigacion, pero ayuda a explicar rapidamente **que esta pasando**.

## Como deberias comunicar un caso

Una estructura simple y util:

- que detectamos
- que evidencia lo respalda
- que alcance tiene
- que tan prioritario es
- que accion se recomienda

## Plantilla de resumen de caso

Puedes usar esta estructura mental:

```text
Resumen:
Evidencia principal:
Timeline:
Usuarios afectados:
Hosts afectados:
IOCs:
Impacto:
Acciones tomadas:
Recomendacion:
Estado:
```

## Resumen ejecutivo vs resumen tecnico

| Audiencia | Enfoque |
|---|---|
| Liderazgo | impacto, riesgo, alcance, decision necesaria |
| Analista SOC | evidencia, queries, pivots, timeline, IOCs |
| Equipo de IT | sistemas afectados, acciones concretas, prioridad |
| Compliance/legal | datos impactados, tiempos, trazabilidad, decisiones |

> [!tip] Idea de examen
> Comunicar bien no es contar todos los logs. Es convertir evidencia tecnica en una decision clara.

## Visualizaciones utiles

Para reportar, suelen servir:

- conteos por usuario
- conteos por host
- eventos por tiempo
- top IPs origen/destino
- volumen por tipo de evento
- comparacion antes/despues de una accion

Estas visualizaciones no prueban solas una conclusion, pero ayudan a explicar tendencia, alcance y anomalia.

## Mini resumen tipo examen

> [!success] Respuesta rapida
> Reporting and Communication evalua si puedes documentar la investigacion en Case Management y convertir resultados tecnicos en resumenes claros, tendencias y visualizaciones utiles para analistas y liderazgo.

## Preguntas de practica

1. Por que Case Management es importante dentro de Falcon Next-Gen SIEM?
2. Que ventaja tiene usar agregaciones en vez de revisar cada evento por separado?
3. Que elementos no deberian faltar en un resumen ejecutivo de incidente?
4. Cual es la diferencia entre investigar bien y comunicar bien?
5. Por que una visualizacion puede ayudar a detectar tendencias o anomalias mas rapido?
6. Que deberia incluir un timeline de investigacion?
7. Que diferencia hay entre un reporte para liderazgo y uno para analistas?
8. Por que conviene documentar decisiones tomadas?

## Respuestas rapidas

1. Porque centraliza evidencia, notas, seguimiento y colaboracion en un solo lugar.
2. Porque ayuda a ver volumen, tendencia y patrones sin perder tiempo en cada evento individual.
3. Hallazgo, evidencia, alcance, prioridad e indicacion de respuesta o remediacion.
4. Investigar bien encuentra la verdad tecnica; comunicar bien hace esa verdad util para decidir.
5. Porque resume grandes cantidades de datos en una forma facil de interpretar.
6. Eventos clave en orden, hora, fuente, observable y significado analitico.
7. Liderazgo necesita impacto y decision; analistas necesitan evidencia tecnica reproducible.
8. Porque deja trazabilidad, facilita handoff y explica por que se escalo, cerro o remedio un caso.

## Relacionadas

- [[Falcon Next-Gen SIEM - Workbench Case Management y Fusion SOAR]]
- [[Preguntas tipo escenario - CCSA CrowdStrike]]
- [[CCSA - Incident Investigation]]
- [[CCSA - Querying and Analytics]]
- [[CCSA - Temario oficial y plan de estudio]]
- [[Mapa CCSA CrowdStrike]]

## Fuentes

- CCSA Exam Guide: https://assets.crowdstrike.com/is/content/crowdstrikeinc/crowdstrike-university-ccsa-certification-exam-guidepdf
- Training Catalog - SIEM 106 / SIEM 109: https://www.crowdstrike.com/content/dam/crowdstrike/marketing/en-us/documents/pdfs/crowdstrike-university/CSU-Training-Catalog.pdf
- Incident Management overview: https://www.crowdstrike.com/en-us/platform/next-gen-siem/incident-management/
