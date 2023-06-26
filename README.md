# Creacion y configuracion del proyecto con flyway

Para comenzar con el proyecto, se puede usar una arquitectura cualquiera de Maven, sin embargo, la que recomeinda flyway en su documentacion es: `maven-archetype-quickstart`. Entonces para iniciar el proyecto desde la consola del sistema (Ya deber tener maven en las variables del sistema) es:
``` Maven
mvn archetype:generate -B ^
    -DarchetypeGroupId=org.apache.maven.archetypes ^
    -DarchetypeArtifactId=maven-archetype-quickstart ^
    -DarchetypeVersion=1.4 ^
    -DgroupId=[Grupo del proyecto, ejm (com.avvale)] ^
    -DartifactId=[nombre del proyecto] ^
    -Dversion=1.0-SNAPSHOT
```
Adentro del archivo `pom.xml` se debe de agregar la siguiente configuracion:
``` XML
<project xmlns="...">
    ...
    <build>
        <plugins>
            <plugin>
                <groupId>org.flywaydb</groupId>
                <artifactId>flyway-maven-plugin</artifactId>
                <version>9.8.1</version>
                <configuration> </configuration>
                <dependencies>
                    <dependency>
                        <!--Aqui van las dependencias del proyecto-->
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
    </build>
</project>
```
En lugar de `<!--Aqui van las dependencias del proyecto-->` Se deben colocar 2 dependencias necesarias:
1. Gestor de la base de datos, este se encuentra en la página de [Flyway - Soported Databses](https://documentation.red-gate.com/fd/supported-databases-184127454.html)
2. Gestor de coneccion con la base de datos que vas a utilizar, este se puede encontrar en [Maven Repositorie](https://mvnrepository.com/)

Aquí hay un ejemplo de como se veria para una base de datos MySQL Server
```Xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.avvale</groupId>
  <artifactId>FlywayLocal</artifactId>
  <version>1.0</version>
  <packaging>jar</packaging>

  <name>FlywayLocal</name>
  <url>http://maven.apache.org</url>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
    </properties>

  <dependencies>


      <dependency>
          <groupId>junit</groupId>
          <artifactId>junit</artifactId>
          <version>4.13.1</version>
          <scope>test</scope>
      </dependency>
  </dependencies>
  <build>
        <plugins>
            <plugin>
                <groupId>org.flywaydb</groupId>
                <artifactId>flyway-maven-plugin</artifactId>
                <version>9.8.1</version>
                <configuration>
                </configuration>
                <dependencies>
                    <dependency>
                        <groupId>org.flywaydb</groupId>
                        <artifactId>flyway-mysql</artifactId>
                        <version>9.20.0</version>
                    </dependency>
                    <dependency>
                        <groupId>mysql</groupId>
                        <artifactId>mysql-connector-java</artifactId>
                        <version>8.0.12</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
    </build>
</project>
```
Las variables de configuracion más importates, como lo son la URL, Nombre de usuario, Contraseña y el esquema a modificar deben ser agregados en un archivo de nombre `flyway.conf`, como se muestra acontunuacion:
``` conf
flyway.user=Nombre de usuario
flyway.password=Contraseña de ususario
flyway.url=url de la base de datos
flyway.schemas=Nombre de los esquemas a modificar (son separados con comas [squema1,squema2])
```

El usuario configurado debe tener permisos de CRUD

Para saber como se debe colocar la url de la base de datos, por favor verificar en [Flyway - Soported Databses](https://documentation.red-gate.com/fd/supported-databases-184127454.html)

Si deseas que la BBDD cree los squemas que no existen:
1. El usuario debe tener permisos para crear squemas en la BBDD
2. Agregar en el archivo `flyway.conf` la línea de configuracion `flyway.createSchemas=true`

# Funcionamiento

Después de configurar el proyecto, se debe agregar la carpeta `src/main/resources/db/migration`. Aquí iran todos los archivos SQL que conforman la BBDD.

Los archivos de la base de datos deben tener la siguiente estructura `V[numero de version][Doble ralla al piso][describcion del archivo].sql` Por ejemplo: `V1.0.0__CreacionTablaPersonas.sql` ó `V1__CreacionTablaPersonas.sql`

Una vez el archivo nuevo este dentro de la ruta mensionada. Correr el siguiente comando: `mvn flyway:migrate`, Lo cual hará lo sigiente:
1. Intenta conectarse a la BBDD
2. Si se conecta con exito, revisa que existan los esquemas mencionados en el archivo `flyway.conf`
3. Si existen, continua al siguiente paso. Si no existen y en la configuracion se da permiso para crear los squemas los crea.
4. Crea una tabla llamada `flyway_squema_history` en donde guardará el historial de versiones. Si ya existia, solo agrega el cambo de la version
5. Ejecuta los archivos dentro de `src/main/resources/db/migration`
6. Termina la migracion.

Si hay fallos durante la migracion, se puede optener un log más detallado agregando -X. `mvn flyway:migrate -X`
