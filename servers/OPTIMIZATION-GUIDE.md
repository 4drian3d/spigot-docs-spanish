# <div id="guia-de-optimizacion">Guía de optimización para servidores de Minecraft ([Fuente original](https://github.com/YouHaveTrouble/minecraft-optimization))</div>

Esta es una guía de optimización adaptada para las versiones más actuales de Minecraft (a pesar de ello, igualmente puedes usar ciertos ajustes en versiones obsoletas), que se basa principalmente en lo que establece [esta guía](https://www.spigotmc.org/threads/guide-server-optimization%E2%9A%A1.283181/) y otras fuentes que serán citadas cuando sea relevante.

Nunca habrá una guía que puedas seguir y que te otorgue resultados perfectos. Cada servidor tiene sus propias necesidades y límites en lo que puedas o te atrevas a sacrificar. La idea es ir jugando con las opciones para ajustarlas a la necesidad de cada servidor. Esta guía solamente se enfoca en ayudarte a entender qué opciones tendrán impacto en el rendimiento y exactamente qué cambiarán, por lo que no es recomendable ir colocando cada opción sin haber leído previamente su descripción.

## Contenidos

- [Software de servidor](#software-de-servidor)
  - [Software recomendado](#software-recomendado)
  - [Software no recomendado](#software-no-recomendado)
- [Pre-generación del mapa](#pre-generacion-mapa)
  - [¿Qué es la pre-generación de mapa?](#beneficios-como-pre-generar-mapa)
  - [Estableciendo un borde del mapa](#borde-mapa)
- [Configuración de Java](#configuracion-java)
  - [Parámetros de la JVM](#parametros-jvm)
  - [Versión de Java](#version-java)
- [¿Qué cosas debo evitar a toda costa?](#evitar-toda-costa)
  - [Plugins que remueven objetos del suelo](#plugins-remueven-objetos-suelo)
  - [Plugins que stackean mobs](#plugins-stackean-mobs)
  - [Activar, desactivar o recargar plugins](#recargar-plugins)
- [¿Cómo mido el rendimiento del servidor?](#medir-rendimiento-servidor)
  - [mspt](#mspt)
  - [timings](#timings)
  - [spark](#spark)
- [Configuraciones optimizadas](#configuraciones-optimizadas)
  - [server.properties](#server-properties)
  - [bukkit.yml](#bukkit-yml)
  - [spigot.yml](#spigot-yml)
  - [paper.yml](#paper-yml)
  - [tuinity.yml](#tuinity-yml)
  - [purpur.yml](#purpur-yml)

### <div id="software-de-servidor">Software de servidor ([inicio](#guia-de-optimizacion))</div>

Tu elección de software de servidor (el archivo JAR, al cual muy comúnmente se le nombra `server.jar`) puede hacer una gran diferencia en el rendimiento y en la posibilidades que ofrece la API como tal. Actualmente hay múltiples opciones populares viables de software de servidor, pero también hay algunas de las cuales te debes mantener alejado y evitar a toda costa.

#### <div id="software-recomendado">**Software recomendado**</div>

- [Paper](https://papermc.io/) - Este es el software de servidor más popular y con menos incompatibilidades enfocado en mejorar el rendimiento, también incluye algunas opciones que ayudan a mejorar las mecánicas de Minecraft Vanilla. Además, siendo fork de Spigot, incluye la compatibilidad con plugins de este último, pudiendo mejorar el rendimiento con el que se desempeñan y ofreciendo una API más extensiva que puede ser aprovechada por desarrolladores de plugins.
- [Tuinity](https://github.com/Spottedleaf/Tuinity) - Un fork (derivado) de Paper que se enfoca en mejorar el rendimiento del servidor en un nivel superior al de Paper. Su principal fortaleza es su sistema de iluminación [Starlight](https://github.com/Spottedleaf/Starlight), el cual mejora considerablemente el [tiempo de carga de chunks](https://camo.githubusercontent.com/c7a72e2b4f101795e37902c64d09c59a4997bcff2117887b37bd7d6fad03cf1c/68747470733a2f2f692e696d6775722e636f6d2f364f6375794a582e706e67).
- [Purpur](https://purpur.pl3x.net/) - Un fork (derivado) de Paper que implementa los parches de Tuinity, que se enfoca en ofrecerle al dueño del servidor más libertad en la configuración de funciones del juego, ofreciendo decenas de nuevas configuraciones y caracteristicas, sin coste extra de rendimiento y desactivadas por defecto.
- [Airplane](https://airplane.gg/) - Un fork (derivado) de Tuinity dedicado a mejorar el rendimiento de entidades, reducir los tiempos de recolección de basura y optimizar las tolvas en alrededor de un 30%. Puedes leer más sobre Airplane haciendo [click aquí](https://blog.airplane.gg/about/).

#### <div id="software-no-recomendado">**Software no recomendado**</div>

- Yatopia - Es un total desorden de parches que ni siquiera han sido probados, que podrían llenar de bugs tu servidor y provocar inestabilidad en las mecánicas - "¡El poder combinado de forks (derivados) de Paper para una máxima inestabilidad y creación de un servidor inmantenible!".
- Cualquier software de servidor de paga que dice que todo es async, tienes 99.99% de probabilidades de ser estafado.
- Vanilla/CraftBukkit/Spigot - Extremadamente obsoletos en temas de rendimiento comparados con otros software de servidor accesibles.
- Cualquier plugin/software de servidor que activa/desactiva/recarga plugins en tiempo de ejecución. Lee [esta sección](#recargar-plugins) para saber por qué.

### <div id="pre-generacion-mapa">Pre-generación del mapa ([inicio](#guia-de-optimizacion))</div>

#### <div id="beneficios-como-pre-generar-mapa">**¿Qué es la pre-generación de mapa?**</div>

La pre-generación del mapa es una de las cosas más importantes para mejorar un servidor de bajo presupuesto o poco potentes. Esto ayuda a servidores que son mantenidos en un nodo compartido que posea solamente 1 núcleo de CPU (evitar a toda costa), ya que no pueden utilizar totalmente la carga de chunks async. Puedes usar algún plugin como [Chunky](https://www.spigotmc.org/resources/chunky.81534/) para pre-generar el mundo o en versiones menores a la `1.13`, puedes usar una versión obsoleta de [WorldBorder](https://dev.bukkit.org/projects/worldborder).

Ejemplos de **Chunky**:

- Generar chunks desde la coordenada 0,0 en un radio de 1000 bloques en el mundo normal:

```txt
/chunky radius 1000
Radio asignado a 1000 bloques desde la coordenada 0,0.

/chunky start
Se ha comenzado la pre-generación del mapa.
```

- Generar chunks desde la coordenada 100,-100 en un radio de 5000 bloques en el mundo del nether:

```txt
/chunky center 100 -100
Se estableció la coordenada central a 100,-100.

/chunky radius 5000
Radio asignado a 5000 bloques desde la coordenada 100,-100.

/chunky world world_nether
El mundo que se pre-generará será: world_nether

/chunky start
Se ha comenzado la pre-generación del mapa.
```

- Establecer un borde de un mundo a 0,0 en un diámetro de 0000 bloques y generar los chunks del interior:

```txt
/worldborder center 0 0
Borde del mundo centrado a las coordenadas 0,0.

/worldborder set 20000
Borde del mundo establecido en un área de 20000 bloques (10000 por 10000).

/chunky worldborder
Se ha establecido que el radio de pre-generación será el mismo que el del borde del mundo.

/chunky start
Se ha comenzado la pre-generación del mapa.
```

Ejemplos de **WorldBorder**:

- Establecer un borde de un mundo llamado "survival" (usando el borde de WorldBorder, posteriormente es recomendable eliminarlo y usar el de Minecraft Vanilla) en un radio de 10000 bloques desde las coordenadas 0,0 y generar los chunks del interior:

```txt
/wb survival set 10000 10000 0 0
Se ha establecido el borde del mundo en un radio de 10000 por 10000 bloques desde la coordenada 0,0.

/wb survival fill
Se pre-generarán todos los chunks restantes de este mundo, ¿estás seguro?

/wb fill confirm
Se ha comenzado la pre-generación del mapa.
```

#### <div id="borde-mapa">**Estableciendo un borde en el mapa**</div>

Asegúrate de establecer un borde para que tus jugadores no generen nuevos chunks, de preferencia el [borde del Minecraft vanilla](https://minecraft-es.gamepedia.com/Borde_del_mundo#Comandos), ya que limita ciertas funcionalidades como el rango de búsqueda de mapas del tesoro que pueden causar problemas de rendimiento. Toma nota que pre-generar puede tomar horas algunas veces dependiendo del radio de generación que hayas establecido.

Es clave de recordar que el mundo normal, el nether y el end tienen bordes de mundo separados que debes establecer en cada mundo. La dimensión del nether es 8 veces más pequeña que el mundo normal (si no está modificado por un datapack), sasí que si estableces mal el tamaño tus jugadores podrían terminar el otro lado del borde del mundo.

Ejemplo:

- Estableciendo un borde en un díametro de 20000 bloques desde la coordenada 0,0:

```txt
/worldborder center 0 0
Borde del mundo centrado a las coordenadas 0,0.

/worldborder set 20000
Borde del mundo establecido en un diámetro de 20000 bloques (10000 por 10000).
```

### <div id="configuracion-java">Configuración de Java ([inicio](#guia-de-optimizacion))</div>

#### <div id="parametros-jvm">**Parámetros de la JVM**</div>

La JVM (Java Virtual Machine) es una máquina virtual que le permite a tu computadora interpretar el bytecode de Java ([más información](https://es.wikipedia.org/wiki/M%C3%A1quina_virtual_Java)).

Las parámetros de inicio de la JVM te permiten modificar aspectos de cómo será iniciado tu servidor, probablemente los parámetros que más te suenen sean las que asignan el consumo inicial y máximo de RAM: `java -Xms1G -Xmx1G -jar server.jar`.

La JVM puede ser configurada para reducir los picos de lag causados por las grandes tareas de recolección de basura. Puedes generar flags de inicio ideales para tu servidor en [startmc.sh](https://startmc.sh/), para mayor información sobre estas flags haz [click aquí](https://mcflags.emc.gs/).

#### <div id="version-java">**Versión de Java**</div>

Usualmente para iniciar un servidor de Minecraft se requería usar Java 8, pero afortunadamente el equipo de Mojang decidió que, a partir de las versiones 1.17 en adelante, será obligatorio el uso de Java 16 a mayor en servidores y clientes.

Anteriormente PaperMC anunció el requerimiento obligatorio del uso de Java 11 para su producto Paper y los forks derivados de éste: [Java 16, MC 1.17, and Paper](https://papermc.io/forums/t/java-16-mc-1-17-and-paper/5615), pero debido al cambio directo de Minecraft a Java 16, todos los servidores tendrán usar esta versión o alguna versión mayor, tal como Java 17 LTS ([más informacion sobre el soporte a versiones de Java](https://www.oracle.com/java/technologies/java-se-support-roadmap.html)).

Esta es una buena noticia, ya que las nuevas versiones de Java poseen sistemas de recolección de basura más modernos, lo cual posiblemente ayudará a que nuestros servidores tengan un mejor rendimiento. Si quieres saber la versión de Java que usa tu servidor, puedes usar el comando `java -version` desde Linux; en caso de que uses un hosting de Minecraft con panel, tendrás que buscarlo en ese lugar (en caso de que uses Paper o alguna de sus bifurcaciones, igualmente te aparecerá una advertencia bastante notoria si usas Java 15 o menor en versiones actuales).

Ten en cuenta que gran parte de los plugins que utilizas actualmente soportan Java 11 o 16, a pesar de ello, antes de actualizar la versión de Java de tu servidor, se te recomienda que instales un servidor local con la versión más reciente de Paper junto a Java 16, el cual puedes encontrar [aqui](https://adoptopenjdk.net/releases.html?variant=openjdk16&jvmVariant=hotspot). Descarga la versión JRE de Java 16 segun el sistema operativo que uses. En caso encuentres algún error, debes informar al desarrollador del plugin correspondiente sobre este fallo.

### <div id="evitar-toda-costa">¿Qué cosas debo evitar a toda costa? ([inicio](#guia-de-optimizacion))</div>

#### <div id="plugins-remueven-objetos-suelo">**Plugins que remueven objetos del suelo**</div>

Son absolutamente innecesarios ya que pueden ser fácilmente reemplazados con [merge-radius](#merge-radius) y [alt-item-despawn-rate](#alt-item-despawn-rate) y francamente, son menos configurables que las configuraciones básicas de un servidor. Lo peor es que tienden a usar más recursos escaneando y removiendo objetos, por lo que causan más lag del que supuestamente deberían quitar.

#### <div id="plugins-stackean-mobs">**Plugins que stackean mobs**</div>

Es realmente difícil justificar el uso de uno. Stackear entidades que aparecieron naturalmente causa más lag que no stackearlas, debido a que el servidor estará constantemente intentando de aparecer más mobs. El único uso "aceptable" es para los spawners en servidores con grandes cantidades de spawners.

#### <div id="recargar-plugins">**Activar, desactivar o recargar plugins**</div>

Cualquier cosa que active, desactive o recargue plugins en tiempo de ejecución es extremadamente peligroso. Cargar un plugin de esa manera causa errores fatales con los datos de seguimiento y desactivar un plugin puede llevar a errores debido a alguna dependencia faltante.

El comando `/reload` sufre de los mismos problemas, debido a que Java no provee una forma soportada o segura de descargar o recargar código que ya ha sido cargado, por lo que lo ideal sería que reinicies tu servidor cada vez que añadas algún nuevo plugin y no intentes recargarlo jamás. Para más información, haz [click aquí](https://madelinemiller.dev/blog/problem-with-reload/).

### <div id="medir-rendimiento-servidor">¿Cómo mido el rendimiento de mi servidor? ([inicio](#guia-de-optimizacion))</div>

#### <div id="mspt">**mspt**</div>

Paper, en versiones recientes, ofrece el comando `/mspt` que te dirá cuánto tiempo le tómo al servidor calcular los ticks recientes. Si el primer y segundo valor están por debajo de `50`, ¡felicidades, tu servidor no tiene lag! Si el tercer valor está sobre `50`, entonces eso signififca que hubo al menos `1` tick que tomó más tiempo. Esto es complementamente normal y suele pasar en ocasiones, así que no te alteres.

#### <div id="timings">**timings**</div>

Una buena forma de saber por qué tu servidor tiene lag son los timings. Los timings son una herramienta que te permiten ver qué tareas están tomando más tiempo. Es la herramienta más básica de solución de problemas y si necesitas ayuda para resolver tus problemas de lag, probablemente te pidan tus timings.

Para obtener los timings de tu servidor, solamente debes ejecutar el comando `/timings paste` y hacer click en el enlace que se muestra. Puedes compartir est eenlace con otras personas para que así te puedan ayuar. También es fácil equivocarse al leerlo si no sabes qué estás haciendo. Aquí hay un [video tutorial de Aikar](https://www.youtube.com/watch?v=T4J0A9l7bfQ) (próximamente en español) que explica cómo leerlos.

#### <div id="spark">**spark**</div>

[Spark](https://github.com/lucko/spark) es un plugin que te permite medir el uso de CPU y memoria RAM de tus servidores. Puedes leer cómo usarlo en su [wiki](https://spark.lucko.me/docs/) (próximamente en español). También hay una guía en cómo encontrar las causas de picos de lag [aquí](https://spark.lucko.me/docs/guides/Finding-lag-spikes).

### <div id="configuraciones-optimizadas">Configuraciones optimizadas ([inicio](#guia-de-optimizacion))</div>

En esta sección se mostrarán opciones de los software de servidor que podrían ayudar a mejorar el rendimiento de tu servidor, y junto a cada opción se mostrará una breve descripción de lo que hace.

Recuerda que puedes encontrar cada cosa en tu editor de texto usando la herramienta de búsqueda usando el atajo de teclado **CTRL + F**, ahí introduces lo que necesites buscar.

#### <div id="server-properties">**server.properties**</div>

`network-compression-threshold`

- **Por defecto:** `256`

- **Optimizado:** Servidor independiente: `512` - BungeeCord: `-1`

- **Explicación:** Esta opción captura el tamaño de un paquete antes que el servidor lo comprima. Establecerlo en valores mayores puede ahorrar algunos recursos a costa de un consumo mayor de ancho de banda, y establecerlo a `-1` lo desactiva. Si tu servidor es una network con un proxy en la misma máquina o datacenter (con menos de 2ms de ping), deshabilitarlo (`-1`) tendrá beneficios, ya que usualmente las redes internas pueden manejar el tráfico no comprimido.

---

#### <div id="bukkit-yml">**bukkit.yml**</div>

<div id="spawn-limits"></div>

`spawn-limits`

- **Por defecto:**

```yaml
spawn-limits:
  monsters: 70
  animals: 10
  water-animals: 15
  water-ambient: 20
  ambient: 15
```

- **Optimizado:**

```yaml
spawn-limits:
  monsters: 12
  animals: 5
  water-animals: 5
  water-ambient: 2
  ambient: 0
```

- **Explicación:** Valores menores significan menos mobs. Menos mobs es menos lag en general, pero debes intentar balancearlo para mantener la calidad de juego, encontrar mobs en un mundo es una gran parte del juego. Con [per-player-mob-spawns](#per-player-mob-spawns) esos números básicamente representan `1:1` del límite de mobs en la categoría dada por cada jugador, así que la matemática de esto es `cantidad-jugadores*límite` donde "cantidad-jugadores" es la cantidad de jugadores conectados actual en el servidor.

`chunk-gc.period-in-ticks`

- **Por defecto:** `600`

- **Optimizado:** `400`

- **Explicación:** Esto decide qué tan frecuentemente los chunks desocupados son descargados. Valores menores significará menos chunks siendo tickeados, pero ir a valores muy bajos solamente estresará más a tu CPU.

`ticks-per`

- **Por defecto:**

```yaml
ticks-per:
  animal-spawns: 400
  monster-spawns: 1
  water-spawns: 1
  water-ambient-spawns: 1
  ambient-spawns: 1
```

- **Optimizado:**

```yaml
ticks-per:
  animal-spawns: 400
  monster-spawns: 10
  water-spawns: 40
  water-ambient-spawns: 40
  ambient-spawns: 40
```

- **Explicación:** Esto establece que tan frecuentemente (en ticks, 20 ticks equivale a 1 segundo) el servidor intenta aparecer ciertos tipos de entidades vivas. Mobs de agua/ambiente no necesitan aparece cada tick debido a que no son usualmente asesinados rápidamente. Para los monstruos: Incrementar un poco el tiempo entre apariciones no debería impactar en la tasa de apariciones, ni tampoco en granjas de mobs.

---

#### <div id="spigot-yml">**spigot.yml**</div>

<div id="view-distance"></div>

`view-distance`

- **Por defecto:** `default`

- **Optimizado:** `3`

- **Explicación:** Establecer la distancia de vista en un valor menor hará menos ticks de chunks, y con la opción [no-tick-view-distance](#no-tick-view-distance) activado podrás enviar más chunks a un jugador que realmente hagan tick. Si tienes problemas con el valor optimizado, puedes aumentarlo a `4` y aún así tendrás un rendimiento decente.

<div id="item-despawn-rate"></div>

`item-despawn-rate`

- **Por defecto:** `6000`

- **Optimizado:** `¿Valores menores?`

- **Explicación:** Una de las razones por la que no son necesarios plugins como ClearLagg es esta opción, ya que te permite remover objetos del suelo en un tiempo determinado de ticks. En el valor por defecto son 5 minutos, pero dependiendo del tipo del servidor que tengas debes bajarlo.

`arrow-despawn-rate`

- **Por defecto:** `1200`

- **Optimizado:** `300`

- **Explicación:** Similar a [item-despawn-rate](#item-despawn-rate), pero para las flechas lanzadas en general. Aquí lo estaríamos estableciendo en 15 segundos (300 ticks) en vez de 60 segundos (1200 ticks).

<div id="nerf-spawner-mobs"></div>

`nerf-spawner-mobs`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Cuando está activado, los mobs de los spawners no tendrán IA (no nadarán, atacarán o se moverán). Esto beneficia bastante a los TPS en servidores con granjas de mobs, pero también arruina la mecánica de éstas; en caso de que se quiera activar, también se recomienda activar [spawner-nerfed-mobs-should-jump](#spawner-nerfed-mobs-should-jump) para así no arruinar tanto la mecánica.

`mob-spawn-range`

- **Por defecto:** `8`

- **Optimizado:** `2`

- **Explicación:** Algunas personas discuten que establecer esto a uno menos que [view-distance](#view-distance) es bueno, pero valores menores como `2` o `3` te permitirán disminuir la cantidad de mobs en [spawn-limits](#spawn-limits).

`max-entity-collisions`

- **Por defecto:** `8`

- **Optimizado:** `2`

- **Explicación:** Limita la cantidad de colisiones que una entidad calculará al mismo tiempo. Establecer esto en el valor optimizado te permitirá mantener la habilidad del jugador para empujar mobs. Establecer esto a 0 completamente desactivaría las colisiones, haciendo que empujar mobs/jugadores sea imposible.

<div id="merge-radius"></div>

`merge-radius`

- **Por defecto:**

```yaml
merge-radius:
  exp: 3.0
  item: 2.5
```

- **Optimizado:**

```yaml
merge-radius:
  exp: 6.0
  item: 3.0
```

- **Explicación:** Esto decidirá la distancia entre los objetos que serán juntados, reduciendo la cantidad de items tickeando en el suelo. Juntarlos llevará a la ilusión de que los objetos han desaparecido, al combinarse en uno solo.

<div id ="entity-activation-range"></div>

`entity-activation-range`

- **Por defecto:**

```yaml
entity-activation-range:
  animals: 32
  monsters: 32
  raiders: 48
  misc: 16
  water: 16
  villagers: 32
  flying-monsters: 32
  villagers-work-immunity-after: 100
  villagers-work-immunity-for: 20
  villagers-active-for-panic: true
  tick-inactive-villagers: true
  wake-up-inactive:
    animals-max-per-tick: 4
    animals-every: 1200
    animals-for: 100
    monsters-max-per-tick: 8
    monsters-every: 400
    monsters-for: 100
    villagers-max-per-tick: 4
    villagers-every: 600
    villagers-for: 100
    flying-monsters-max-per-tick: 8
    flying-monsters-every: 200
    flying-monsters-for: 100
```

- **Optimizado:**

```yaml
entity-activation-range:
  animals: 16
  monsters: 16
  raiders: 48
  misc: 12
  water: 12
  villagers: 16
  flying-monsters: 32
  villagers-work-immunity-after: 100
  villagers-work-immunity-for: 20
  villagers-active-for-panic: true
  tick-inactive-villagers: false
  wake-up-inactive:
    animals-max-per-tick: 2
    animals-every: 1200
    animals-for: 40
    monsters-max-per-tick: 4
    monsters-every: 400
    monsters-for: 60
    villagers-max-per-tick: 1
    villagers-every: 600
    villagers-for: 20
    flying-monsters-max-per-tick: 1
    flying-monsters-every: 200
    flying-monsters-for: 60
```

- **Explicación:** Entidades pasado este rango serán menos tickeadas frecuentemente. Evita establecer esto muy bajo o podrías romper las mecánicas de los mobs.

`tick-inactive-villagers`

- **Por defecto:** `true`

- **Optimizado:** `false`

- **Explicación:** Desactivar esto evita que el servidor haga tick a los aldeanos que están fuera del rango de activación (esto ya es desactivado anteriormente en [entity-activation-range](#entity-activation-range)).. Las tareas de los aldeanos en versiones `1.14+` son muy pesadas.

`max-tick-time`

- **Por defecto:**

```yaml
max-tick-time:
  tile: 50
  entity: 50
```

- **Optimizado:**

```yaml
max-tick-time:
  tile: 1000
  entity: 1000
```

- **Explicación:** Establecer esto a los valores recomendados lo desactiva. Puedes leer el por qué debería estar desactivado desde [aquí](https://aikar.co/2015/10/08/spigot-tick-limiter-dont-use-max-tick-time/).

---

#### <div id="paper-yml">**paper.yml**

`optimize-explosions`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Algoritmo más rápido de explosión sin ningún impacto notable en la jugabilidad.

`max-auto-save-chunks-per-tick`

- **Por defecto:** `24`

- **Optimizado:** `8`

- **Explicación:** Hace más lento el guardado de mundo incremental al repartir la tarea en el tiempo incluso más, para así tener un mejor rendimiento promedio. Probablemente quieras establecer esto en un valor un tanto mayor si tienes más de `20 - 30` jugadores; si el guardado incremental no logra terminar a tiempo, entonces Bukkit automáticamente guardará los chunks restantes de una vez y comenzará el proceso nuevamente.

<div id="per-player-mob-spawns"></div>

`per-player-mob-spawns`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Por defecto los límites de mob están contados para el servidor entero, lo que significa que los mobs probablemente terminen distribuidos de forma desigual entre los jugadores conectados. Esta opción habilita los límites de mobs por jugador, haciendo así que cada jugador pueda obtener aproximadamente la misma cantidad de mobs independiente de la cantidad de jugadores en línea. Habilitar esta opción también te permite disminuir el valor por defecto de [spawn-limits](#spawn-limits) en `bukkit.yml`, ya que éstos están optimizados para límite de mobs para todo el servidor.

`max-entity-collisions`

- **Por defecto:** `8`

- **Optimizado:** `2`

- **Explicación:** Limita la cantidad de colisiones que una entidad calculará al mismo tiempo. Establecer esto en el valor optimizado te permitirá mantener la habilidad del jugador para empujar mobs. Establecer esto a 0 completamente desactivaría las colisiones, haciendo que empujar mobs/jugadores sea imposible.

`remove-corrupt-tile-entities`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Activo esto solamente si tienes errores en la consola relacionados con entidades de tipo tile, esta opción removerá esas tiles que causen errores en vez de ignorarlas.

`update-pathfinding-on-block-update`

- **Por defecto:** `true`

- **Optimizado:** `false`

- **Explicación:** Desactivar esto resulatará en que los mobs hagan una menor búsqueda de rutas, aumentando así el rendimiento, pero se verán como si fueran con retraso ya que solamente actualizarán su ruta cada 5 ticks (0.25 segundos).

`grass-spread-tick-rate`

- **Por defecto:** `1`

- **Optimizado:** `4`

- **Explicación:** Tiempo en ticks (20 ticks equivalen a 1 segundo) en el que el servidor intenta esparcir el pasto/micelio. No afecta a la jugabilidad en la mayoría de los casos.

`use-faster-eigencraft-redstone`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Sistema de redstone alternativo mucho más rápido. Reduce las actualizaciones de redstone redundates en cerca de un 95%.

`nether-ceiling-void-damage-height`

- **Por defecto:** `0`

- **Optimizado:** `127`

- **Explicación:** Si esta opción es mayor que `0`, los jugadores arriba del nivel de axis y establecido serán lastimados como si estuvieran cayendo al vacío. Esto prevendrá que los jugadores usen el techo del nether. El techo vanilla del nether es de 128 bloques de altura, por lo que este valor deberías colocarlo en `127`. Si modificaste la altura del nether de alguna manera, debes colocarlo usando la siguiente fórmula: `altura_nether - 1`.

`armor-stands-do-collision-entity-lookups`

- **Por defecto:** `true`

- **Optimizado:** `false`

- **Explicación:** Desactiva que los soportes de armadura (armor stands) tengan colisiones, es decir, no podrán ser empujados por entidades.

`prevent-moving-into-unloaded-chunks`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Previene que los jugadores entren a chunks no cargados (debido al lag), lo cual causa más lag. Establecer esto a `true` los devolverá a su última ubicación segura en caso de que suceda esto.

`container-update-tick-rate`

- **Por defecto:** `1`

- **Optimizado:** `3`

- **Explicación:** Cuando un inventario o cofre es abierto, Minecraft actualiza la vista cada tick por defecto para mostrar apropiadamente los cambios. Puedes disminuir qué tan frecuentemente se actualiza el inventario, pero no uses valores más altos que `3` o `4` ya que podría causar molestias visuales.

`fix-climbing-bypassing-cramming-rule`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Arregla una forma de evadir el límite de mobs en un solo bloque, que se basa en colocar mobs en distintos niveles. De esta forma evitamos que se junten grandes cantidades de entidades en un solo bloque.

`armor-stands-tick`

- **Por defecto:** `true`

- **Optimizado:** `false`

- **Explicación:** La única cosa que hace que los soportes de armaduras sean tickeados es para verificar si están siendo empujadas por agua. Desactivando esto, eres libre de liberar algunos procesos de tick y solamente perder una mecánica menor. Ten en cuenta que hay algunos plugins que dependen de esto y por lo tanto pueden haber incompatibilidades, pero solamente en casos muy específicos.

`non-player-arrow-despawn-rate`

- **Por defecto:** `-1`

- **Optimizado:** `20`

- **Explicación:** El tiempo en ticks (20 ticks equivale a 1 segundo) en el que las flechas disparadas por mobs desaparecen. En este caso desaparecerán en 1 segundo tras ser disparadas.

`creative-arrow-despawn-rate`

- **Por defecto:** `-1`

- **Optimizado:** `20`

- **Explicación:** El tiempo en ticks (20 ticks equivale a 1 segundo) en el que las flechas disparadas por jugadores en creativo desaparecen. En este caso desaparecerán en 1 segundo tras ser disparadas.

<div id="spawner-nerfed-mobs-should-jump"></div>

`spawner-nerfed-mobs-should-jump`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Hace que los mobs de spawners salten a pesar de ser nerfeados en la opción [nerf-spawner-mobs](#nerf-spawner-mobs), haciendo así que la mecánica de las granjas de spawners no se vea tan afectada sin afectar al rendimiento.

`mob-spawner-tick-rate`

- **Por defecto:** `1`

- **Optimizado:** `2`

- **Explicación:** Determina qué tan frecuente los spawners deben hacer tick para calcular el área disponible y aparecer nuevas entidades en el mundo. Esto ayuda bastante a servidores con grandes granjas con spawners.

<div id="enable-treasure-maps"></div>

`enable-treasure-maps`

- **Por defecto:** `true`

- **Optimizado:** `false`

- **Explicación:** Generar mapas del tesoro es extremadamente caro en temas de rendimiento y puede colgar un servidor si la estructura que está intentando localizar está fuera del mundo generado. Solamente es seguro activar esto si has pre-generado tu mundo y establecido el borde de Minecraft Vanilla.

`treasure-maps-return-already-discovered`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** El valor por defecto forza a los mapas generados recientemente a buscar estructuras inexploradas que usualmente están fuera del terreno generado. Estableciendo esto a `true` se hace que los mapas puedan llevar a estructuras ya descubiertas.

`game-mechanics.disable-chest-cat-detection`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Desactiva la mecánica que previene abrir un cofre cuando un gato está encima, algo bastante inútil y tierno al mismo tiempo.

`entity-per-chunk-save-limit`

- **Por defecto:**

```yaml
entity-per-chunk-save-limit:
  experience_orb: -1
  snowball: -1
  ender_pearl: -1
  arrow: -1
```

- **Optimizado:**

```yaml
entity-per-chunk-save-limit:
  experience_orb: 16
  arrow: 16
  dragonfireball: 8
  egg: 8
  ender_pearl: 16
  fireball: 8
  firework: 8
  largefireball: 8
  lingeringpotion: 8
  llamaspit: 8
  shulkerbullet: 8
  sizedfireball: 8
  snowball: 16
  spectralarrow: 8
  splashpotion: 8
  thrownexpbottle: 8
  trident: 8
  witherskull: 8
```

- **Explicación:** Limita la cantidad de proyectiles que pueden ser guardados en un chunk. Esto previene problemas que surgen cuando se disminuye la opción [view-distance](#view-distance), como jugadores lanzando cantidades masivas de bolas de nieve a un chunk no cargado que puede causar un potencial crasheo al intentar cargar ese chunk.

`despawn-ranges`

- **Por defecto:**

```yaml
despawn-ranges:
  soft: 32
  hard: 128
```

- **Optimizado:**

```yaml
despawn-ranges:
  soft: 28
  hard: 52
```

- **Explicación:** Reduce el rango de limpieza de mobs en segundo plano y permite que más aparezcan en áreas de tráfico de jugadores. Esto reduce el impacto en la jugabilidad por las apariciones reducidas de `bukkit.yml`. Valores ajustados para [view-distance](#view-distance) en `3`.

<div id="#alt-item-despawn-rate"></div>

`alt-item-despawn-rate.enabled`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Esta opción te permitirá desaparecer algunos objetos más rápido que en la tasa de desaparición por defecto. Puedes añadir cosas como cobblestone, netherrack, entre otras a la lista y hacer que desaparezcan después de ~20 segundos (400 ticks).

`hopper.disable-move-event`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** `InventoryMoveItemEvent` no es lanzado a menos que tengas un plugin activamente escuchando ese evento. Esto significa que deberías colocar esto en `true` solamente si no tienes plugins que modifiquen mecánicas de las tolvas o no te importa que no puedan escuchar este evento. Un ejemplo de plugin que utiliza esta mecánica sería los de protecciones.

`anti-xray.enabled`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Esconde menas de jugadores con X-ray. Para una configuración detallada de esta función, revisa los [ajustes recomendados de Stonar96](https://gist.github.com/stonar96/ba18568bd91e5afd590e8038d14e245e).

<div id="no-tick-view-distance"></div>

`viewdistances.no-tick-view-distance`

- **Por defecto:** `-1`

- **Optimizado:** `8`

- **Explicación:** Esto permite a los jugadores a ver más allá sin hacer tick a tantos chunks como el [view-distance](#view-distance) regular lo haría. Aunque no sea realmente pesado para el servidor, ten en cuenta que enviar más chunks afectará al consumo de ancho de banda.

---

#### <div id="tuinity-yml">**tuinity.yml**

`delay-chunkunloads-by`

- **Por defecto:** `5`

- **Optimizado:** `5`

- **Explicación:** Retrasa la descarga de chunks en el tiempo especificado (en segundos).

`lag-compensate-block-breaking`

- **Por defecto:** `true`

- **Optimizado:** `true`

- **Explicación:** Esta opción te permite determinar si romper bloques se ve impactado por los TPS. En Minecraft Vanilla, el tiempo en el que el bloque se rompe es determinado por los TPS del servidor, si los TPS son bajos, la ruptura del bloque toma más tiempo. Sin embargo, el cliente piensa que el bloque se rompe a 20 TPS, esto puede causar desacuerdos con el servidor sobre si el bloque está roto o no. La compensación de lag arregla esto calculando los checks del servidor con tiempo en vez de ticks.

`use-new-light-engine`

- **Por defecto:** `true`

- **Optimizado:** `true`

- **Explicación:** Esta opción habilita el nuevo sistema de iluminación llamado [Starlight](https://github.com/Spottedleaf/Starlight), el cual mejora enormemente el rendimiento. Esta opción usualmente viene activada por defecto en nuevas versiones de Tuinity y forks de este mismo, pero igualmente se lo recuerdo por si usted no lo tiene activado. Nota: Deshabilitarlo puede ser útil para debug y buscar incompatibilidades con algunos plugins que tal vez necesiten el sistema de iluminación de Minecraft Vanilla.

`world-settings`

- **Por defecto:**

```yaml
world-settings:
  default:
    spawn-limits:
      monsters: -1
      animals: -1
      water-ambient: -1
      water-animals: -1
      ambient: -1
  world: {}
```

- **Optimizado:**

```yaml
world-settings:
  default:
    spawn-limits:
      monsters: -1
      animals: -1
      water-ambient: -1
      water-animals: -1
      ambient: -1
  world: {}
```

- **Explicación:** Básicamente cumple la misma función que [spawn-limits](#spawn-limits), pero por mundos, por lo que lo mejor es dejarlo en `-1` para así ocupar los valores de `bukkit.yml`. En caso de que tengas algún mundo con algún tipo de mazmorra o algún mundo en el que quieras tener más mobs, solamente reemplaza `world` con el nombre del mundo y añades lo mismo que está en `default.spawn-limits` pero modificándolo con los valores deseados, puedes ver un ejemplo haciendo [click aquí](https://sourceb.in/Bi06rQ5FIN).

---

#### <div id="purpur-yml">**purpur.yml**</div>

`tps-catchup`

- **Por defecto:** `true`

- **Optimizado:** `false`

- **Explicación:** La recuperación de TPS hace que tu servidor se ponga en marcha después de las caídas de tps. Si tu servidor está constantemente funcionando a más de `50` mspt lo más probable es que vea una mejora en el rendimiento debido a que el servidor no tratará de promediar los tps a `20` al pasar de `20`. Toma nota que algunos plugins pueden no esperar que esta característica esté deshabilitada.

`use-alternate-keepalive`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Sistema alternativo de paquetes keepalive que hace que los jugadores con mala conexión no sobrepasen el tiempo de espera de forma tan regular. Es incompatible con el funcionamiento de TCP Shield.

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

`villager.brain-ticks`

- **Por defecto:** `1`

- **Optimizado:** `2`

- **Explicación:** Esta opción establece qué tan frecuentemente sus cerebros harán ticks (lo que los hace trabajar). Por defecto esta opción hace tick en todos los ticks, cantidades mayores pueden reducir el lag causado pero tendrá un efecto muy notorio de retraso o poca respuesta en valores sobre `3` por lo que no es muy recomendado.

`villager.lobotomize`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Esto le remueve la IA de los aldeanos que no pueden encontrar un camino (no pueden moverse), pero no desactiva el intercambio con ellos por lo que no afecta mucho.

`mobs.dolphin.disable-treasure-searching`

- **Por defecto:** `false`

- **Optimizado:** `true`

- **Explicación:** Evita que los delfines hagan una búsqueda de estructuras similar a la de los mapas del tesoro ([enable-treasure-maps](enable-treasure-maps)).

`mobs.zombie.aggressive-towards-villager-when-lagging`

- **Por defecto:** `true`

- **Optimizado:** `false`

- **Explicación:** Los zombies paran de atacar aldeanos cuando los TPS están muy bajos. Esto le ahorra al servidor precioso tiempo calculando rutas para los zombies que no están persiguiendo jugadores.
