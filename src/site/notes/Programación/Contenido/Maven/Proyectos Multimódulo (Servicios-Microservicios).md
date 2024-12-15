---
{"dg-publish":true,"permalink":"/programacion/contenido/maven/proyectos-multimodulo-servicios-microservicios/"}
---

La mayoría de los proyectos de la vida real tienen más de un módulo, cada uno de ellos se puede desarrollar de forma independiente, o alguno de ellos puede depender de otros. Para los proyectos que utilicen módulos dependientes, debemos asegurarnos, de que estos los proyectos, se compilan con las versiones adecuadas de éstos módulos.
![programacion-contenido-maven-multimodulo_Modulos-Dependientes-Independientes_.png](/img/user/Im%C3%A1genes/programacion-contenido-maven-multimodulo_Modulos-Dependientes-Independientes_.png)
# Herencia de proyectos
Permite que se usen valores de otro archivo pom.xml evitando así la repetición de código.
![programacion-contenido-maven-multimodulo_Herencia-De_Proyecto_.png](/img/user/Im%C3%A1genes/programacion-contenido-maven-multimodulo_Herencia-De_Proyecto_.png)
# Creación proyecto multimódulo
- **Proyecto padre:** mvn archetype:generate "-DgroupId=com.udemy.maven" "-DartifactId=proyecto-multimodulo"  "-DinteractiveMode=false"
Dentro del proyecto padre realizamos varios cambios:
- Sustituir el contenido de la etiqueta \<packaging\>...\</packaging\> por \<packaging\>pom\</packaging\>
- Eliminar la carpeta /src

Ahora crearemos tantos módulos hijos como queramos y maven modificara el pom.xml del padre para manejar estos módulos:
- **Módulo 1:** mvn archetype:generate "-DgroupId=com.udemy.maven" "-DartifactId=modulo-1"  "-DinteractiveMode=false"

# Gestión de dependencias heredadas
Las dependencias definidas en el pom.xml del proyecto padre, estarán disponibles para los proyectos hijos, siempre que se definan dentro del la etiqueta \<dependencyManagement\>...\</dependencyManagement\>:
![programacion-contenido-maven-multimodulo_Dependencias-Heredadas_.png](/img/user/Im%C3%A1genes/programacion-contenido-maven-multimodulo_Dependencias-Heredadas_.png)

```
<dependencyManagement>
	<dependencies>
	  <dependency>
		<groupId>junit</groupId>
		<artifactId>junit</artifactId>
		<version>3.8.1</version>
		<scope>test</scope>
	  </dependency>
	</dependencies>
</dependencyManagement>    
```

# Gestión de plugins heredados
Los plugins definidos en el pom.xml del proyecto padre, estarán disponibles para los proyectos hijos, siempre que se definan dentro del la etiqueta \<pluginManagement\>...\</pluginManagement\>:
```xml
<build>
	<pluginManagement>
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
	</pluginManagement>
</build>
```

Si este plugin es definido en los pon de los hijos, únicamente con el groupId y artifactId, heredará la versión y configuración del pom.xml del padre. Si no estuviese definido, nunca la heredaría.
**IMPORTANTE:** **Si** el **pom.xml del padre** **define** un **plugin** que se **utilice** para el **ciclo de vida de de maven**, los **módulos hijos** lo **heredarán**, **aunque** **no lo tengan especificado en su pom.xml** hijo. Para evitar esto, se debe usar la etiqueta específica:
```xml
<build>
	<pluginManagement>
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
			<execution>
			  <id>default-report</id>
			  <phase>prepare-package</phase>
			  <goals>
				<goal>report</goal>
			  </goals>
			</execution>
		  </executions>
		</plugin>
		
		<!-- Si se elimina <inherited>false</inherited> se heredara -->
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.10.1</version>
          <inherited>false</inherited>
        </plugin>
        
	  </plugins>
	</pluginManagement>
</build>
```

# Módulos segun perfil
```
<profiles>
	<profile>
	<id>des</id>
	<modules>
	  <module>modulo-1</module>
	  <module>modulo-2</module>
	</modules>
	</profile>
</profiles>

<modules>
	<module>modulo-1</module> 
</modules>
```

# Documentación multimódulo
```xml
<!-- Necesario genere documentación con los enlaces a los módulos hijos de forma correcta -->
<distributionManagement>
	<site>
		<id>sitio-web</id>
		<url>http://sitio.url/que/puede/ser/tentativo/o/hipotetico</url>
	</site>
</distributionManagement>
```

- Opción 2: Elegir una carpeta de salida distinta:
  mvn site.stage -DstagingDirectory={RUTA_DESEADA}