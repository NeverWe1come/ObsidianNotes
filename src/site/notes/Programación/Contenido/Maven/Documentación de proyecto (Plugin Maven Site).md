---
{"dg-publish":true,"permalink":"/programacion/contenido/maven/documentacion-de-proyecto-plugin-maven-site/"}
---

# Crearla
- mvn site
  Este comando generara una documentación básica, en la ubicación ${proyecto}/target/site/index.html

# Configuración
La documentación para los proyectos JAVA, se genera usando javadocs. Los plugins configurados dentro del elemento reporting del pom.xml del proyecto, generaran el contenido de dicha documentación.
```xml
<reporting>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-javadoc-plugin</artifactId>
            <version>3.3.2</version>
			<reportSets>
			    <reportSet>
			        <reports>
			            <report>javadoc</report>   <-- Omitir la documentación de las pruebas del proyecto -->
			        </reports>
			    </reportSet>
			</reportSets>
        </plugin>
        <!-- Generar un informe de las pruebas unitarias del proyecto y agregarlo al sitio web de documentación -->
		<plugin>
		    <groupId>org.apache.maven.plugins</groupId>
		    <artifactId>maven-surefire-report-plugin</artifactId>
		    <version>3.0.0-M5</version>
		</plugin>
    </plugins>
</reporting>
```
