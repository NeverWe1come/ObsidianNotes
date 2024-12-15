---
{"dg-publish":true,"permalink":"/programacion/contenido/maven/pom-xml-otros/"}
---


# Personalizar documentación 
```xml
<reporting>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-project-info-reports-plugin</artifactId>
      <version>3.2.2</version>
      <reportSets>
        <reportSet>
          <reports>
            <report>index</report>
            <report>dependencies</report>
            <report>team</report>
            <report>licenses</report>
          </reports>
        </reportSet>
      </reportSets>
    </plugin>
  </plugins>
</reporting>
```

```xml
<licenses>
  <license>
    <name>Mi licencia</name>
    <url>https://www.mi-licencia.com/licencia.txt</url>
    <distribution>repo</distribution>
    <comments>Una descripción sobre mi licencia</comments>
  </license>
</licenses>
```

```xml
<developers>
	<developer>
	    <id>jmmc</id>
	    <name>Jose Moya</name>
	    <email>josemoya@ejemplo.com</email>
	    <url>http://www.ejemplo.com/jmmc</url>
	    <organization>UDEMY</organization>
	    <organizationUrl>http://www.udemy.com/organizationUrl</organization>
	    <roles>
	        <role>Data Engineer</role>
	        <role>developer</role>
	    </roles>
	    <timezone>America/Mexico City</timezone>
	</developer>
<developers>
```

```xml
<contributors>
  <contributor>
    <name>Idalme</name>
    <email>idalme@gmail.com</email>
    <url>http://idalme.com</url>
    <organization>Udemy</organization>
    <organizationUrl>http://udemy.com/organizationUrl</organizationUrl>
    <roles>
      <role>tester</role>
    </roles>
    <timezone>America/Mexico City</timezone>
  </contributor>
</contributors>
```

# Incluir recursos adicionales
(Por defecto se copiar los recursos de /src/main/resources)
```xml
<resources>
  <resource>
    <directory>src/resources/xml</directory>
    <includes>
      <include>*.xml</include>
    </includes>
  </resource>
  <resource>
    <directory>src/resources/json</directory>
    <includes>
      <include>incluir.json</include>
    </includes>
    <excludes>
      <exclude>no-incluir.json</exclude>
    </excludes>
  </resource>
</resources>
```

# Logs de Maven
*Niveles de logs* (Especificar un nivel, implica que se muestran todos los logs de su nivel y de sus superiores):
- **DEBUG**
- **INFO (Defecto)**
- **WARNING**
- **ERROR**

Comando para controlar logs en maven: 
-  **mvn -q clean package** -> No mostrar nada, excepto nivel ERROR
- **mvn clean package** -> Mostrar todos
- **mvn -X clean package** -> Todos mensajes en nivel DEBUG y superiores (Es decir todos)
- **mvn -X clean package > log.txt** -> Crea el fichero log.txt

