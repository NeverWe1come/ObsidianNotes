---
{"dg-publish":true,"permalink":"/programacion/contenido/maven/maven/"}
---

# ¿Qué es?
Apache Maven es una potente herramienta de gestión de proyectos que se utiliza para gestión de dependencias, como herramienta de compilación e incluso como herramienta de documentación. Es de código abierto y gratuita.

Con Maven se puede:
- **Gestionar las dependencias del proyecto**, para descargar e instalar módulos, paquetes y herramientas que sean necesarios para el mismo.
- **Compilar el código fuente** de la aplicación de manera automática.
- **Empaquetar el código** en archivos .jar o .zip...
- **Instalar los paquetes en un repositorio** (local, remoto o en el central de la empresa)
- **Generar documentación a partir del código fuente.**
- **Gestionar las distintas fases del ciclo de vida** de las build: validación, generación de código fuente, procesamiento, generación de recursos, compilación, ejecución de test...

# Cambiar el JDK utilizado por Maven en local
Cambiar la versión de JAVA_HOME a la versión del JDK que se desee.

# Crear proyecto Maven desde terminal
- Abrir terminal en la carpeta donde se desee crear un proyecto con Maven
- **Modo interactivo:** mvn archetype:generate
- **Modo directo:** mvn archetype:generate "-DgroupId=com.udemy.maven" "-DartifactId=mi-primer-proyecto-maven" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DarchetypeVersion=1.4" "-DinteractiveMode=false"

# Repositorios de Maven
Lugares donde se encuentran los archivos utilizados por Maven para su funcionamiento correcto:
- **Local:** Repositorio local de maven en nuestro ordenador (Carpeta oculta).
	- **Windows:** C:/Users/{username}/.m2/repository
	- **Linux:** /home/{username}/.m2/repository
- **Remoto:** Repositorio desde donde Maven descarga los archivos requeridos para su funcionamiento.
- **Espejos:** Repositorios administrados (Ej. Nexus o Artifactory)

## Cambiar repositorio local de maven
Maven determina la ubicación del repositorio local, buscando el archivo settings.xml en dos ubicaciones:
![programacion-maven-maven_Decision-Repositorio-Local_.png](/img/user/Im%C3%A1genes/programacion-maven-maven_Decision-Repositorio-Local_.png)

settings.xml:
```
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 https://maven.apache.org/xsd/settings-1.0.0.xsd">
      
  	<localRepository>/Tu/Ruta/</localRepository>

</settings>
```

# Contenido importante carpeta "target" autogenerada al compilar el proyecto:
- **/classes:** Archivos fuente compilados.
- **/test-classes:** Archivos fuente de tests compilados.
- **/surefire-reports:** Informes de los tests.
- **/.jar:** Artefacto del proyecto generado.
- **/maven-archiver y /maven-status:** Archivos utilizados por maven durante la compilación del proyecto.
  ![programacion-maven-maven_Carpeta-Target_.png](/img/user/Im%C3%A1genes/programacion-maven-maven_Carpeta-Target_.png)
- 