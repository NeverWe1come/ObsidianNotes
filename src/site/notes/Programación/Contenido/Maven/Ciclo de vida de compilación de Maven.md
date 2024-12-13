---
{"dg-publish":true,"permalink":"/programacion/contenido/maven/ciclo-de-vida-de-compilacion-de-maven/"}
---

# Lifecycle y objetivos
Maven se basa en el concepto central de un ciclo de vida de construcción, es decir, el proceso para construir y distribuir un artefacto (proyecto) esta bien definido. Una persona que construye un nuevo proyecto, solo necesita conocer un pequeño conjunto de comandos y el fichero pom.xml se asegura de que se obtienen los resultados deseados. Hay 3 ciclos de vida en los proyectos Maven:
- **Default:** Construcción y despliegue del proyecto. Existen varias fases importantes (cuando se ejecuta una de ellas, también se ejecutan todas las anteriores):
	- **Validate:** Valida que toda la información del proyecto sea correcta y este disponible. 
	- **Compile:** Compila el código fuente.
	- **Test:** Ejecuta pruebas unitarias.
	- **Package:** Empaqueta el código compilado en el formato definido en el pom.xml
	- **Verify:** Ejecuta controles para verificar que el paquete es válido. 
	- **Install:** Instala el paquete en el repositorio local.
	- **Deploy:** Instala el paquete final en el repositorio (Debe configurarse, sino fallará).
- **Clean:** Limpieza de los archivos y carpetas producidos por Maven. (Elimina todos los archivos y carpetas creados por Maven como parte de la compilación)
- **Site:** Creación de documentación del proyecto. (Genera la documentación del proyecto, así como una plantilla que se puede personalizar)
# Pom.xml
- **pom.xml:** POM(Project Object Model) archivo xml basado en un esquema específico que define de que se trata el proyecto y cómo debe construirse. Elementos:
	- **\<groupId\>...\</groupId\>:** Identificador único de la organización a la que pertenece el proyecto.
	- **\<artifactId\>...\<artifactId\>:** Nombre del proyecto.
	- **\<version\>...<version\>:** Instancia específica del proyecto en un momento específico.
	- **\<packaging\>...\</packaging\>:** Tipo de artefacto del proyecto (jar,war,zip....)
	- **\<dependencies\>...\</dependencies\>:** Proyectos de los que depende nuestros proyecto.
	- **\<parent\>...\</parent\>:** Se utiliza para indicar una relación padre-hijo. Si el proyecto es parte de un proyecto de varios módulos o hereda la información de otro proyecto, los detalles se especifican en esta sección.
	- **<\properties\>...\</properties\>:**  Son marcadores de posición, se puede acceder a sus valores en cualquier parte del pom con ${key}.
	- \<**modules\>...\<modules\>:** Se emplea para identificar módulos.

# Profiles
Maven está diseñado para crear compilaciones portátiles que se espera que funcionen en distintas plataformas y entornos distintos(Ej. No es lo mismo construir un proyecto para desarrollo y otro para producción). Comando para compilar con perfiles:
- mvn package -P {profile}
# Variables en pom.xml y <\properties\>...\</properties\>
Se utilizan para dar valores fijos que vana a ser utilizados en varias partes del pom.xml, permite que los valores se manden en tiempo de ejecución y son accesibles mediante ${key}. Existen diferentes tipos de variables:
- **Variables de entorno:** Prefijar variable con *env* -> ${env.PATH} (Devolverá el valor de la variable de entorno PATH)
- **Variables POM:** Sirven para prefijar una variable con *project* -> ${project.version} (Devolverá el valor versión del pom.xml)
- **Variables de configuración:** Sirven para prefijar una variable de configuración con *settings* ->${settings.offline} (Devolverá el valor del elemento offline del archivo settings.xml) 
- **Propiedades normales:** Aquellas que se establecen dentro de la etiqueta properties -> ${java.version} (Devolverá 17)
```
<properties>
	<java.version>17</java.version>
</properties>
```