---
tags:
  - ccsa
  - crowdstrike
  - siem
  - logscale
  - cql
  - correlate
---

# correlate() en SIEM - CrowdStrike LogScale

> [!info] Idea clave
> En **CrowdStrike Falcon LogScale**, `correlate()` es una funcion para **encontrar patrones formados por multiples eventos** conectados por campos en comun y, opcionalmente, por reglas de tiempo o secuencia.

> [!important] En CrowdStrike LogScale
> La correccion importante aqui es esta: para tu estudio no hay que mezclar el concepto generico de "correlation" con la funcion concreta **`correlate()`**.

## Que es `correlate()`

Segun la documentacion oficial, `correlate()`:

- busca relaciones entre eventos de **dos o mas queries nombradas**
- une eventos usando **links** con el operador `<=>`
- puede exigir que ocurran en cierta **secuencia**
- puede exigir que ocurran dentro de una ventana **within**
- devuelve coincidencias llamadas **constellations**

## Como piensa `correlate()` la relacion entre eventos

> [!tip] Regla mental
> `correlate()` no agrupa ni cuenta.  
> `correlate()` intenta responder:  
> "Que conjunto de eventos cumple este patron?"

La documentacion habla de estos conceptos:

- **Query**: cada tipo de evento que quieres buscar
- **Link**: la relacion entre campos de distintas queries
- **Constraint**: las reglas extra que deben cumplirse
- **Constellation**: el conjunto de eventos que si hace match

## Para que sirve en el SIEM

Sirve cuando quieres detectar patrones como:

- un proceso padre que crea otro proceso y luego este escribe en disco
- un flujo de autenticacion con varios eventos que deben ocurrir juntos
- relaciones entre host, red, usuario o email cuando un solo evento no basta

## Ejemplo simple

```logscale
correlate(
  AuthError: {
    #event_type="authentication_error"
  },
  DatabaseError: {
    #event_type="database_error"
    | username <=> AuthError.username
  },
  within=1h,
  globalConstraints=[username]
)
```

### Que hace este ejemplo

- define dos queries
- relaciona ambas por `username`
- exige que los eventos correlacionados queden dentro de `1h`
- devuelve las coincidencias que cumplen ese patron

## Ejemplo explicado 2: cuenta habilitada, alerta y cuenta deshabilitada

Escenario:

> Quieres detectar una cuenta de Active Directory que fue habilitada, luego genero una alerta de identity protection y despues fue deshabilitada.

```logscale
correlate(
  AccountEnabled: {
    #repo="base_sensor"
    #event_simpleName="ActiveDirectoryAccountEnabled"
  },

  IDPDetection: {
    #repo="xdr_indicatorsrepo"
    #Vendor="crowdstrike"
    #event.module="identity-protection"
    #event.kind="alert"
    | source.user.id <=> AccountEnabled.AccountObjectSid
  },

  AccountDisabled: {
    #repo="base_sensor"
    #event_simpleName="ActiveDirectoryAccountDisabled"
    | AccountObjectSid <=> AccountEnabled.AccountObjectSid
  },

  sequence=true,
  within=24h,
  maxPerRoot=10
)
```

### Paso por paso

1. `AccountEnabled` busca cuentas habilitadas.
2. `IDPDetection` busca alertas de identity protection.
3. `source.user.id <=> AccountEnabled.AccountObjectSid` conecta la alerta con la cuenta habilitada.
4. `AccountDisabled` busca la cuenta deshabilitada.
5. `AccountObjectSid <=> AccountEnabled.AccountObjectSid` asegura que sea la misma cuenta.
6. `sequence=true` exige el orden exacto: habilitada -> alerta -> deshabilitada.
7. `within=24h` exige que todo ocurra dentro de 24 horas.
8. `maxPerRoot=10` permite mas de una coincidencia por evento raiz.

### Respuesta tipo examen

Esta query busca:

```text
Una cuenta habilitada, alertada y luego deshabilitada en ese orden.
```

Si `sequence=false`, los eventos podrian ocurrir en cualquier orden dentro de la ventana.

## Ejemplo explicado 3: VPN extranjero -> SMB -> upload externo

Escenario:

> Detectar una secuencia donde un usuario autentica al VPN desde pais extranjero, luego se conecta por SMB a un file server y despues hace upload grande a cloud externo.

```logscale
correlate(
  ForeignVPN: {
    event.category="authentication"
    event.outcome="success"
    source.geo.country!="US"
  },

  SMBAccess: {
    event.category="network"
    network.protocol="smb"
    destination.host.name="FILE-SERVER-05"
    | user.name <=> ForeignVPN.user.name
  },

  CloudUpload: {
    event.category="network"
    network.direction="outbound"
    destination.domain=/cloudstorage|drive|dropbox|box/i
    bytes.out > 100000000
    | user.name <=> ForeignVPN.user.name
  },

  sequence=true,
  within=15m
)
```

### Que intenta detectar

```text
Mismo usuario:
login VPN sospechoso -> acceso SMB -> upload grande externo
```

### Por que `correlate()` sirve aqui

Porque necesitas:

- tres tipos de eventos
- tres fuentes posibles
- misma entidad (`user.name`)
- orden especifico
- ventana temporal

Esto no es un simple `groupBy()`. Tampoco es solo `neighbor()`, porque no quieres "el evento anterior"; quieres una constelacion de eventos que cumpla una historia.

