# PokeData

Datos del metagame de **Pokémon Champions**, reconstruidos y publicados
automáticamente una vez al día.

Este repositorio **no lleva código**: solo el resultado. Lo genera una tarea
programada del repositorio de la aplicación, que agrega tres fuentes públicas y
publica aquí el paquete ya hecho.

## Por qué existe

La aplicación reconstruía el metagame **en cada instalación**: unas quince
peticiones y 4,4 MB por usuario y día, contra tres APIs gratuitas de terceros.
Con mil usuarios eso son 4,3 GB diarios del mismo cálculo repetido mil veces.

Publicándolo aquí, ese trabajo se hace **una vez** y la aplicación baja un
fichero estático. Los días que no cambia le cuesta cero bytes (`If-None-Match`
→ `304`).

## Qué hay dentro

```
metagame/<REGLAMENTO>/metagame-snapshot.json
```

Por ejemplo `metagame/M-B/metagame-snapshot.json`. Cada fichero trae:

| clave | qué es |
|---|---|
| `regulationId` | el reglamento al que pertenece |
| `generatedAt` | cuándo se construyó este paquete |
| `pokemonUsage` | ranking de uso y desglose por especie |
| `topTeams` | equipos de torneo, con sus seis Pokémon |
| `attributions` | de quién son los datos (ver abajo) |

## Atribución

Los datos de combate proceden de **Pokémon Champions Battle Data** y su uso
exige acreditarlo:

> Battle data provided by Pokémon Champions Battle Data — https://championsbattledata.com

La atribución viaja también **dentro de cada JSON**, en la clave `attributions`,
para que llegue a cualquiera que use el fichero sin pasar por aquí.

Los equipos de torneo proceden de [Limitless](https://play.limitlesstcg.com) y
las cuotas de uso de [MunchStats](https://www.munchstats.com).

## Cómo se actualiza

Una tarea diaria (06:00 UTC) reconstruye el paquete y lo publica **solo si ha
cambiado**: un commit vacío cambiaría el `ETag` sin motivo y obligaría a todos
los clientes a bajar medio mega para nada.

Antes de publicar se comprueba que el paquete nuevo **no tenga menos equipos**
que el anterior. La API de torneos sirve una ventana móvil de los más recientes,
y una pasada mal dada podría reducir el histórico; si eso pasa, la tarea falla y
aquí se queda el de ayer, que es correcto y solo un día más viejo.
