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

## Algo que suelen preguntar

> [!question] Por que a veces va despues de `head()` o `sort()`?
> Porque `neighbor()` necesita que los eventos tengan un **orden definido**.  
> Si no hay orden, no tiene sentido hablar de "anterior" o "siguiente".

## Errores comunes

- pensar que `neighbor()` busca eventos "parecidos"
- creer que funciona bien sin ordenar primero los eventos
- confundir "vecino" con IP vecina o dispositivo de red
- olvidar que puede mirar hacia atras o hacia adelante

## Respuesta corta tipo examen

> [!success] Respuesta lista para decir
> En CrowdStrike Falcon LogScale, `neighbor()` es una funcion de consulta que permite obtener campos del evento anterior o siguiente dentro de una secuencia. Sirve en el SIEM para comparar eventos cercanos, detectar cambios, medir tiempos entre eventos y encontrar patrones o comportamientos sospechosos.

## Idea para memorizar

**Neighbor = evento vecino = contexto inmediato.**

## Relacionadas

- [[correlate() en SIEM - CrowdStrike LogScale]]
- [[groupBy() en SIEM - CrowdStrike LogScale]]
- [[Mapa CCSA CrowdStrike]]

## Fuentes

- LogScale Docs: https://library.humio.com/data-analysis/functions-neighbor.html
- Nota oficial: `neighbor()` debe usarse despues de una funcion que ordene/agrupe eventos, como `head()`, `sort()`, `bucket()` o `groupBy()`
