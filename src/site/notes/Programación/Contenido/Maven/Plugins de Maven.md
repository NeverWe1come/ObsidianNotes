---
{"dg-publish":true,"permalink":"/programacion/contenido/maven/plugins-de-maven/"}
---

# Clean
Cuando se llama a la fase clean de un ciclo de vida, Maven ejecuta este ciclo de vida y elimina la carpeta target de nuestro proyecto, de esta forma elimina cualquier rastro de una compilación anterior que pueda afectar a una nueva. Para ello hace uso del *Maven Clean Plugin*, como clean es un ciclo de vida separado, es necesario llamarlo siempre antes del ciclo de vida default, si se necesita que se eliminen compilaciones anteriores.
- mvn clean
Si se desea que de forma automática el ciclo de vida clean se ejecute antes de cualquier compilación, se deben añadir alguna configuraciones en el pom.xml:
```
<plugins>
<!-- Ejecutará el plugin clean en la fase de inicialización y se podrá identificar con el id auto-clean -->
  <plugin>
	<artifactId>maven-clean-plugin</artifactId>
	<version>3.1.0</version>
	<executions>
	  <execution>
	  <id>auto-clean</id>
	  <phase>initialize</phase>
	  <!-- Eliminará además de la carpeta target la carpeta output que se encuentra en la raíz del proyecto -->
	  <configuration>
		<filesets>
		  <fileset>
			<directory>${basedir}/output</directory>
		  </fileset>
		</filesets>
	  </configuration>
	  <goals>
		<goal>clean</goal>
	  </goals>
	  </execution>
	</executions>
  </plugin>
</plugins>
```

# Surefire
Plugin utilizado por Maven para la ejecución de los test generados. Surefire utiliza el framework de test, que hayamos definido en el fichero pom.xml, si se desea cambiar, simplemente debemos añadir la dependencia al propio pom.xml:
```
<!-- Framework JUnit -->
<dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
  <version>4.11</version>
  <scope>test</scope>
</dependency>

<!-- Framework TestNG-->
<dependency>
  <groupId>org.testng</groupId>
  <artifactId>testng</artifactId>
  <version>6.9.8</version>
  <scope>test</scope>
</dependency>
```

Para omitir las pruebas tenemos dos opciones:
- mvn -DskipTest test
- Configuración en pom.xml:
```
<plugins>
  <plugin>
	<artifactId>maven-surefire-plugin</artifactId>
	<version>2.22.1</version>
	<!-- Saltar Tests-->
	<configuration>
	  <skipTests>true</skipTests>
	</configuration>
  </plugin>
</plugins>  
```
# JaCoCo
Plugin Open Source de Java que realiza prueba automáticas sobre código Java. para utilizarlo, se deba añadir la dependencia y configurar el plugin JaCoCo en el pom.xml:
```
<!-- Dependencia de JaCoCo -->
<dependencies>
	<dependency>
	  <groupId>org.jacoco</groupId>
	  <artifactId>jacoco-maven-plugin</artifactId>
	  <version>0.8.7</version>
	</dependency>
</dependencies>

<!-- Configuración Plugin de JaCoCo -->
<plugins>
  <plugin>
	<groupId>org.jacoco</groupId>
	<artifactId>jacoco-maven-plugin</artifactId>
	<version>0.8.7</version>
	<executions>
	  <execution>
		<id>default-prepare-agent</id>
		<goals>
		  <goal>prepare-agent</goal>
		</goals>
	  </execution>

	  <!-- Porcentaje mínimo de cobertura -->
	  <!--
	  <execution>
		<id>default-check</id>
		<phase>prepare-package</phase>
		<goals>
		  <goal>check</goal>
		</goals>
		<configuration>
		  <rules>
			<rule>
			  <element>BUNDLE</element>
			  <limits>
				<limit>
				  <counter>COMPLEXITY</counter>
				  <value>COVEREDRATIO</value>
				  <minimum>0.60</minimum>
				</limit>
			  </limits>
			</rule>
		  </rules>
		</configuration>
	  </execution>
	  -->

	  <execution>
		<id>default-report</id>
		<phase>prepare-package</phase>
		<goals>
		  <goal>report</goal>
		</goals>
	  </execution>
	</executions>
  </plugin>
</plugins>
```

Este plugin generara un report de los problemas encontrados en nuestro código en la ruta *${project_route}/target/site/jacoco/index.html*
# SonarQube
Plataforma de código abierto para la inspección continua de la calidad del código a través de diferentes herramientas de análisis estático de código fuente.
Configuraciones en pom.xml:
```
<!-- Dependencia sonar-maven-plugin  -->
<dependency>
  <groupId>org.sonarsource.scanner.maven</groupId>
  <artifactId>sonar-maven-plugin</artifactId>
  <version>3.8.0.2131</version>
</dependency>

<!-- Porcentaje de covertura en SonarQube -->  
<plugin>
	<groupId>org.sonarsource.scanner.maven</groupId>
	<artifactId>sonar-maven-plugin</artifactId>
	<version>3.8.0.2131</version>
</plugin>

<!-- Plugin necesario para generar el reporte de cobertura que luego usará SonarQube -->
<plugin>
	 <groupId>org.jacoco</groupId>
	 <artifactId>jacoco-maven-plugin</artifactId>
	 <version>0.8.6</version>
	 <executions>
		<execution>
			<id>prepare-agent</id>
			<goals>
			   <goal>prepare-agent</goal>
			</goals>
			<phase>test-compile</phase>
		</execution>
		<execution>
			<id>report</id>
			<goals>
			   <goal>report</goal>
			</goals>
			<phase>test</phase>
		</execution>
	  </executions>
</plugin>
```