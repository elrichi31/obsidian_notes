---
tags:
  - ccsa
  - crowdstrike
  - siem
  - logscale
  - cql
---

# Neighbor en SIEM - CrowdStrike LogScale

> [!info] Idea clave
> En **CrowdStrike Falcon LogScale**, `neighbor()` es una **funcion de consulta** que te deja mirar el **evento anterior o siguiente** dentro de una secuencia de eventos.

> [!important] Para examen
> `neighbor()` **no es un concepto generico de todos los SIEM**, sino una funcion de **LogScale/CQL** usada para comparar eventos cercanos en el tiempo o dentro de un grupo.

## Que es `neighbor()`

`neighbor()` toma campos de un evento "vecino":

- el **anterior** (`preceding`)
- o el **siguiente** (`succeeding`)

Tambien puede mirar a una distancia mayor, por ejemplo:

- `distance=1` = el inmediatamente anterior/siguiente
- `distance=2` = dos eventos antes/despues

## Para que sirve en el SIEM

En un SIEM sirve para **relacionar eventos cercanos** y detectar comportamiento, no solo eventos aislados.

Si quieres unir **varios eventos** para formar una historia mas grande con varias queries, revisa [[correlate() en SIEM - CrowdStrike LogScale]].
Si lo que quieres es resumir datos por campo, revisa [[groupBy() en SIEM - CrowdStrike LogScale]].

Casos comunes:

- comparar un evento con el anterior
- detectar cambios bruscos entre un evento y otro
- medir tiempos entre eventos
- encontrar secuencias sospechosas
- revisar si sesiones de un mismo usuario se traslapan
- identificar tendencias, por ejemplo varios incrementos consecutivos

## Explicacion simple

Piensalo asi:

> [!tip] Regla mental
> `neighbor()` le pregunta al SIEM:  
> "Para este evento actual, que paso justo antes o justo despues?"

Eso ayuda a convertir logs sueltos en **contexto**.

## Sintaxis rapida

```logscale
head()
| neighbor(field, prefix=prev)
```

Ejemplo:

```logscale
head()
| neighbor(user, prefix=prev)
```

Esto agrega algo como `prev.user`, o sea el valor del campo `user` del evento anterior.

## Ejemplos utiles

### 1. Ver el evento anterior

```logscale
head()
| neighbor(status, prefix=prev)
```

Sirve para comparar el `status` actual con el anterior.

### 2. Ver el siguiente evento

```logscale
head()
| neighbor(status, prefix=next, direction=succeeding)
```

Sirve para saber que paso despues del evento actual.

### 3. Detectar cambios bruscos

```logscale
head()
| neighbor(value, prefix=prev)
| change := value - prev.value
| change > 5
```

Esto ayuda a detectar aumentos importantes entre un evento y el anterior.

### 4. Revisar secuencias por usuario

```logscale
head()
| groupBy(user.id, function=neighbor(include=[startTime, endTime], prefix=previous))
```

Esto sirve para comparar sesiones del mismo usuario y encontrar solapamientos o actividad rara.

## Ejemplo explicado 5: impossible travel

Escenario:

> Quieres detectar si un usuario inicio sesion desde dos paises distintos en poco tiempo. Para eso necesitas comparar cada login exitoso contra el login exitoso anterior del mismo usuario.

Query conceptual:

```logscale
#event.category="authentication"
#event.outcome="success"
| ipLocation(source.ip)
| sort([user.name, @timestamp], order=[asc, asc])
| neighbor(include=[user.name, source.ip, source.ip.country, source.ip.lat, source.ip.lon, @timestamp], prefix="prev")
| user.name = prev.user.name
| source.ip.country != prev.source.ip.country
```

### Paso por paso

1. Filtras autenticaciones exitosas.
2. `ipLocation(source.ip)` agrega geolocalizacion de la IP.
3. `sort([user.name, @timestamp])` establece un orden por usuario y tiempo.
4. `neighbor(..., prefix="prev")` copia campos del evento anterior.
5. `user.name = prev.user.name` evita comparar usuarios distintos.
6. `source.ip.country != prev.source.ip.country` deja eventos donde cambio el pais.

