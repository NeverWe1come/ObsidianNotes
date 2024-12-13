---
{"dg-publish":true,"permalink":"/programacion/contenido/maven/instalacion/"}
---

# Requisitos
- JDK: https://www.oracle.com/es/java/technologies/downloads/
- Variables de entorno JAVA
	-  **Variables de usuario:** *JAVA_HOME -> C:\Program Files\Java\jdk-17*
	- **Variables de usuario - Path:** *%JAVA_HOME%\bin*
- Maven: https://maven.apache.org/download.cgi
# Instalación
Una vez reunidos todos los requisitos, se debe descomprimir el archivo .zip de maven y colocar la carpeta en algún lugar donde no vaya a ser eliminada (Preferiblemente que la ruta donde se coloque la carpeta, no tenga espacios en blanco, para evitar errores).
Agregar dos nuevas variables de entorno:
- **Variables de usuario:** *MAVEN_HOME -> C:\apache-maven-3.9.6*
- **Variables de usuario - Path:** *%MAVEN_HOME%\bin*

Reiniciar sistema operativo para que los cambios se guarden correctamente. Para comprobarla instalación java y maven en nuestro sistema, abrir una shell del sistema  y ejecutar los comandos:
- mvn -v
- java --version


