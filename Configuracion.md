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