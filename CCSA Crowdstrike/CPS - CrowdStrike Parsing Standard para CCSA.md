---
tags:
  - ccsa
  - crowdstrike
  - siem
  - cps
  - parsing
  - normalization
---

# CPS - CrowdStrike Parsing Standard para CCSA

> [!info] Idea clave
> **CPS** significa **CrowdStrike Parsing Standard**. Es el esquema de normalizacion que ayuda a que logs de distintas fuentes tengan campos mas consistentes dentro de Falcon Next-Gen SIEM.

## Por que importa para el examen

El CCSA no solo evalua si sabes buscar logs. Evalua si puedes analizar datos de multiples fuentes sin depender demasiado del nombre original de cada vendor.

CPS ayuda porque:

- reduce diferencias entre fuentes
- permite queries mas reutilizables
- facilita correlacionar host, red, identidad, email, cloud y aplicaciones
- hace mas facil pivotear entre observables comunes

## Vendor fields vs CPS fields

Un log puede traer campos propios del producto original y tambien campos normalizados.

Ejemplo conceptual:

| Vendor A | Vendor B | Campo normalizado CPS |
|---|---|---|
| `src_ip` | `clientAddress` | `source.ip` |
| `dst_ip` | `remote_ip` | `destination.ip` |
| `username` | `actor.user` | `user.name` |
| `hostname` | `device_name` | `host.name` |

> [!tip] Regla mental
> Si el examen habla de consultar de forma agnostica a la fuente, piensa en CPS.

## Observables que suelen normalizarse

- usuario
- host
- IP origen
- IP destino
- proceso
- command line
- hash
- dominio
- URL
- accion
- resultado
- severidad

## Como se usa en investigacion

CPS sirve cuando necesitas responder:

- que usuarios tuvieron actividad sospechosa?
- que hosts estan relacionados?
- que IPs externas aparecen en varias fuentes?
- que dominios o URLs se repiten?
- que eventos comparten un mismo hash o proceso?

## Ejemplo mental de pivot con CPS

1. Detectas un login raro de `user.name`.
2. Buscas eventos del mismo `user.name` en identidad y endpoint.
3. Pivoteas al `host.name` donde aparecio.
4. Buscas conexiones salientes por `source.ip` o `destination.ip`.
5. Revisas procesos por `process.name` y `process.command_line`.

La clave es que los campos normalizados te dejan moverte entre fuentes con menos friccion.

## Errores comunes

- memorizar campos sin entender que representan
- asumir que CPS hace que todos los logs sean perfectos
- olvidar que un parser mal configurado puede afectar la calidad del dato
- usar solo el campo original del vendor cuando existe uno normalizado mas util

## Preguntas tipo examen

1. Por que CPS ayuda a crear queries reutilizables?
2. Si dos vendors llaman distinto a la IP origen, que ventaja ofrece CPS?
3. CPS reemplaza la necesidad de entender la fuente original?
4. Que riesgo hay si los datos no estan bien parseados?

## Respuestas rapidas

1. Porque usa campos normalizados que pueden aplicar a varias fuentes.
2. Permite consultar un campo comun como `source.ip`.
3. No. CPS ayuda, pero igual debes entender contexto, calidad y significado del log.
4. Puedes perder campos importantes, correlacionar mal o generar falsos positivos/falsos negativos.

## Relacionadas

- [[CCSA - Querying and Analytics]]
- [[Chuleta CQL para CCSA - CrowdStrike LogScale]]
- [[CCSA - Incident Investigation]]
- [[Mapa CCSA CrowdStrike]]

## Fuentes

- CrowdStrike Parsing Standard: https://developer.crowdstrike.com/docs/ng-siem/cps-standard/
- Falcon LogScale Parsing Standard: https://library.humio.com/logscale-parsing-standard/pasta.html