## Ejemplo explicado 4: password spraying

Escenario:

> Muchas cuentas distintas reciben fallos de login desde la misma IP en pocos minutos.

Esto suele resolverse mejor con agregacion/correlation rule basada en umbral:

```logscale
event.category="authentication"
event.outcome="failure"
| groupBy(source.ip, function=[count(user.name, distinct=true, as=unique_users)])
| unique_users >= 10
```

### Por que no siempre necesitas `correlate()`

Aqui la historia no es "A luego B luego C". Es:

```text
muchos eventos similares desde la misma IP contra usuarios distintos
```

Eso se parece mas a `groupBy()` + threshold que a `correlate()`.

## Diferencia entre `correlate()`, `groupBy()` y `neighbor()`

| Funcion | Para que sirve | Clave mental |
|---|---|---|
| `correlate()` | encontrar patrones entre multiples queries y eventos relacionados | patron multi-evento |
| `groupBy()` | agrupar eventos por uno o mas campos y aplicar agregaciones | resumen / conteo / estadistica |
| `neighbor()` | mirar el evento anterior o siguiente en una secuencia ordenada | contexto inmediato |

## Diferencia con `groupBy()`

`groupBy()`:

- es una funcion de **agregacion**
- agrupa eventos por uno o varios campos
- por defecto usa `count()`
- sirve para contar, resumir o encontrar combinaciones unicas

`correlate()`:

- no esta pensado para estadisticas
- no es para "cuantos por host" o "cuantos por usuario"
- esta pensado para encontrar **coincidencias entre eventos** que cumplen un patron

> [!important] Dato oficial util
> La documentacion indica que `correlate()` **no esta disenado para generar estadisticas sobre eventos**. Para eso no es la herramienta correcta.

## Diferencia con `neighbor()`

`neighbor()`:

- toma campos del evento anterior o siguiente
- depende de un **orden definido**
- debe ir despues de una funcion que ordene o agregue eventos

`correlate()`:

- no mira "el evento vecino"
- arma relaciones entre varias queries
- usa links, constraints, `within` y `sequence`
- **no puede usarse despues de aggregators**

## Cosas muy importantes de la documentacion

### 1. Puede unir varias queries

La documentacion oficial destaca que `correlate()` puede correlacionar **multiples queries**, no solo dos.

### 2. Tiene restricciones de colocacion

`correlate()`:

- no puede usarse despues de aggregators
- no puede ir dentro de parametros de otras funciones, salvo ciertos subqueries independientes

### 3. Tiene ayudas visuales

Cuando ejecutas `correlate()`, LogScale muestra un **Query Graph** para visualizar las relaciones entre queries y links.

### 4. `sequence=true` cambia completamente la interpretacion

| Parametro | Significado |
|---|---|
| `sequence=false` | los eventos pueden hacer match en cualquier orden |
| `sequence=true` | los eventos deben ocurrir en el orden en que declaraste las queries |

### 5. `within` limita la historia

`within=15m` significa que la constelacion completa debe ocurrir dentro de 15 minutos.

Sin una ventana razonable, puedes correlacionar eventos que comparten usuario/IP pero no pertenecen al mismo incidente.

## Cuando usar cada una

Usa `correlate()` cuando:

- buscas un patron multi-evento
- necesitas relacionar varios tipos de eventos
- necesitas `within` o `sequence`

Usa `groupBy()` cuando:

- quieres conteos, agregaciones o combinaciones unicas
- quieres resumir datos por host, usuario, IP o estado

Usa `neighbor()` cuando:

- quieres comparar cada evento con el anterior o el siguiente
- quieres medir cambios o diferencias secuenciales

## Errores comunes

- pensar que `correlate()` es solo otro nombre para "correlation"
- usar `correlate()` cuando en realidad necesitas `groupBy()`
- confundir relacion por patron con "evento anterior/siguiente"
- olvidar definir bien los links `<=>`
- olvidar limitar por `within` cuando el contexto temporal importa
- poner `sequence=true` sin entender que ahora el orden es obligatorio
- no conectar todas las queries por una entidad comun como usuario, host, SID o IP

## Respuesta corta tipo examen

> [!success] Respuesta lista para decir
> En CrowdStrike Falcon LogScale, `correlate()` es una funcion que encuentra patrones compuestos por multiples eventos relacionados mediante campos en comun. Se diferencia de `groupBy()` porque no agrega ni resume estadisticas, y se diferencia de `neighbor()` porque no busca el evento anterior o siguiente, sino constelaciones de eventos que cumplen links y constraints como `within` o `sequence`.

## Idea para memorizar

**`correlate()` = patron multi-evento.**  
**`groupBy()` = agregacion.**  
**`neighbor()` = vecino secuencial.**

## Relacionadas

- [[Neighbor en SIEM - CrowdStrike LogScale]]
- [[groupBy() en SIEM - CrowdStrike LogScale]]
- [[defineTable() y match() en SIEM - CrowdStrike LogScale]]
- [[Mapa CCSA CrowdStrike]]

## Fuentes

- LogScale Docs - `correlate()`: https://library.humio.com/data-analysis-1.219/functions-correlate.html
- LogScale KB - Correlating Events: https://library.humio.com/kb/kb-correlating-events.html
- Display Results and Events - Query Graph: https://library.humio.com/data-analysis/searching-data-changing-the-events-display.html
