# <div id="guia-de-optimizacion">Guía de optimización para servidores de Minecraft ([original](https://github.com/YouHaveTrouble/minecraft-optimization))</div>

Ten en cuenta que esta guía está hecha para la versión 1.16.5 de Minecraft (a pesar de ello, igualmente puedes usarla para versiones más obsoletas), basándose en [esta guía](https://www.spigotmc.org/threads/guide-server-optimization%E2%9A%A1.283181/) y otras fuentes (todas son citadas en la guía cuando son relevantes).

Nunca habrá una guía que puedas seguir y que te otorgue resultados perfectos. Cada servidor tiene sus propias necesidades y límites en lo que puedas o te atrevas a sacrificar. La idea es ir jugando con las opciones para ajustarlas a la necesidad de cada servidor. Esta guía solamente se enfoca en ayudarte a entender qué opciones tendrán impacto en el rendimiento y exactamente qué cambiarán.

## Contenidos

- [Software de servidor](#software-de-servidor)

### <div id="software-de-servidor">Software de servidor ([inicio](#guia-de-optimizacion))</div>

Tu elección de software de servidor (el archivo JAR, al cual muy comúnmente se le nombra `server.jar`) puede hacer una gran diferencia en el rendimiento y posibilidades que ofrece la API. Actualmente hay múltiples opciones populares viables de software de servidor, pero también hay algunas de las que te debes mantener alejado por varias razones.

**Recomendadas:**

- [Paper](https://papermc.io/) - Este el el software de servidor más popular enfocado en mejorar el rendimiento, al igual que arreglar el juego e inconsistencias en las mecánicas.
- [Tuinity](https://github.com/Spottedleaf/Tuinity) - Un fork (derivado) de Paper que se enfoca en mejorar el rendimiento del servidor aún más que Paper.
- [Purpur](https://github.com/pl3xgaming/Purpur) - Un fork (derivado) de Tuinity que se enfoca en ofrecerle al dueño del servidor más libertad en la configuración de funciones del juego.

**Evitar a toda costa:**

- Yatopia - Es un total desorden de parches que ni siquiera han sido probados, que llenarán de bugs y harán injugable tu servidor - "¡El poder combinado de forks (derivados) de Paper para una máxima inestabilidad y creación de un servidor inmantenible!".
- Cualquier software de servidor de paga que dice que todo es async, tienes 99.99% de probabilidades de ser estafado.
- Bukkit/CraftBukkit/Spigot - Extremadamente obsoletos en temas de rendimiento comparados con otros software de servidor accesibles.
- Cualquier plugin/software de servidor que activa/desactiva/recarga plugins en tiempo de ejecución. Lee [esta sección]() para saber por qué.

### Pre-generación del mapa

La pre-generación del mapa es una de las cosas más importantes para mejorar un servidor de bajo presupuesto. Esto ayuda a servidores que son mantenidos en un nodo compartido de un núcleo de CPU, ya que no pueden utilizar totalmente la carga de chunks async. Puedes usar algún plugin como [Chunky](https://www.spigotmc.org/resources/chunky.81534/) para pre-generar el mundo o en versiones menores a la `1.13`, puedes usar [WorldBorder](https://dev.bukkit.org/projects/worldborder). Asegúrate de establecer un borde para que tus jugadores no generen nuevos chunks, de preferencia el [borde del Minecraft vanilla](https://minecraft-es.gamepedia.com/Borde_del_mundo#Comandos), ya que limita ciertas funcionalidades como el rango de búsqueda de mapas que pueden causar problemas de rendimiento. Toma nota que pre-generar puede tomar horas algunas veces dependiendo del radio de generación que hayas establecido.

Es clave de recordar que el mundo normal, el nether y el end tienen bordes de mundo separados que debes establecer en cada mundo. La dimensión del nether es 8 veces más pequeña que el mundo normal (si no está modificado por un datapack), sasí que si estableces mal el tamaño tus jugadores podrían terminar el otro lado del borde del mundo.

### Flags de inicio de Java

[Paper y sus forks (derivados) en la próxima versión 1.17 requerirán Java 11 (LTS) o mayor](https://papermc.io/forums/t/java-11-mc-1-17-and-paper/5615). ¡Una buena resolución para el 2021 de finalmente actualizar tu versión de Java! (o por lo menos informar a tu proveeedor, así ellos manejan la migración).

La JVM puede ser configurada para reducir los picos de lag causados por las grandes tareas de recolección de basura. Puedes generar flags de inicio ideales para tu servidor en [startmc.sh](https://startmc.sh/), para mayor información sobre estas flags haz [click aquí](https://mcflags.emc.gs/).

### Plugins "demasiado buenos para ser verdad"

TODO

### Configuraciones

En esta sección se mostrarán opciones de los software de servidor que podrían ayudar a mejorar el rendimiento de tu servidor, y junto a cada opción se mostrará una breve descripción de lo que hace.

#### **server.properties**

`network-compression-threshold`

- **Por defecto:** `256`

- **Optimizado:** Servidor independiente: `512` - BungeeCord: `-1`

- **Explicación:** Esta opción captura el tamaño de un paquete antes que el servidor lo comprima. Establecerlo en valores mayores puede ahorrar algunos recursos a costa de un consumo mayor de ancho de banda, y establecerlo a `-1` lo desactiva. Si tu servidor es una network con un proxy en la misma máquina o datacenter (con menos de 2ms de ping), deshabilitarlo (`-1`) tendrá beneficios.

---

#### **bukkit.yml**

<div id="spawn-limits"></div>

`spawn-limits`

- **Por defecto:** `monsters: 70, animals: 10, water-animals: 15, water-ambient: 20, ambient:15`

- **Optimizado:** `monsters: 12, animals: 5, water-animals: 2, water-ambient: 2, ambient: 0`

- **Explicación:** Valores menores significan menos mobs. Menos mobs es menos lag en general, pero debes intentar balancearlo para mantener la calidad de juego, encontrar mobs en un mundo es una gran parte del juego. Con [per-player-mob-spawns](#per-player-mob-spawns) esos números básicamente representan `1:1` del límite de mobs en la categoría dada por cada jugador, así que la matemática de esto es `cantidad-jugadores*límite` donde "cantidad-jugadores" es la cantidad de jugadores conectados actual en el servidor.

`chunk-gc.period-in-ticks`

- **Por defecto:** `600`

- **Optimizado:** `400`

- **Explicación:** Esto decide qué tan frecuentemente los chunks desocupados son descargados. Valores menores significará menos chunks siendo tickeados, pero ir a valores muy bajos solamente estresará más a tu CPU.

`ticks-per`

- **Por defecto:** `monster-spawn: 1, animal-spawns: 400, water-spawns: 1, ambient-spawns: 1, water-ambient-spawns: 1`

- **Optimizado:** `monster-spawn: 10, animal-spawns: 400, water-spawns: 40, ambient-spawns: 40, water-ambient-spawns: 40`

- **Explicación:** Esto establece que tan frecuentemente (en ticks, 20 ticks equivale a 1 segundo) el servidor intenta aparecer ciertos tipos de entidades vivas. Mobs de agua/ambiente no necesitan aparece cada tick debido a que no son usualmente asesinados rápidamente. Para los monstruos: Incrementar un poco el tiempo entre apariciones no debería impactar en la tasa de apariciones, ni tampoco en granjas de mobs.

---

#### **spigot.yml**

`max-tick-time`

- **Por defecto:** `tile: 50, entity: 50`

- **Optimizado:** `tile: 1000, entity: 1000`

- **Explicación:** Establecer esto a los valores recomendados lo desactiva. Puedes leer el por qué debería estar desactivado desde [aquí](https://aikar.co/2015/10/08/spigot-tick-limiter-dont-use-max-tick-time/).

<div id="view-distance"></div>

`view-distance`

- **Por defecto:** `default`

- **Optimizado:** `3`

- **Explicación:** Establecer la distancia de vista en un valor menor hará menos ticks de chunks, y con la opción [no-tick-view-distance](#no-tick-view-distance) activado podrás enviar más chunks a un jugador que realmente hagan tick. Si tienes problemas con el valor optimizado, puedes aumentarlo a `4` y aún así tendrás un rendimiento decente.

`mob-spawn-range`

- **Por defecto:** `8`

- **Optimizado:** `2`

- **Explicación:** Algunas personas discuten que establecer esto a uno menos que [view-distance](#view-distance) es bueno, pero valores menores como `2` o `3` te permitirán disminuir la cantidad de mobs en [spawn-limits](#spawn-limits).

`entity-activation-range`

- **Por defecto:** `animals:32, monsters:32, raiders: 48, misc:16`

- **Optimizado:** `animals:16, monsters:24, raiders: 48, misc:8`

- **Explicación:** Entidades pasado este rango serán menos tickeadas frecuentemente. Evita establecer esto muy bajo o podrías romper las mecánicas de los mobs.

`tick-inactive-villagers`

- **Por defecto:** `true`

- **Optimizado:** `false`

- **Explicación:** Habilitar esto evita que el servidor haga tick a los aldeanos que están fuera del rango de activación. Las tareas de los aldeanos en versiones `1.14+` son muy pesadas.

`merge-radius`

- **Por defecto:** `item:2.5, exp:3.0`

- **Optimizado:** `item:3.0, exp:6.0`

- **Explicación:** Esto decidirá la distancia entre los objetos que serán juntados, reduciendo la cantidad de items tickeando en el suelo. Juntarlos llevará a la ilusión de que los objetos han desaparecido, al combinarse en uno solo.

`nerf-spawner-mobs`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Cuando está activado, los mobs de los spawners no tendrán IA (no nadarán, atacarán o se moverán). Esto beneficia bastante a los TPS en servidores con granjas de mobs, pero también arruina la mecánica de éstas.

---

#### **paper.yml**

`max-auto-save-chunks-per-tick`

- **Por defecto:** `24`

- **Optimizado:** `8`

- **Explicación:** Hace más lento el guardado de mundo incremental al repartir la tarea en el tiempo incluso más, para así tener un mejor rendimiento promedio. Probablemente quieras establecer esto en un valor un tanto mayor si tienes más de `20 - 30` jugadores; si el guardado incremental no logra terminar a tiempo, entonces Bukkit automáticamente guardará los chunks restantes de una vez y comenzará el proceso nuevamente.

<div id="per-player-mob-spawns"></div>

`per-player-mob-spawns`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Por defecto los límites de mob están contados para el servidor entero, lo que significa que los mobs probablemente terminen distribuidos de forma desigual entre los jugadores conectados. Esta opción habilita los límites de mobs por jugador, haciendo así que cada jugador pueda obtener aproximadamente la misma cantidad de mobs independiente de la cantidad de jugadores en línea. Habilitar esta opción también te permite disminuir el valor por defecto de [spawn-limits](#spawn-limits) en `bukkit.yml`, ya que éstos están optimizados para límite de mobs para todo el servidor.

`optimize-explosions`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Algoritmo más rápido de explosión sin ningún impacto notable en la jugabilidad.

`max-entity-collisions`

- **Por defecto:** `8`

- **Optimizado:** `2`

- **Explicación:** Limita la cantidad de colisiones que una entidad calculará al mismo tiempo. Establecer esto en el valor optimizado te permitirá mantener la habilidad del jugador para empujar mobs. Establecer esto a 0 completamente desactivaría las colisiones, haciendo que empujar mobs/jugadores sea imposible.

`grass-spread-tick-rate`

- **Por defecto:** `1`

- **Optimizado:** `4`

- **Explicación:** Tiempo en ticks (20 ticks equivalen a 1 segundo) en el que el servidor intenta esparcir el pasto/micelio. No afecta a la jugabilidad en la mayoría de los casos.

`despawn-ranges`

- **Por defecto:** `soft: 32, hard: 128`

- **Optimizado:** `soft: 28, hard: 52`

- **Explicación:** Reduce el rango de limpieza de mobs en segundo plano y permite que más aparezcan en áreas de tráfico de jugadores. Esto reduce el impacto en la jugabilidad por las apariciones reducidas de `bukkit.yml`. Valores ajustados para [view-distance](#view-distance) en `3`.

`hopper.disable-move-event`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** `InventoryMoveItemEvent` no es lanzado a menos que tengas un plugin activamente escuchando ese evento. Esto significa que deberías colocar esto en `true` solamente si no tienes plugins que modifiquen mecánicas de las tolvas o no te importa que no puedan escuchar este evento. Un ejemplo de plugin que utiliza esta mecánica sería los de protecciones.

`non-player-arrow-despawn-rate`

- **Por defecto:** `-1`

- **Optimizado:** `20`

- **Explicación:** El tiempo en ticks (20 ticks equivale a 1 segundo) en el que las flechas disparadas por mobs desaparecen. En este caso desaparecerán en 1 segundo tras ser disparadas.

`creative-arrow-despawn-rate`

- **Por defecto:** `-1`

- **Optimizado:** `20`

- **Explicación:** El tiempo en ticks (20 ticks equivale a 1 segundo) en el que las flechas disparadas por jugadores en creativo desaparecen. En este caso desaparecerán en 1 segundo tras ser disparadas.

`prevent-moving-into-unloaded-chunks`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Previene que los jugadores entren a chunks no cargados (debido al lag), lo cual causa más lag. Establecer esto a `true` los devolverá a su última ubicación segura en caso de que suceda esto.

`use-faster-eigencraft-redstone`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Sistema de redstone alternativo mucho más rápido. Reduce las actualizaciones de redstone redundates en cerca de un 95%.

`alt-item-despawn-rate.enabled`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Esta opción te permitirá desaparecer algunos objetos más rápido que en la tasa de desaparición por defecto. Puedes añadir cosas como cobblestone, netherrack, entre otras a la lista y hacer que desaparezcan después de ~20 segundos (400 ticks).

`enable-treasure-maps`

- **Por defecto:** `true`

- **Optimizado:** `false`

- **Explicación:** Generar mapas del tesoro es extremadamente caro en temas de rendimiento y puede colgar un servidor si la estructura que está intentando localizar está fuera del mundo generado. Solamente es seguro activar esto si has pre-generado tu mundo y establecido el borde de Minecraft Vanilla.

`treasure-maps-return-already-discovered`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** El valor por defecto forza a los mapas generados recientemente a buscar estructuras inexploradas que usualmente están fuera del terreno generado. Estableciendo esto a `true` se hace que los mapas puedan llevar a estructuras ya descubiertas.

<div id="no-tick-view-distance"></div>

`viewdistances.no-tick-view-distance`

- **Por defecto:** `-1`

- **Optimizado:** `8`

- **Explicación:** Esto permite a los jugadores a ver más allá sin hacer tick a tantos chunks como el [view-distance](#view-distance) regular lo haría. Aunque no sea realmente pesado para el servidor, ten en cuenta que enviar más chunks afectará al consumo de ancho de banda.

`entity-per-chunk-save-limit`

- **Por defecto:** `-1`

- **Optimizado:**

```yaml
entity-per-chunk-save-limit:
    arrow: 8
    dragonfireball: 8
    egg: 8
    ender_pearl: 8
    fireball: 8
    firework: 8
    largefireball: 8
    lingeringpotion: 8
    llamaspit: 8
    shulkerbullet: 8
    sizedfireball: 8
    snowball: 8
    spectralarrow: 8
    splashpotion: 8
    thrownexpbottle: 8
    trident: 8
    witherskull: 8
```

- **Explicación:** Limita la cantidad de proyectiles que pueden ser guardados en un chunk. Esto previene problemas que surgen cuando se disminuye la opción [view-distance](#view-distance), como jugadores lanzando cantidades masivas de bolas de nieve a un chunk no cargado que puede causar un potencial crasheo al intentar cargar ese chunk.

`anti-xray.enabled`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Esconde menas de jugadores con X-ray. Para una configuración detallada de esta función, revisa los [ajustes recomendados de Stonar96](https://gist.github.com/stonar96/ba18568bd91e5afd590e8038d14e245e).

---

#### **purpur.yml**

`tps-catchup`

- **Por defecto:** `true`

- **Optimizado:** `false`

- **Explicación:** La recuperación de TPS hace que tu servidor se ponga en marcha después de las caídas de tps. Si tu servidor está constantemente funcionando a más de 50mspt lo más probable es que vea una mejora en el rendimiento debido a que el servidor no tratará de promediar los tps a 20 al pasar de 20. Toma nota que algunos plugins pueden no esperar que esta característica esté deshabilitada.

`use-alternate-keepalive`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Sistema alternativo de paquetes keepalive que hace que los jugadores con mala conexión no sobrepasen el tiempo de espera de forma tan regular.

`dont-send-useless-entity-packets`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Evita que el servidor envíe paquetes vacíos de cambio de posición (por defecto el servidor manda paquetes de movimiento de cada entidad incluso cuando la entidad no se ha movido).

`gameplay-mechanics.player.teleport-if-outside-border`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Teletransportar al jugador al punto de aparición del mundo si llega pasar que están afuera del borde del mundo. Esto es útil debido a que el borde vanilla es evadible y el daño que hace al jugador puede ser mitigado.

`gameplay-mechanics.player.entities-can-use-portals`

- **Por defecto:** `true`

- **Optimizado:** `false`

- **Explicación:** Desactiva el uso de portales en todas las entidades a excepción de jugadores. Esto potencialmente arregla un bug de duplicación (faltan fuentes) y evita que las entidades vayan de mundo en mundo cargando chunks en el main thread.

`mobs.dolphin.disable-treasure-searching`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Evita que los delfines hagan una búsqueda de estructuras similar a la de los mapas del tesoro ([enable-treasure-maps]()).

`mobs.zombie.aggressive-towards-villager-when-lagging`

- **Por defecto:** `true`

- **Optimizado:** `false`

- **Explicación:** Los zombies paran de atacar aldeanos cuando los TPS están muy bajos. Esto le ahorra al servidor precioso tiempo calculando rutas para los zombies que no están persiguiendo jugadores.
