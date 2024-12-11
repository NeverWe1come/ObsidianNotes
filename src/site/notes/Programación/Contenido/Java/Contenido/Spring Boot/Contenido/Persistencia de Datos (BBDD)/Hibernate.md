---
{"dg-publish":true,"permalink":"/programacion/contenido/java/contenido/spring-boot/contenido/persistencia-de-datos-bbdd/hibernate/"}
---

**Enlace:** https://docs.jboss.org/hibernate/orm/6.6/introduction/html_single/Hibernate_Introduction.html
**Definición:** **Hibernate** es una herramienta de mapeo objeto-relacional (ORM) para Java, que implementa la especificación JPA (Java Persistence API) de manera eficiente. En resumen, **Hibernate** facilita la interacción entre las aplicaciones Java y las bases de datos relacionales, permitiendo que las entidades (objetos Java) se mapeen automáticamente a las tablas de la base de datos, sin necesidad de escribir sentencias SQL manualmente.
### Principales características de Hibernate:
1. **Mapeo objeto-relacional (ORM)**: Hibernate permite mapear las clases Java a tablas de una base de datos y viceversa. De esta forma, los objetos Java representan directamente los registros en las tablas. Por ejemplo, una clase `Usuario` puede corresponder con una tabla `usuario` en la base de datos.
2. **Transparencia de persistencia**: Hibernate maneja automáticamente el ciclo de vida de los objetos persistentes, como la creación, lectura, actualización y eliminación de registros, sin tener que escribir código SQL para hacerlo.
3. **Independencia de la base de datos**: Hibernate abstrae las diferencias entre las bases de datos, permitiendo que tu aplicación sea más fácilmente portable entre diferentes motores de bases de datos (MySQL, PostgreSQL, Oracle, etc.), ya que Hibernate adapta el SQL generado según la base de datos que se use.
4. **Gestión automática de transacciones**: Hibernate maneja transacciones de forma automática, integrándose bien con el entorno Java EE y proporcionándote control de transacciones a nivel de la sesión o del contenedor.
5. **Optimización de consultas con HQL (Hibernate Query Language)**: Hibernate no solo utiliza SQL, sino que también tiene su propio lenguaje de consulta, llamado **HQL**, que está basado en objetos. HQL permite trabajar con las entidades Java directamente en lugar de escribir SQL crudo.
6. **Caché**: Hibernate tiene un sistema de caché incorporado que mejora el rendimiento al reducir las consultas repetidas a la base de datos.