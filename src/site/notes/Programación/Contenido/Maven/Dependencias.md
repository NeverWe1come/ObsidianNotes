---
{"dg-publish":true,"permalink":"/programacion/contenido/maven/dependencias/"}
---

Las dependencias son bibliotecas externas que el proyecto necesita para funcionar correctamente. Estas pueden ser librerías de terceros, frameworks, APIs, u otros componentes que el proyecto requiere para compilar, ejecutar, o realizar tareas específicas(Ej. Pruebas - JUnit). Una de las características principales de Maven es su capacidad como administrador de dependencias.

# ¿Cómo declarar una dependencia?
Las dependencias se declaran en el fichero pom.xml de nuestro proyecto, de la siguiente forma:
```
<dependencies>
	<dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
    </dependency>
</dependencies>
```

\<groupId\>...\</groupId\>
\<artifactId\>...\</artifactId\>       ------>   Estas tres anotaciones identifican de forma única a una dependencia
\<version\>...\</version\>
# \<scope\>...\</scope\>

El scope (o alcance) de una dependencia en Maven define cuándo y dónde estará disponible la dependencia durante el ciclo de vida del proyecto (compilación, ejecución, pruebas, etc.). Especificar el scope permite controlar el uso de las dependencias, asegurando que se incluyan solo donde sean necesarias, lo que evita dependencias innecesarias en el entorno de producción. Por defecto, si no se indica nada, el scope es *compile*.

Scopes disponibles:
- **compile:** Dependencia necesaria para la [[Programación/Contenido/Maven/Definiciones#^d5fbc6\|compilación]](Incluidas pruebas y runtime, es decir, cuando se ejecuta el proyecto).
- **test:** Dependencia necesaria solo para pruebas(No necesarias para el runtime).
- **runtime:** Dependencia necesarias para la [[Programación/Contenido/Maven/Definiciones#^d10908\|ejecución]] del proyecto(No hacen falta para compilarlo).
- **provide:** Dependencia necesaria para la compilación y el [[Programación/Contenido/Maven/Definiciones#^862406\|runtime]](No es necesaria empaquetarla en el paquete de distribución, esta será proporcionada por el usuario/servidor...)
- **system:** Similar a *provide*, se necesita proporcionar explícitamente la ubicación del archivo JAR de la dependencia.
- **import:** 

# Informe de dependencias
- mvn dependency:list
- mvn dependency:tree

# Dependencias no utilizadas
- mvn dependency:analyze