### Por que el orden importa

`neighbor()` no sabe que significa "anterior" si antes no ordenas o agregas los eventos. Por eso una query que haga esto esta incompleta:

```logscale
#event.category="authentication"
#event.outcome="success"
| ipLocation(source.ip)
| neighbor(include=[source.ip, @timestamp], prefix="prev")
```

El problema es que intenta usar `neighbor()` sin establecer un orden claro.

> [!important] Clave de examen
> Si ves `neighbor()` justo despues de filtros simples, sospecha. La funcion necesita una secuencia ordenada por `head()`, `sort()`, `bucket()`, `groupBy()` o algo equivalente.

## Ejemplo explicado 6: que paso antes y despues de LSASS

Escenario:

> Una deteccion indica acceso sospechoso a LSASS en `DESKTOP-8472` a las 14:23 UTC. Quieres ver procesos, conexiones y archivos alrededor del evento.

Opcion de investigacion en consola:

- usar **Show in Context** desde el evento para ver eventos cercanos en una ventana temporal

Opcion conceptual en CQL:

```logscale
host.name="DESKTOP-8472"
| sort(@timestamp, order=asc)
| neighbor(include=[@timestamp, event_simpleName, process.name, process.command_line], prefix="prev")
| neighbor(include=[@timestamp, event_simpleName, process.name, process.command_line], prefix="next", direction=succeeding)
```

### Que te da

Para cada evento, ves:

- campos del evento anterior como `prev.process.name`
- campos del evento siguiente como `next.process.name`
- contexto temporal inmediato

Esto sirve para distinguir:

- herramienta administrativa legitima
- secuencia sospechosa con acceso a credenciales
- actividad posterior como red, escritura de archivo o ejecucion adicional

## Diferencia entre `neighbor()` y Show in Context

| Concepto | Uso |
|---|---|
| `neighbor()` | funcion CQL para comparar evento actual contra anterior/siguiente |
| Show in Context | accion de investigacion en UI para ver eventos alrededor de uno seleccionado |

Ambos ayudan con contexto temporal, pero no son lo mismo.

## Algo que suelen preguntar

> [!question] Por que a veces va despues de `head()` o `sort()`?
> Porque `neighbor()` necesita que los eventos tengan un **orden definido**.  
> Si no hay orden, no tiene sentido hablar de "anterior" o "siguiente".

## Errores comunes

- pensar que `neighbor()` busca eventos "parecidos"
- creer que funciona bien sin ordenar primero los eventos
- confundir "vecino" con IP vecina o dispositivo de red
- olvidar que puede mirar hacia atras o hacia adelante
- comparar usuarios distintos por no filtrar o validar la misma entidad
- usarlo para patrones multi-evento complejos donde conviene mas `correlate()`

## Respuesta corta tipo examen

> [!success] Respuesta lista para decir
> En CrowdStrike Falcon LogScale, `neighbor()` es una funcion de consulta que permite obtener campos del evento anterior o siguiente dentro de una secuencia. Sirve en el SIEM para comparar eventos cercanos, detectar cambios, medir tiempos entre eventos y encontrar patrones o comportamientos sospechosos.

## Idea para memorizar

**Neighbor = evento vecino = contexto inmediato.**

## Relacionadas

- [[correlate() en SIEM - CrowdStrike LogScale]]
- [[groupBy() en SIEM - CrowdStrike LogScale]]
- [[defineTable() y match() en SIEM - CrowdStrike LogScale]]
- [[Mapa CCSA CrowdStrike]]

## Fuentes

- LogScale Docs: https://library.humio.com/data-analysis/functions-neighbor.html
- Nota oficial: `neighbor()` debe usarse despues de una funcion que ordene/agrupe eventos, como `head()`, `sort()`, `bucket()` o `groupBy()`
