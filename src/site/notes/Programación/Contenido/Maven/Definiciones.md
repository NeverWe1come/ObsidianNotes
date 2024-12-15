---
{"dg-publish":true,"permalink":"/programacion/contenido/maven/definiciones/"}
---

# Compilación (`compile`)
{ #d5fbc6}


- **Definición**: Es el proceso de convertir el código fuente (normalmente escrito en Java en el caso de Maven) en código máquina intermedio que puede ejecutarse (archivos `.class` en el caso de Java).
- **Etapa**: Sucede antes de que la aplicación se ejecute.
- **Herramienta principal**: El compilador (en Maven, suele ser `javac`).
- **Uso en Maven**:
	- Las dependencias con alcance `compile` estarán disponibles en esta etapa.
	- Maven requiere que las dependencias necesarias para compilar el código (como bibliotecas externas) estén disponibles durante esta fase.

# Ejecución(`execution`)
{ #d10908}


- **Definición**: Es el proceso de **lanzar y ejecutar** la aplicación después de haber sido compilada. Aquí, el programa es cargado en la máquina virtual (JVM) y se procesa dinámicamente.
- **Etapa**: Ocurre después de la compilación, cuando el programa ya está en un formato ejecutable.
- **Herramienta principal**: La JVM (Java Virtual Machine) es la encargada de interpretar y ejecutar los archivos `.class`.
- **Uso en Maven**:
    - Maven permite ejecutar aplicaciones empaquetadas usando el comando `mvn exec:java` o al ejecutar el JAR generado.
    - Las dependencias con alcance `runtime` son importantes aquí porque son necesarias para que la aplicación funcione, pero no para compilarla.

# Runtime (Tiempo de ejecución)
{ #862406}


- **Definición**: Es el período **continuo** en el que el programa está activo y en funcionamiento después de que ha sido iniciado.
- **Enfoque temporal**: Abarca **todo el tiempo que el programa está corriendo** hasta que finaliza o es detenido.
- **Ejemplo**: Mientras la aplicación Java sigue procesando datos o manejando solicitudes después de haber sido ejecutada, está en "runtime".