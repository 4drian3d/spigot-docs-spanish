# <div id="plugin-maven-intellij">Plugin usando Maven en IntelliJ IDEA <font size="2px">([original](https://www.spigotmc.org/wiki/creating-a-plugin-with-maven-using-intellij-idea/))</font></div>

Maven es una herramienta de gestión y comprensión de proyectos de software, cual le pertenece a Apache.
El principal objetivo de Maven es facilitar el proceso de compilado y construcción. Y eso es exactamente lo que queremos.

En este tutorial se mostrará cómo crear un plugin usando la API de Spigot usando Maven en IntelliJ IDEA.

## Contenidos

- [Pre-requisitos](#pre-requisitos)
- [Creando un nuevo proyecto](#nuevo-proyecto)
- [Configurando el pom.xml](#configurando-pom)
- [Creando la clase principal del plugin](#clase-principal)
- [Creando el archivo plugin.yml](#plugin-yml)
- [Compilando el plugin](#compilando)


### <div id="pre-requisitos">Pre-requisitos <font size="2px">([inicio](#plugin-maven-intellij))</font></div>

- Estaremos usando [IntelliJ IDEA](https://www.jetbrains.com/idea/) para desarrollar el plugin.
- Será necesario el [Kit de desarrollo de Java (JDK)](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html).
- Debido que usamos Maven, necesitaremos un nombre de dominio (ej: `ejemplo.com`). Si no tienes uno, puedes usar tu perfil de [Github](https://github.com/) o tu correo electrónico como será mencionado posteriormente.
- Conocimientos básicos de Java (en caso de que no tengas ningún conocimiento se te recomienda el curso de [pildorasinformaticas](https://www.youtube.com/c/pildorasinformaticas) de [Java desde cero](https://www.youtube.com/playlist?list=PLU8oAlHdN5BktAXdEVCLUYzvDyqRQJ2lk)).

### <div id="nuevo-proyecto">Creando un nuevo proyecto <font size="2px">([inicio](#plugin-maven-intellij))</font></div>

Si inicias IntelliJ IDEA (2020.3) por primera vez, verás algo como esto:

![Inicio de IntelliJ IDEA 2020.3 (Primera vez)](https://i.imgur.com/W3gnueT.png)

Haz click en "New Project". Si ya has abierto IntelliJ IDEA anteriormente, ve a la barra de navegación superior de Projects, al costado de "Search projects​" verás el botón "New Project".

![Inicio de IntelliJ IDEA 2020.3](https://i.imgur.com/7buyeW8.png)

Una vez presionado el botón "New Project", se abrirá una nueva ventana en la cual te aparecerá algo similar a lo siguiente:

![Nuevo proyecto en IntelliJ IDEA 2020.3](https://i.imgur.com/0ypqU1d.png)

En ese lugar, en el costado izquierdo debes buscar "Maven" y presionarlo, posteriormente haz click en el menú desplegable llamado "Project SDK". Navega hacia al JDK que tienes instalado, el cual para funcionar en gran parte de las versiones es el `1.8` como se muestra en la siguiente imagen:

![Proyecto maven en IntelliJ IDEA 2020.3](https://i.imgur.com/jzljpOu.png)

Posteriormente, en la parte inferior le damos al botón "Next" de color azul.

En este paso eligirás tanto la ubicación de creación de la carpeta del proyecto de tu plugin, asi como los datos de tu plugin. En mi caso la carpeta del proyecto de mi plugin será guardada en `~/General/Development/MiPlugin`, para cambiar de carpeta haz click en el ícono que parece una carpeta. En "Name" puedes cambiar el nombre de tu proyecto, en este caso será `MiPlugin`.

![Escoger ruta y nombre de proyecto Maven en IntelliJ IDEA 2020.3](https://i.imgur.com/wKV3ZCn.png)

Ahora, necesitamos el nombre de tu dominio, *¡no uses un dominio que no te pertenece!*

Apreta en la flecha del apartado "Artifact Coordinates", posteriormente se desplegarán tres casillas, las cuales serán descritas a continuación:

En `GroupId` introduce el nombre de tu dominio, pero invertido. En mi caso `zafire.org` sería `org.zafire`, *este dominio es ***en mi caso****, ya que soy propietario de este mismo. Si usas [Github](https://github.com) puedes usar `io.github.[tu nombre de usuario]`, también puedes usar tu correo electrónico `[tu nombre de usuario]@gmail.com` que se convertiría en `com.gmail.[tu nombre de usuario]`. Finalmente, escribe la versión de tu proyecto, la cual en mi caso seguirá el [control de versiones semántico](https://semver.org/), que es escrito como MAYOR.MENOR.PARCHE (ej: `0.0.1` o `9.12.4`)

En `ArtifactId` es el nombre de tu plugin. En este caso el nombre será `MiPlugin`. Si tu plugin se llama `AwesomeChat`, debes poner ese nombre en `ArtifactId`, obviamente.

![Creación de proyecto Maven en IntelliJ IDEA 2020.3](https://i.imgur.com/F0aayvR.png)​

Algo así debería verse, una vez hecho eso presiona el botón "Finish" de color azul en la barra inferior para comenzar con lo más interesante.

### <div id="configurando-pom">Configurando el pom.xml <font size="2px">([inicio](#plugin-maven-intellij))</font></div>

POM significa "Project Object Model", en español "Modelo de objeto de proyecto". Ese es el lugar en donde añadimos nuestras dependencias y otras cosas.

Ahora mismo se debería haber abierto el archivo `pom.xml` de tu proyecto de Maven. Para decirle a Maven que queremos hacer un plugin de Spigot y que queremos usar la API para eso, necesitamos añadir unas cuantas cosas a este archivo. Necesitamos decirle a Maven desde dónde obtener la API, y después debemos decirle a Maven que nuestro plugin depende de la API de Spigot. Puedes hacer eso copiando y pegando esto en tu archivo POM.

```xml
<repositories>
    <repository>
        <id>spigot-repo</id>
        <url>https://hub.spigotmc.org/nexus/content/repositories/snapshots/</url>
    </repository>
</repositories>

<dependencies>
    <dependency>
        <groupId>org.spigotmc</groupId>
        <artifactId>spigot-api</artifactId>
        <version>1.16.5-R0.1-SNAPSHOT</version>
        <scope>provided</scope>
    </dependency>
</dependencies>
```

Obviamente puedes cambiar la versión de la API. Un plugin creado con la API de la `1.12.2` tendrá `<version>1.12.2-R0.1-SNAPSHOT</version>`, la `1.13.2` tendría `<version>1.13.2-R0.1-SNAPSHOT</version>`, la `1.8.8` sería `<version>1.8.8-R0.1-SNAPSHOT</version>` y así de esa forma. Sin embargo, hay algunas diferencias entre versiones de la API de las cuales debes estar atento.

Algo así debería verse:

![Proyecto Maven de SpigotMC en IntelliJ IDEA 2020.3](https://i.imgur.com/PDoN5iF.png)

Finalmente, debemos los cambios a Maven. 

En IntelliJ IDEA esto puede ser hecho presionando el ícono que aparece en el lado derecho, el cual se ve de esta forma: ![Recargar proyecto Maven en IntelliJ IDEA 2020.3](https://i.imgur.com/sLohass.png)

### <div id="clase-principal">Creando la clase principal del plugin <font size="2px">([inicio](#plugin-maven-intellij))</font></div>

En el lado izquierdo, descontrae la pestaña desde el botón que tiene el nombre de tu proyecto, en este caso es `MiPlugin`. Navega a `MiPlugin -> src -> main -> java`, haz click derecho en `java -> New -> Package`.

Aquí debes introducir el nombre del paquete de tu plugin, el cual es tu dominio invertido en conjunto con el nombre de tu plugin. En el caso de `MiPlugin` sería `org.zafire.miplugin`, siguiendo las [Convenciones de Nombramiento de Java (JNC)](https://www.oracle.com/java/technologies/javase/codeconventions-namingconventions.html).

Posteriormente, vamos a crear la clase principal dando click derecho en tu nuevo paquete, luego `New -> Java Class` y te aparecerá un cuadro el cual debes rellenar con el nombre de la clase principal de tu plugin, que por lo general es el nombre del plugin por lo que en este caso es `MiPlugin`. Teóricamente, puedes escoger cualquier nombre pero lo recomendado es que sea el nombre de tu plugin para así evitar confusiones. Algo así debería verse tu pantalla:

![Creación de clase principal en Proyecto Maven en IntelliJ IDEA 2020.3](https://i.imgur.com/pHk8Yif.png)

Finalmente presiona la tecla ***ENTER*** para crear la clase y comenzar a programar.

La clase principal de tu plugin debe extender [JavaPlugin](https://hub.spigotmc.org/javadocs/spigot/org/bukkit/plugin/java/JavaPlugin.html), por lo que en vez de lucir así:

```java
package org.zafire.miplugin;

public class MiPlugin {

}
```

Debe lucir de esta manera:

```java
package org.zafire.miplugin;

import org.bukkit.plugin.java.JavaPlugin;

public class MiPlugin extends JavaPlugin {
    
}
```

IntelliJ IDEA te sugerirá la clase [JavaPlugin](https://hub.spigotmc.org/javadocs/spigot/org/bukkit/plugin/java/JavaPlugin.html) una vez comiences a excribir el nombre, presiona la tecla ***ENTER*** para automáticamente completar el nombre de la clase. Esto, además añadirá el importe requerido para que funcione correctamente tu clase (`import org.bukkit.plugin.java.JavaPlugin`).

La función de auto-completado es extremadamente útil y se recomienda usarla cada vez que se pueda. También puedes añadir importes requeridos para clases después de haberlas escrito, presionando la tecla ***ALT + ENTER*** con el puntero sobre el nombre de la clase que da el error. IntelliJ IDEA te sugerirá métodos para resolver tu problema, en este caso debes añadir el importe de la clase.

Ahora vamos a crear nuestros primeros dos métodos: `onEnable()` y `onDisable()`. Estos realmente no son requeridos, ya que ya se encuentran en la clase [JavaPlugin](https://hub.spigotmc.org/javadocs/spigot/org/bukkit/plugin/java/JavaPlugin.html) que estamos extendiendo, aun así algunas personas creen que son totalmente requeridos. Enviemosle un mensaje a la consola cuando estos métodos sean llamados, lo cual lo haremos con el Logger de la API de Bukkit.

```java
package org.zafire.miplugin;

import org.bukkit.plugin.java.JavaPlugin;

public class MiPlugin extends JavaPlugin {

    @Override
    public void onEnable() {
        getLogger().info("¡onEnable() ha sido llamado!");
    }

    @Override
    public void onDisable() {
        getLogger().info("¡onDisable() ha sido llamado!");
    }

}
```

Podemos añadir mucho más de lo que hemos añadido hasta ahora, pero primero crearemos nuestro archivo [plugin.yml](https://github.com/ZafireStudios/spigot-docs-spanish/blob/main/development/PLUGIN-YML.md), desde el cual el servidor Spigot lee más información sobre nuestro plugin.


### <div id="plugin-yml">Creando el archivo plugin.yml <font size="2px">([inicio](#plugin-maven-intellij))</font></div>

Ya casi podemos compilar nuestro plugin, pero primero necesitamos un archivo más, llamado `plugin.yml`. Este archivo presenta nuestro plugin al servidor Spigot.

A la izquierda, haz click en `resources -> New -> File` y en el recuadro que aparezca, colocamos `plugin.yml` como nombre de archivo.

![Creación de plugin.yml en proyecto Maven en IntelliJ IDEA 2020.3](https://i.imgur.com/pFyLxnI.png)​

Posteriormente, presiona la tecla ***ENTER*** para confirmar la creación.

Hay algunas cosas que el servidor Spigot necesita conocer sobre el plugin:

- El nombre del plugin.
- La versión de este plugin.
- La ruta hacia la clase principal (creada anteriormente).
- La versión de la API (`api-version`) que tu plugin debe usar (la última es la `1.16`).

Algo así debería verse:
![Archivo plugin.yml en proyecto Maven en IntelliJ IDEA 2020.3](https://i.imgur.com/yT0tg05.png)
​
Donde dice `name` va el nombre de tu plugin, puede ser cualquier cosa en realidad, pero es bastante recomendable que sea el nombre de tu plugin.

En la parte donde dice `version` colocamos `${project.version}` para indicar que debe obtener la versión desde nuestro POM, esto es para evitar que tengamos que cambiar la versión en ambos lugares al actualizar. Para ello, debemos añadir lo siguiente a nuestro POM:

```xml
<build>
    <finalName>${project.artifactId}</finalName>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.8.1</version>
            <configuration>
                <source>8</source>
                <target>8</target>
            </configuration>
        </plugin>
    </plugins>
    <resources>
        <resource>
            <directory>${project.basedir}/src/main/resources</directory>
            <includes>
                <include>plugin.yml</include>
            </includes>
            <filtering>true</filtering>
        </resource>
    </resources>
</build>
```

Para más información sobre el archivo `plugin.yml`, te recomendamos visitar nuestra documentación en español en: [development/PLUGIN-YML.md](https://github.com/ZafireStudios/spigot-docs-spanish/blob/main/development/PLUGIN-YML.md)

### <div id="compilando">Compilando el plugin <font size="2px">([inicio](#plugin-maven-intellij))</font></div>

Finalmente es hora de compilar y empaquetar nuestro plugin en un archivo JAR.

Como último detalle, el cual olvidé anteriormente, recomiendo colocar soporte de `UTF-8` a el POM para que así se puedan usar letras del español, como la ñ o los tíldes.
Esto se haría añadiendo `<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>` y quedaría de la siguiente forma:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.zafire</groupId>
    <artifactId>MiPlugin</artifactId>
    <version>0.0.1</version>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

    <repositories>
        <repository>
            <id>spigot-repo</id>
            <url>https://hub.spigotmc.org/nexus/content/repositories/snapshots/</url>
        </repository>
    </repositories>

    <dependencies>
        <dependency>
            <groupId>org.spigotmc</groupId>
            <artifactId>spigot-api</artifactId>
            <version>1.16.5-R0.1-SNAPSHOT</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>

    <build>
        <finalName>${project.artifactId}</finalName>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <source>8</source>
                    <target>8</target>
                </configuration>
            </plugin>
        </plugins>
        <resources>
            <resource>
                <directory>${project.basedir}/src/main/resources</directory>
                <includes>
                    <include>plugin.yml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>
    </build>

</project>
```

Posteriormente, debes ir al costado derecho de IntelliJ IDEA y deberías ver una pequeña pestañita llamada "Maven". Presiónala para expandirla, luego apreta la flecha que está al costado del nombre de tu plugin para abrir un menú desplegable y despliega nuevamente en el apartado "Lifecycle". Ahora solamente debes hacer doble click en "package" y tu plugin será compilado y empaquetado, listo para ser usado en un servidor.

![Compilar plugin en proyecto Maven en IntelliJ IDEA 2020.3](https://i.imgur.com/Y5B6Fvz.png)

Puedes encontrar tu plugin empaquetado en formato JAR dentro de la carpeta raíz de tu proyecto llamada "target"​:

![Finalizada la compilación de plugin en proyecto Maven en IntelliJ IDEA 2020.3](https://i.imgur.com/1lHmChz.png)

***¡Haz terminado!***

Ahora puedes iniciar tu servidor y tu plugin te dirá cuando el método `onEnable()` es llamado, asi como también el método `onDisable()`.

​![Llamada a onEnable()](https://i.imgur.com/5SOLQWf.png)

​![Llamada a onDisable()](https://i.imgur.com/Gn6U3vX.png)
