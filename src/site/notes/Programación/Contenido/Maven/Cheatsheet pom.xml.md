---
{"dg-publish":true,"permalink":"/programacion/contenido/maven/cheatsheet-pom-xml/"}
---

**Documentación:** https://maven.apache.org/pom.html

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	
	<!-- The Basics -->
	<groupId>...</groupId>
	<artifactId>...</artifactId>
	<version>...</version>
	<packaging>...</packaging>
	<dependencies>...</dependencies>
	<parent>...</parent>
	<dependencyManagement>...</dependencyManagement>
	<modules>...</modules>
	<properties>...</properties>
	
	<!-- Build Settings -->
	<build>...</build>
	<reporting>...</reporting>
	
	<!-- More Project Information -->
	<name>...</name>
	<description>...</description>
	<url>...</url>
	<inceptionYear>...</inceptionYear>
	<licenses>...</licenses>
	<organization>...</organization>
	<developers>...</developers>
	<contributors>...</contributors>
	
	<!-- Environment Settings -->
	<issueManagement>...</issueManagement>
	<ciManagement>...</ciManagement>
	<mailingLists>...</mailingLists>
	<scm>...</scm>
	<prerequisites>...</prerequisites>
	<repositories>...</repositories>
	<pluginRepositories>...</pluginRepositories>
	<distributionManagement>...</distributionManagement>
	<profiles>...</profiles>
</project>
```

# Maven POM Cheatsheet

Esta hoja de referencia incluye una descripción concisa de las etiquetas más importantes del archivo `pom.xml` de Maven, seguida de ejemplos detallados por etiqueta.

## Tabla de Etiquetas

| Etiqueta                   | Descripción breve                                               |
| -------------------------- | --------------------------------------------------------------- |
| `<project>`                | Encabezado principal del POM con espacios de nombres y esquema. |
| `<modelVersion>`           | Versión del modelo POM (siempre `4.0.0`).                       |
| `<groupId>`                | Identificador único del grupo u organización.                   |
| `<artifactId>`             | Nombre único del proyecto dentro del grupo.                     |
| `<version>`                | Versión del proyecto.                                           |
| `<packaging>`              | Tipo de paquete (`jar`, `war`, etc.).                           |
| `<dependencies>`           | Lista de dependencias del proyecto.                             |
| `<parent>`                 | Proyecto padre del cual heredar configuraciones.                |
| `<dependencyManagement>`   | Gestión de dependencias compartidas para módulos.               |
| `<modules>`                | Lista de módulos en proyectos multi-módulo.                     |
| `<properties>`             | Propiedades personalizadas.                                     |
| `<build>`                  | Configuraciones de compilación.                                 |
| `<reporting>`              | Configuración de generación de informes.                        |
| `<name>`                   | Nombre legible del proyecto.                                    |
| `<description>`            | Descripción breve del proyecto.                                 |
| `<url>`                    | URL asociada al proyecto.                                       |
| `<inceptionYear>`          | Año de inicio del proyecto.                                     |
| `<licenses>`               | Información de licencias.                                       |
| `<organization>`           | Información de la organización propietaria.                     |
| `<developers>`             | Lista de desarrolladores.                                       |
| `<contributors>`           | Lista de colaboradores.                                         |
| `<issueManagement>`        | Configuración del sistema de seguimiento de problemas.          |
| `<ciManagement>`           | Configuración de integración continua.                          |
| `<mailingLists>`           | Listas de correos asociadas al proyecto.                        |
| `<scm>`                    | Información del sistema de control de versiones.                |
| `<prerequisites>`          | Requisitos mínimos para construir el proyecto.                  |
| `<repositories>`           | Lista de repositorios de dependencias.                          |
| `<pluginRepositories>`     | Repositorios para plugins de Maven.                             |
| `<distributionManagement>` | Configuración para publicar el proyecto.                        |
| `<profiles>`               | Perfiles personalizados para diferentes entornos.               |

---
## Ejemplos Detallados

### `<project>`

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
```
---
### `<modelVersion>`

