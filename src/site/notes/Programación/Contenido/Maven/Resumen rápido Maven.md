---
{"dg-publish":true,"permalink":"/programacion/contenido/maven/resumen-rapido-maven/"}
---

## ¿Qué es Maven?
Apache Maven es una herramienta de gestión y comprensión de proyectos que se utiliza principalmente en proyectos Java. Facilita la automatización de tareas como la construcción, la gestión de dependencias y el despliegue.

---
## Diferencia entre Dependencia y Plugin

| **Aspecto**          | **Dependencia**                                                                                          | **Plugin**                                                                                                  |
|----------------------|--------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| **Propósito**       | Biblioteca o framework utilizado en el código del proyecto.                                           | Herramienta que extiende o modifica el comportamiento de Maven.                                            |
| **Declaración**      | Bajo la etiqueta `<dependencies>` en el POM.                                                         | Bajo la etiqueta `<plugins>` dentro de `<build>`.                                                         |
| **Ejemplo**          | Spring Framework, Hibernate, etc.                                                                     | Compiler Plugin, Surefire Plugin, etc.                                                                    |
| **Ejecutado**        | En tiempo de compilación o ejecución del proyecto.                                                 | En el momento de la construcción del proyecto (build).                                                  |
| **Obtención**       | [https://mvnrepository.com](https://mvnrepository.com)                                                | [https://maven.apache.org/plugins](https://maven.apache.org/plugins)                                       |

---

## Ciclo de Vida de Maven

El ciclo de vida de Maven consta de tres ciclos principales: **Build** (construcción), **Clean** (limpieza) y **Site** (documentación). Cada ciclo contiene varias fases que se ejecutan de forma secuencial.

### Ciclo de Construcción (Build Lifecycle)
Fases principales:

1. **validate**: Verifica que el proyecto esté correcto y toda la información necesaria esté disponible.
2. **compile**: Compila el código fuente del proyecto.
3. **test**: Ejecuta pruebas unitarias.
4. **package**: Empaqueta el código compilado en un formato distribuible, como un JAR o WAR.
5. **verify**: Ejecuta verificaciones para asegurar que el paquete es válido y cumple con los criterios de calidad.
6. **install**: Instala el paquete en el repositorio local para que otros proyectos puedan usarlo.
7. **deploy**: Copia el paquete final a un repositorio remoto compartido.

### Ciclo de Limpieza (Clean Lifecycle)
Fases principales:

1. **pre-clean**: Realiza tareas previas a la limpieza.
2. **clean**: Elimina los archivos generados por la construcción previa.
3. **post-clean**: Realiza tareas tras la limpieza.

### Ciclo de Documentación (Site Lifecycle)
Fases principales:

1. **pre-site**: Realiza tareas previas a la generación de la documentación.
2. **site**: Genera la documentación del proyecto.
3. **post-site**: Realiza tareas tras la generación de la documentación.
4. **site-deploy**: Publica la documentación generada en un servidor.
---
## Plugins comunes

| **Plugin**                     | **Descripción**                                                                                  | **URL**                                                             |
|--------------------------------|--------------------------------------------------------------------------------------------------|----------------------------------------------------------------------|
| Compiler Plugin                | Compila el código fuente del proyecto.                                                         | [Compiler Plugin](https://maven.apache.org/plugins/maven-compiler-plugin/) |
| Surefire Plugin                | Ejecuta pruebas unitarias.                                                                      | [Surefire Plugin](https://maven.apache.org/plugins/maven-surefire-plugin/) |
| Failsafe Plugin                | Ejecuta pruebas de integración.                                                                | [Failsafe Plugin](https://maven.apache.org/plugins/maven-failsafe-plugin/) |
| Clean Plugin                   | Limpia los archivos generados por construcciones anteriores.                                   | [Clean Plugin](https://maven.apache.org/plugins/maven-clean-plugin/)       |
| Shade Plugin                   | Empaqueta dependencias en un archivo JAR sombreado.                                            | [Shade Plugin](https://maven.apache.org/plugins/maven-shade-plugin/)       |

---
## Dependencias comunes

| **Dependencia**     | **Descripción**                                            | **URL**                                                                |
| ------------------- | ---------------------------------------------------------- | ---------------------------------------------------------------------- |
| Spring Boot Starter | Framework para crear aplicaciones Spring Boot.             | [Spring Boot](https://spring.io/projects/spring-boot)                  |
| Hibernate ORM       | Framework de mapeo objeto-relacional (ORM).                | [Hibernate](https://hibernate.org/)                                    |
| Apache Commons Lang | Utilidades comunes para manipulación de cadenas y objetos. | [Apache Commons Lang](https://commons.apache.org/proper/commons-lang/) |
| JUnit               | Framework para pruebas unitarias en Java.                  | [JUnit](https://junit.org/junit5/)                                     |
| Log4j               | Biblioteca para registro de eventos (logging).             | [Log4j](https://logging.apache.org/log4j/)                             |

---
## Proyectos Multimódulo
Los proyectos multimódulo permiten dividir una aplicación grande en módulos más pequeños, cada uno con su propio `pom.xml`. Esto facilita la gestión, reutilización y mantenimiento de los componentes del proyecto.
### Aspectos clave
1. **POM principal**: Contiene la configuración común y una lista de módulos.
   ```xml
   <project>
     <modelVersion>4.0.0</modelVersion>
     <groupId>com.example</groupId>
     <artifactId>parent-project</artifactId>
     <packaging>pom</packaging>
     <modules>
       <module>module-a</module>
       <module>module-b</module>
     </modules>
   </project>
   ```

2. **Módulos**: Cada módulo tiene su propio `pom.xml`.
   ```xml
   <project>
     <modelVersion>4.0.0</modelVersion>
     <parent>
       <groupId>com.example</groupId>
       <artifactId>parent-project</artifactId>
       <version>1.0.0</version>
     </parent>
     <artifactId>module-a</artifactId>
   </project>
   ```

3. **Herencia**: Los módulos heredan configuraciones del POM principal, lo que reduce la redundancia.
4. **Comandos comunes**:
   - `mvn clean install`: Construye todos los módulos en orden.
   - `mvn compile -pl module-a`: Compila solo el módulo especificado.
5. **Ventajas**:
   - Reutilización de configuraciones.
   - Construcción incremental.
   - Gestión simplificada de dependencias compartidas.
