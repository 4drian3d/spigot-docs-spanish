# <div id="plugin-yml">Archivo plugin.yml <font size="2px">([Fuente original](https://www.spigotmc.org/wiki/plugin-yml/))</font></div>

El `plugin.yml` es un archivo creado para contener información acerca de tu plugin. Sin este archivo tu plugin no funcionará. Consiste en un conjunto de atributos, cada uno definido por una nueva línea sin indentación. Todos los atributos son `case-sensitive`, por lo que hay que fijarse en las minúsculas y mayúsculas. Atributos en **negrita** son requeridos, atributos en ***itálica*** no lo son.

## Contenidos

- [Atributos requeridos](#atributos-requeridos)
- [Atributos opcionales](#atributos-opcionales)
- [Atributos de comandos](#atributos-comandos)
- [Atributos de permisos](#atributos-permisos)
- [Ejemplo de archivo](#ejemplo-archivo)
- [Plugin Annotations](#plugin-annotations)

### <div id="atributos-requeridos">Atributos requeridos <font size="2px">([inicio](#plugin-yml))</font></div>

Hay tres atributos que son requeridos por el archivo `plugin.yml` para que sea considerado válido.

**main**

- Este atributo apunta a la clase de tu plugin que extiende [JavaPlugin](https://hub.spigotmc.org/javadocs/spigot/org/bukkit/plugin/java/JavaPlugin.html).
- *Debe* contener el nombre completo del paquete, incluyendo el nombre de la clase.

*Ejemplo:*

```yaml
main: org.spigotmc.testplugin.Test
```

**name**

- Este atributo es el nombre de tu plugin.
- *Debe* consistir completamente de caracteres alfanuméricos y guiones bajos (`a-z,A-Z,0-9, _`).
- Usado para determinar el nombre de la carpeta de tu plugin.
- Es una buena práctica nombrar tu JAR igual que este atributo (ej: `MiPlugin.jar`)

*Ejemplo:*

```yaml
name: MiPlugin
```

**version**

- La versión de tu plugin.
- El formato más común de versiones es el [control de versiones semántico](https://semver.org/), que es escrito como MAYOR.MENOR.PARCHE (ej: `1.4.1` o `9.12.4`)

*Ejemplo:*

```yaml
version: 1.4.1
```

### <div id="atributos-opcionales">Atributos opcionales <font size="2px">([inicio](#plugin-yml))</font></div>

Estos atributos no son obligatorios a comparación de los anteriores y tu plugin funcionará sin problemas aunque no los apliques (solamente en algunos casos).

***description***

- Una descripción simple que describa la funcionalidad que tu plugin provee.
- La descripción puede tener múltiples líneas.

*Ejemplo:*

```yaml
description: "¡Este plugin contiene tantas cosas, que no puede ser descrito!"
```

***api-version***

- La versión de la API que se desea usar.
- Las versiones disponibles son `1.13`, `1.14`, `1.15` y `1.16`.
- Esto señalará al servidor que tu plugin ha sido programado con una versión específica de servidor en mente, y que no debería aplicar ninguna tipo de medida para compatibilidad con versiones anteriores. Como resultado tú también te tendrás que asegurar que has programado tu plugin para que pueda leer configuraciones antiguas, datos, entre otras cosas. Cada versión del servidor decide cómo la compatibilidad es manejada, versiones desconocidas o futuras evitarán que el plugin se active. A partir de la versión `1.14`, la `api-version` de la `1.13` todavía está permitida; sin embargo, las versiones futuras pueden dejar de ser compatibles con versiones anteriores según esta versión.

*Ejemplo:*

```yaml
api-version: 1.16
```

***load***

- Explicítamente señala cuando el plugin debe ser cargado. Si no es puesto, por defecto será `POSTWORLD`.
- Tiene dos posibles valores: `STARTUP` y `POSTWORLD`.

*Ejemplo:*

```yaml
load: POSTWORLD
```

***author***

- Identifica únicamente al que desarrolló el plugin.
- Usado en algunos mensajes de error en el servidor, para proveer de información a quién contactar cuando un error ocurre.
- Se recomienda que sea un identificador de [SpigotMC.org](https://spigotmc.org/) o una dirección de correo electrónico.

*Ejemplo:*

```yaml
author: md_5
```

***authors***

- Te permite listar a múltiples autores, en caso de que sea un proyecto colaborativo. Ver *author*.
- Debe ser en formato válido de [Lista YAML](https://en.wikipedia.org/wiki/YAML#Lists).

*Ejemplo:*

```yaml
authors: [md_5, thinkofdeath]
```

***website***

- El sitio web del plugin o del autor.
- Si no se tiene un sitio web dedicado, un enlace a la página donde el plugin está listado es recomendado.

*Ejemplo:*

```yaml
website: www.spigotmc.org
```

***depend***

- Una lista de plugins que tu plugin necesita obligatoriamente para funcionar.
- Debe ser en formato válido de [Lista YAML](https://en.wikipedia.org/wiki/YAML#Lists).
- Usa el atributo **name** del plugin requerido para especificar la dependencia.
- Si algún plugin listado aquí no es encontrado, tu plugin fallará al cargar.
- Si múltiples plugins se listan unos a otros como dependencia, de modo que no haya complementos sin una dependencia cargable, todos fallarán al cargar.

*Ejemplo:*

```yaml
depend: [WorldEdit, Towny]
```

***prefix***

- El nombre usado para registrar en la consola en vez del nombre del plugin.

*Ejemplo:*

```yaml
prefix: Testing
```

***softdepend***

- Una lista de plugins que son requeridos por tu plugin para tener funcionalidad completa.
- Los valores deben estar en un formato válido de [Lista YAML](https://en.wikipedia.org/wiki/YAML#Lists).
- Usa el atributo **name** del plugin requerido para especificar la dependencia.
- Tu plugin cargará después de los plugins listados aquí.
- Las dependencias suaves circulares se cargan arbitrariamente.

*Ejemplo:*

```yaml
softdepend: [Essentials, AnotherPlugin]
```

***loadbefore***

- Una lista de plugins que deben ser cargados *después* que tu plugin.
- Los valores deben estar en un formato válido de [Lista YAML](https://en.wikipedia.org/wiki/YAML#Lists).
- Tratado como si los plugins listados fueran dependencias suaves del plugin.
- Usa el atributo **name** del plugin requerido para especificar la dependencia.
- Tu plugin se cargará *antes* que los plugins listados aquí.
- Las dependencias suaves circulares se cargan arbitrariamente.

*Ejemplo:*

```yaml
loadbefore: [OnePlugin, AnotherPlugin]
```

***commands***

- El nombre de un comando que un plugin desea registrar, asi como también una lista opcional de atributos de comando.
- El nombre del comando no debe contener el `/` requerido para ejecutar un comando.

*Ejemplo:*

```yaml
commands:
 flagrate:
 [atributos de comando opcionales]
```

***permissions***

- Permiso que el plugin desea registrar. Cada nodo representa un permiso a registrar.
- Cada permiso puede tener múltiples atributos.
- El registro del permisos es opcional, ya que puede ser realizado desde código.
- El registro de permisos te permite establecer descripciones, si es por defecto o no y relaciones padre-hijo.
- Los nombres de permisos deben mantenerse en el estilo de `<nombreplugin>.[categoría].[categoría].<permiso>`

*Ejemplo:*

```yaml
permissions:
 inferno.*:
 [atributos de permiso opcionales]
 inferno.flagate:
 [atributos de permiso opcionales]
```

### <div id="atributos-comandos">Atributos de comandos <font size="2px">([inicio](#plugin-yml))</font></div>

Un bloque de comando empieza con el nombre del comando, y después tiene una lista de atributos opcionales.

***description***

- Una breve descripción de lo que el comando hace.
- Puede ser usado en conjunto con `/help`.

*Ejemplo:*

```yaml
description: Una descripción simple.
```

***aliases***

- Alterna nombres de comandos que un usuario tal vez debería usar.
- Puedes especificar ninguno, uno o muchos.
- Si especificas más de un alias, debe estar en un formato válido de [Lista YAML](https://en.wikipedia.org/wiki/YAML#Lists).

*Ejemplos:*

```yaml
aliases: [foobar, fubar]
```

```yaml
aliases: foobar
```

***permission***

- El nodo de permiso más básico requerido para usar el comando.
- Este nodo de permiso puede ser usado para determinar si un usuario debería poder ver este comando.

*Ejemplo:*

```yaml
permission: test.foo
```

***permission-message***

- Un mensaje que es mostrado cuando el usuario no tiene el permiso requerido para usar el comando.
- Puedes usar comillas vacías para indicar que nada debe ser mostrado.

*Ejemplo:*

```yaml
permission-message: "¡No tienes permiso para usar este comando!"
```

***usage***

- Una pequeña descripción que indica cómo usar el comando.
- Mostrado a cualquiera que haya usado el comando, en el momento en el que el manejador de comandos del plugin (Típicamente el método [CommandExecutor#onCommand()](https://hub.spigotmc.org/javadocs/spigot/org/bukkit/command/CommandExecutor.html)) no retorna `true`.
- `<command>` es un macro reemplazado por el comando que se haya usado, donde quiera que ocurra.
- Para usar la string `"Uso:"` (ej: `usage: Uso: /test command`), envuelve el texto después de `usage:` con comillas dobles (ej: `usage: "Uso: /test command"`).

*Ejemplo:*

```yaml
usage: "Uso: /<command> [test|stop]"
```

### <div id="atributos-permisos">Atributos de permisos <font size="2px">([inicio](#plugin-yml))</font></div>

Un bloque de permiso comienza con el nombre del permiso y es seguido por nodos de atributos.

***description***

- Una descripción corta de lo que este permiso permite.
- Permite el acceso mediante programación y ayuda a los administradores del servidor.

*Ejemplo:*

```yaml
description: Te permite usar el comando /test.
```

***default***

- Establece el valor por defecto del permiso.
- Si nodos no existen, el permiso por defecto es sólo para *op*.
- Por defectos permitidos son: *true, false, op, not op*

*Ejemplo:*

```yaml
default: true
```

- El defecto `true` siempre le dará el permiso al jugador.
- El defecto `false` no dará permiso al jugador.
- El defecto `op` será `true` si el jugador es operador.
- El defecto `not op` hace lo opuesto a lo anterior.

***children***

- Te permite establecer el permiso hijo para el permiso. Los nodos del hijo son nombres de permisos.
- Cada nodo del hijo debe ser `true` o `false`.
- Un nodo hijo en `true` hereda el permiso padre.
- Un nodo hijo en `false` hereda el permiso padre inverso.
- También puede contener otros nodos de permiso.

*Ejemplo:*

```yaml
permissions:
  test.all:
    children:
      test.foo: true
      test.bar: false
```

### <div id="ejemplo-archivo">Ejemplo de archivo <font size="2px">([inicio](#plugin-yml))</font></div>

Aquí se muestra un ejemplo de lo que sería un `plugin.yml` válido:

```yaml
main: org.spigotmc.annotationtest.Test
name: TestPlugin
version: '1.0'
description: Un plugin de prueba
load: POSTWORLD
author: md_5
website: spigotmc.org
prefix: Testing
depend:
- WorldEdit
- Towny
softdepend:
- FAWE
loadbefore:
- Essentials
commands:
  foo:
   description: Comando foo
   aliases:
  - foobar
   - fubar
   permission: test.foo
   permission-message: "¡No tienes permiso!"
   usage: /<command> [test|stop]
permissions:
  test.foo:
   description: Permite el comando foo
   default: op
  test.*:
   description: Permiso comodín
   default: op
   children:
     test.foo: true
```

### <div id="plugin-annotations">Plugin Annotations <font size="2px">([inicio](#plugin-yml))</font></div>

Si no quieres lidiar con el problema de crear un `plugin.yml` para cada proyecto, existe esta peqieña herramienta llamada Plugin Annotations. Es un simple procesador de anotaciones que automáticamente genera un `plugin.yml` correcto para ti.

Para usarlo simplemente debes añadirlo como dependencia a tu proyecto.

```xml
<dependency>
  <groupId>org.spigotmc</groupId>
  <artifactId>plugin-annotations</artifactId>
  <version>1.1.0-SNAPSHOT</version>
  <scope>provided</scope>
</dependency>
```

Plugin Annotations usa el mismo repositorio donde la `spigot-api` está alojada.

```xml
<repositories>
  <repository>
    <id>spigot-repo</id>
    <url>https://hub.spigotmc.org/nexus/content/repositories/snapshots/</url>
  </repository>
</repositories>
```

Más información sobre esta herramienta puede ser encontrada en su [wiki](https://www.spigotmc.org/wiki/spigot-plugin-yml-annotations/).