```xml
<modelVersion>4.0.0</modelVersion>
```
---
### `<groupId>`

```xml
<groupId>com.example</groupId>
```
---
### `<artifactId>**

```xml
<artifactId>my-app</artifactId>
```
---
### `<version>`

```xml
<version>1.0.0</version>
```
---
### `<packaging>`

```xml
<packaging>jar</packaging>
```
---
### `<dependencies>`

```xml
<dependencies>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>5.3.8</version>
  </dependency>
</dependencies>
```
---
### `<parent>`

```xml
<parent>
  <groupId>org.apache.maven</groupId>
  <artifactId>maven-parent</artifactId>
  <version>34</version>
</parent>
```
---
### `<dependencyManagement>`

```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>com.example</groupId>
      <artifactId>shared-library</artifactId>
      <version>1.0.0</version>
    </dependency>
  </dependencies>
</dependencyManagement>
```
---
### `<modules>`

```xml
<modules>
  <module>module-a</module>
  <module>module-b</module>
</modules>
```
---
### `<properties>`

```xml
<properties>
  <java.version>11</java.version>
</properties>
```
---
### `<build>`

```xml
<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.8.1</version>
    </plugin>
  </plugins>
</build>
```
---
### `<reporting>`

```xml
<reporting>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-site-plugin</artifactId>
    </plugin>
  </plugins>
</reporting>
```
---
### `<name>`

```xml
<name>My Application</name>
```
---
### `<description>`

```xml
<description>Este es un ejemplo de aplicación</description>
```
---
### `<url>`

```xml
<url>https://mi-aplicacion.com</url>
```
---
### `<inceptionYear>`

```xml
<inceptionYear>2024</inceptionYear>
```
---
### `<licenses>`

```xml
<licenses>
  <license>
    <name>Apache License 2.0</name>
    <url>https://www.apache.org/licenses/LICENSE-2.0</url>
  </license>
</licenses>
```
---
### `<organization>`

```xml
<organization>
  <name>My Company</name>
  <url>https://www.mycompany.com</url>
</organization>
```
---
### `<developers>

```xml
<developers>
  <developer>
    <id>dev01</id>
    <name>Jane Doe</name>
  </developer>
</developers>
```
---
### `<contributors>`

```xml
<contributors>
  <contributor>
    <name>John Smith</name>
  </contributor>
</contributors>
```
---
### `<issueManagement>`

```xml
<issueManagement>
  <system>JIRA</system>
  <url>https://jira.mycompany.com</url>
</issueManagement>
```
---
### `<ciManagement>`

```xml
<ciManagement>
  <system>Jenkins</system>
  <url>https://jenkins.mycompany.com</url>
</ciManagement>
```
---
### `<mailingLists>`

```xml
<mailingLists>
  <mailingList>
    <name>dev</name>
    <archive>https://archives.mycompany.com</archive>
  </mailingList>
</mailingLists>
```
---
### `<scm>`

```xml
<scm>
  <connection>scm:git:git://github.com/example/my-app.git</connection>
</scm>
```
---
### `<prerequisites>`

```xml
<prerequisites>
  <maven>3.6.0</maven>
</prerequisites>
```
---
### `<repositories>`

```xml
<repositories>
  <repository>
    <id>central</id>
    <url>https://repo.maven.apache.org/maven2</url>
  </repository>
</repositories>
```
---
### `<pluginRepositories>`

```xml
<pluginRepositories>
  <pluginRepository>
    <id>central</id>
    <url>https://repo.maven.apache.org/maven2</url>
  </pluginRepository>
</pluginRepositories>
```
---
### `<distributionManagement>`

```xml
<distributionManagement>
  <repository>
    <id>releases</id>
    <url>https://repo.mycompany.com/releases</url>
  </repository>
</distributionManagement>
```
---
### `<profiles>`

```xml
<profiles>
  <profile>
    <id>development</id>
    <properties>
      <env>dev</env>
    </properties>
  </profile>
</profiles>
```

