---
{"dg-publish":true,"permalink":"/programacion/contenido/java/contenido/spring-boot/contenido/persistencia-de-datos-bbdd/anotaciones/"}
---

# Entidades

## Una entidad debe:
- Ser una clase NO final
- Contener un constructor sin parámetros NO privado
## Conceptos:
### Entidades
- **Acceso a datos de entidades:** Cada entidad tiene un tipo de **acceso** predeterminado a los datos, que puede ser acceso directo **por campo ó** a través de la **propiedad (método)**.
```
public class Person {
    @Id
    private Long id;  // Acceso por campo
}

ó

public class Person {
    private Long id;

    @Id
    public Long getId() {  // Acceso por propiedad
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }
}
```
- **Herencia entidades:** **Una entidad, puede extender otra entidad (Herencia)**. **Estas "subentidades" no pueden tener su propio @Id** definido, si heredan de otra clase @Entity, ya que cuando se anota con @Entity, se esta realizando un mapeo a una tabla de la BBDD.
```
@Entity
public class Book {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String title;

    // getters y setters
}

@Entity
public class AudioBook extends Book {
    private int length;  // Longitud en minutos

    // getters y setters
}

@Entity
public class Ebook extends Book {
    private String format;  // Formato del ebook (PDF, EPUB, etc.)

    // getters y setters
}
```
**Existe una forma** en la que las **"subclases" pueden tener su propio @Id**, esto se puede hacer anotando la clase raíz como **@MappedSuperclass**, ya que las propias subentidades heredarán todos los atributos, pero al no estar mapeada con @Entity, no es una tabla de la BBDD:
```
@MappedSuperclass
public class Versioned {
    @Version
    private Long version;
}

@Entity
public class Book extends Versioned {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String title;
}

@Entity
public class AudioBook extends Versioned {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private int length;
}
```

**Si cada una de las entidades fuese una tabla de la BBDD**, se deberían anotar como **@Entity** **y no habría una herencia como tal, sino una relación @ManyToOne:**
```
@Entity
public class Book {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String title;
}

@Entity
public class Ebook {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @ManyToOne
    @JoinColumn(name = "idBook")
    private Book book;

    private String format;
}

@Entity
public class AudioBook {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @ManyToOne
    @JoinColumn(name = "idBook")
    private Book book;

    private String audioFormat;
}
```
-  **Tabla BBDD 2 o más identificadores:** En ocasiones, alguna **tabla de la BBDD** puede tener **dos o más identificadores**, **por norma,** una **clase marcada como @Entity (Entidad)** **solo** puede tener **1**. Pero **existe una forma** de hacerlo, **crear una clase única que contenga los identificadores** y se anota como **@Embeddable**. Después **la clase "original"**, **contendrá como atributo esta nueva entidad** y se marcará el atributo id como **@EmbeddedId**
```
@Embeddable
public class BookId implements Serializable {
    private String isbn;
    private int printing;

    // Constructor vacío necesario para JPA
    public BookId() {}

    public BookId(String isbn, int printing) {
        this.isbn = isbn;
        this.printing = printing;
    }

    // Getters, setters y otros
    ...
}
```

```
@Entity
public class Book {
    @EmbeddedId
    private BookId bookId;

    private String title;
    private String author;

    // Constructor vacío necesario para JPA
    public Book() {}

    public Book(BookId bookId, String title, String author) {
        this.bookId = bookId;
        this.title = title;
        this.author = author;
    }

    // Getters, setters y otros
    ...
}
```
- **Atributos con bloqueos optimistas:** Aquellos **atributos @Version** son **asignados automáticamente por Hibernate** **cuando una entidad se persiste** **y se incrementan o actualizan automáticamente** **cada vez que la entidad se actualiza**. Estos atributos suelen ser de los tipos Integer, Short, Long, LocalDateTime, OffsetDateTime, ZonedDateTime o Instant.
```
@Version
LocalDateTime lastUpdated;
```
  La anotación **@Version, permite implementar bloqueo optimista** para evitar conflictos al actualizar los datos en entornos concurrentes. **Cuando Hibernate actualiza una entidad**, **verifica** **que** el v**alor del atributo** **@Version** en la base de datos **no haya cambiado** desde que la entidad fue leída. **Si el valor ha cambiado** (otra transacción modificó la entidad), **lanza** una **OptimisticLockException**, protegiendo así la consistencia de los datos. Avanzado:
  1. **@OptimisticLocking:** Permite definir cómo Hibernate realiza el bloqueo optimista, verificando todos los campos o solo los modificados.
```
@Entity
@OptimisticLocking(type = OptimisticLockType.DIRTY)  // Solo verifica campos modificados
public class Book {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;

    private String author;

    @Version
    private Integer version;

    // Getters y setters
}
```
Flujo con la estrategia DIRTY (Por defecto All):
	**Transacción A:** Lee el libro con id=1
	**Transacción B:** Lee el libro con id=1
	**Transacción A:** Modifica el título e Hibernate actualiza el campo version a 2 (title = "Hola 2",author = "John Doe",version = 2)
	**Transacción B:** Modifica solo author e intenta guarda los cambios.
	Hibernate detecta que title fue modificado en otra transacción (aunque author no cambió). **Si** la **estrategia fuera ALL, la operación fallaría.** Con **DIRTY**, c**omo no hay conflicto** en el c**ampo modificado, el guardado tiene éxito.**
	
  1. **@OptimisticLock:** Excluye campos específicos del proceso de verificación de bloqueo optimista.
```
@Entity
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String username;

    private String email;

    @OptimisticLock(excluded = true)  // Excluir este campo del bloqueo optimista
    private LocalDateTime lastLogin;

    @Version
    private Integer version;

    // Getters y setters
}     
```
Flujo con @OptimisticLock (excluded por defecto a false):
	**Transacción A:** Lee el usuario con id=1.
	**Transacción B:** También lee el mismo usuario.
	**Transacción A:** Actualiza el campo username y guarda los cambios. Hibernate incrementa version a 2.
	**Transacción B:** Actualiza solo el campo lastLogin (por ejemplo, registra un inicio de sesión).
	Como lastLogin está excluido del bloqueo optimista, el guardado no lanza excepciones, incluso si la versión de la entidad ha cambiado.

- Puede que una entidad tenga un **atributo o conjunto de atributos, que sin ser Id's sean únicos**. Con la anotación **@NaturalId** indicamos a Hibernate este tipo de atributos únicos(Ej. ISBN de un libro). A nivel de funcionalidad, Hibernate crea una clave única para estos atributos/columnas de la BBDD.
```
@Entity
public class Book {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;  // clave primaria generada automáticamente

    @NaturalId
    private String isbn;  // clave natural

    @NaturalId
    private int printing;  // otra parte de la clave natural

    // Otros atributos y métodos
}
```
- Para definir **atributos de tipos básicos** (Por defecto la mayoría en JPA) y **adicionalmente definir** si un **atributo es opcional** con @Basic(optional=false)
```
@Basic(optional=false) String firstName;
```
**Otras formas** de definir atributos opcionales/obligatorios y diferencias:
1. **`(Atributo-Getter)`** **@Basic(optional = false)**: Se realiza la comprobación en la capa lógica, es decir, el atributo no debe ser nulo en el modelo de dominio de Java.
```
@Basic(optional=false) String firstName;  
```
2. **`(Atributo-Getter)`** **@Column(nullable=false):** Pertenece a la capa de mapeo y afecta al esquema de la base de datos (DDL). No afecta la lógica de la aplicación directamente, solo al esquema.
```
@Column(nullable=true) String firstName;
```
3. **`(Atributo-Getter)`** **@NotNull**: Bean Validation para garantizar que el atributo no sea nulo.
```
@NotNull private String firstName;
```
  4. **Mejor práctica**: Utilizar **@Basic(optional=false)** para controlar la nulidad en la capa lógica **y @NotNull** de Bean Validation para validaciones adicionales.

- En algunos casos se necesitara realizar **personalizar el mapeo entre los tipos de datos de Java y los tipos de SQL**. Para ello se utilizan las anotaciones **@JavaType** y **@JdbcType** (**Cuando un JavaType no puede convertir instancias al tipo esperado por su JdbcType**, usar un  **@Converter** personalizado para manejar la conversión.)
	- **@JavaType(BitSetJavaType.class)**: Representa un tipo Java específico y define cómo manejarlo.
	```
	@JavaType(BitSetJavaType.class) BitSet bitSet;
	```
	- **@JdbcType(VarcharJdbcType.class):** Representa cómo se debe leer y escribir un tipo SQL desde/hacia JDBC.
	```
	  @JdbcType(VarcharJdbcType.class) long currentTimeMillis;
	```
	- **@Convert(converter = LongToTimestampConverter.class)**: Cuando un JavaType no puede convertir instancias al tipo esperado por su JdbcType.
		```
	  @JdbcType(TimestampJdbcType.class) 
	  @Convert(converter = LongToTimestampConverter.class) 
	  long currentTimeMillis;
		```
		**Ejemplo** completo **@Converter y @Convert** :
```
@Entity
public class Task {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Convert(converter = DurationToLongConverter.class) // Usa el convertidor para mapear Duration a BIGINT
    @JdbcTypeCode(Types.BIGINT) // Especifica el tipo SQL
    private Duration duration;

    // Getters y setters
}

@Converter(autoApply = true) // autoApply = true aplica este convertidor automáticamente para cualquier Duration
public class DurationToLongConverter implements AttributeConverter<Duration, Long> {

    @Override
    public Long convertToDatabaseColumn(Duration duration) {
        if (duration == null) {
            return null;
        }
        return duration.toMillis(); // Convierte Duration a milisegundos para almacenar en la base de datos
    }

    @Override
    public Duration convertToEntityAttribute(Long value) {
        if (value == null) {
            return null;
        }
        return Duration.ofMillis(value); // Convierte milisegundos de la base de datos a Duration
    }
}
```

- En ocasiones, las **entidades** necesitan **relacionarse con otras** Hibernate nos da distintas opciones, que cubren todas las asociaciones posibles:
	- **@OneToOne:** Relaciona una instancia única con otra (Ej. Persona-Pasaporte). 
	- **@ManyToOne:** Relaciona varias instancias a una (Ej. Pedidos-Cliente).
	- **@OneToMany:** Relaciona una instancia a muchas (Ej. Departamento-Empleados).
	- **@ManyToMany:** Relaciona muchas instancias a muchos (Ej. Estudiantes-Curso).
![associations.png](/img/user/Im%C3%A1genes/associations.png)
		Estas anotaciones se pueden **complementar con distintas opciones**:
			
| Member       | Interpretation                                                                                    | Default value                                                      |
| ------------ | ------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| cascade      | Persistence operations which should cascade to the associated entity; a list of CascadeTypes      | {}                                                                 |
| fetch        | Whether the association is eagerly fetched or may be proxied                                      | LAZY for @OneToMany and @ManyToMany<br>EAGER for @ManyToOne 💀💀💀 |
| targetEntity | The associated entity class                                                                       | Determined from the attribute type declaration                     |
| optional     | For a @ManyToOne or @OneToOne association, whether the association can be null                    | true                                                               |
| mappedBy     | For a bidirectional association, an attribute of the associated entity which maps the association | By default, the association is assumed unidirectional              |
Ejemplo:
- Una **universidad** tiene **muchos departamentos** (`@OneToMany` bidireccional).
- Cada **departamento** tiene **muchos estudiantes** (`@OneToMany` bidireccional).
- Cada **estudiante** puede inscribirse en **muchos cursos** y cada curso puede tener **muchos estudiantes** (`@ManyToMany` bidireccional).
- Cada **estudiante** tiene una **dirección única** (`@OneToOne` unidireccional).
- Cada **curso** es enseñado por un **profesor único** (`@ManyToOne` unidireccional).
  
```
****Entidad University (Bidireccional @OneToMany con Department)****
@Entity
public class University {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @OneToMany(mappedBy = "university", cascade = CascadeType.ALL)
    private List<Department> departments;
}

****Entidad Department (Bidireccional @ManyToOne con University y @OneToMany con Student)****
@Entity
public class Department {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @ManyToOne
    @JoinColumn(name = "university_id", nullable = false)
    private University university;

    @OneToMany(mappedBy = "department", cascade = CascadeType.ALL)
    private List<Student> students;
}

****Entidad Student (Bidireccional @ManyToOne con Department, Unidireccional @OneToOne con Address, y @ManyToMany con Course)****
@Entity
public class Student {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @ManyToOne
    @JoinColumn(name = "department_id", nullable = false)
    private Department department;

    @OneToOne(cascade = CascadeType.ALL)
    @JoinColumn(name = "address_id", referencedColumnName = "id")
    private Address address;

    @ManyToMany
    @JoinTable(
        name = "student_course",
        joinColumns = @JoinColumn(name = "student_id"),
        inverseJoinColumns = @JoinColumn(name = "course_id")
    )
    private List<Course> courses;
}

****Entidad Address (Unidireccional @OneToOne con Student)****
@Entity
public class Address {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String city;

    private String street;

    private String zipCode;
}

****Entidad Course (Bidireccional @ManyToMany con Student y @ManyToOne con Professor)****
@Entity
public class Course {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;

    @ManyToMany(mappedBy = "courses")
    private List<Student> students;

    @ManyToOne
    @JoinColumn(name = "professor_id", nullable = false)
    private Professor professor;
}

****Entidad Professor (Unidireccional @OneToMany con Course)****
@Entity
public class Professor {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @OneToMany(mappedBy = "professor")
    private List<Course> courses;
}

```
### Mapeo Relacional de Objetos (ORM)
- Mapeo entidades a tablas de la BBDD, **por defecto**, **una entidad** se mapea **a una tabla de la BBDD con @Table**
```
@Entity
@Table(name="People")
class Person { ... }
```

| Annotation member | Purpose                                                                                        |
| ----------------- | ---------------------------------------------------------------------------------------------- |
| name              | The name of the mapped table                                                                   |
| schema 💀         | The schema to which the table belongs (No recomendable colocar el esquema en la propia clase)  |
| catalog 💀        | The catalog to which the table belongs (No recomendable colocar el esquema en la propia clase) |
| uniqueConstraints | One or more `@UniqueConstraint` annotations declaring multi-column unique constraints          |
| indexes           | One or more `@Index` annotations each declaring an index                                       |
Pero **en ocasiones**, **una entidad**, puede **mapearse** **con más de una tabla de la BBDD** usando **@SecondaryTable**/**@SecondaryTables**
```
@Entity
@Table(name="Books")
@SecondaryTable(name="Editions")
class Book { ... }
```

```
@Entity
@Table(name = "Products")
@SecondaryTables({
    @SecondaryTable(name = "ProductDetails", pkJoinColumns = @PrimaryKeyJoinColumn(name = "product_id")),
    @SecondaryTable(name = "ProductPricing", pkJoinColumns = @PrimaryKeyJoinColumn(name = "product_id"))
})
public class Product {}
```

| Annotation member | Purpose                                                                                                                                                                                                              |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name              | The name of the mapped table                                                                                                                                                                                         |
| schema 💀         | The schema to which the table belongs (No recomendable colocar el esquema en la propia clase)                                                                                                                        |
| catalog 💀        | The catalog to which the table belongs (No recomendable colocar el esquema en la propia clase)                                                                                                                       |
| uniqueConstraints | One or more `@UniqueConstraint` annotations declaring multi-column unique constraints                                                                                                                                |
| indexes           | One or more `@Index` annotations each declaring an index                                                                                                                                                             |
| pkJoinColumns     | One or more `@PrimaryKeyJoinColumn` annotations, specifying [primary key column mappings](https://docs.jboss.org/hibernate/orm/6.6/introduction/html_single/Hibernate_Introduction.html#primary-key-column-mappings) |
| foreignKey        | An `@ForeignKey` annotation specifying the name of the `FOREIGN KEY` constraint on the `@PrimaryKeyJoinColumn`s                                                                                                      |
- Es posible **asociar entidades** **mediante una tabla intermedia** en BBDD con **@JoinTable**. Es común en asociaciones ManyToMany, aunque también se usa en ManyToOne y OneToOne.
```
@Entity
class Book {
    @ManyToMany
    @JoinTable(name="BooksAuthors")
    Set<Author> authors;
}
```

| Annotation member  | Purpose                                                                                                                                                                                                                              |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| name               | The name of the mapped association table                                                                                                                                                                                             |
| schema 💀          | The schema to which the table belongs                                                                                                                                                                                                |
| catalog 💀         | The catalog to which the table belongs                                                                                                                                                                                               |
| uniqueConstraints  | One or more `@UniqueConstraint` annotations declaring multi-column unique constraints                                                                                                                                                |
| indexes            | One or more `@Index` annotations each declaring an index                                                                                                                                                                             |
| joinColumns        | One or more `@JoinColumn` annotations, specifying [foreign key column mappings](https://docs.jboss.org/hibernate/orm/6.6/introduction/html_single/Hibernate_Introduction.html#join-column-mappings) to the table of the owning side  |
| inverseJoinColumns | One or more `@JoinColumn` annotations, specifying [foreign key column mappings](https://docs.jboss.org/hibernate/orm/6.6/introduction/html_single/Hibernate_Introduction.html#join-column-mappings) to the table of the unowned side |
| foreignKey         | An `@ForeignKey` annotation specifying the name of the `FOREIGN KEY` constraint on the `joinColumns`s                                                                                                                                |
| inverseForeignKey  | An `@ForeignKey` annotation specifying the name of the `FOREIGN KEY` constraint on the `inverseJoinColumns`s                                                                                                                         |

## Anotaciones:
- **`(Clase)` @Entity:** Toda entidad debe estar anotada con esta anotación.
```
@Entity
class Book {
    Book() {}
}   
```
- **`(Clase)` @MappedSuperclass:** Clase raíz de la que heredan otras subclases, pero que no se asocia con ninguna tabla de la BBDD.
```
@MappedSuperclass
public class Versioned {
    @Version
    private Long version;
}

@Entity
public class Book extends Versioned {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
}

@Entity
public class AudioBook extends Versioned {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
}
```
- **`(Atributo-Getter)` @Id:** Identificador (Normalmente el id de la tabla de la BBDD), puede ponerse en un atributo o método de la clase. Solo puede existir 1.
```
@Entity
class Book {
    Book() {}
    
    (**Nunca poner los 2 solamente 1**)
	@Id 
    private Long id;

    @Id
    Long getId() { return id; }
    void setId(Long id) { this.id = id; }
}
```
  - **`(Atributo-Getter)` @Id + @GeneratedValue:** Un Id suele ser auto generado.
  
| Estrategia              | Tipo de Java        | Implementación                                                                                       |
| ----------------------- | ------------------- | ---------------------------------------------------------------------------------------------------- |
| GenerationType.UUID     | `UUID` or `String`  | A Java `UUID`                                                                                        |
| GenerationType.IDENTITY | `Long` or `Integer` | An identity or autoincrement column                                                                  |
| GenerationType.SEQUENCE | `Long` or `Integer` | A database sequence                                                                                  |
| GenerationType.TABLE    | `Long` or `Integer` | A database table                                                                                     |
| GenerationType.AUTO     | `Long` or `Integer` | Selects `SEQUENCE`, `TABLE`, or `UUID` based on the identifier type and capabilities of the database |
```
@Id @GeneratedValue(strategy=IDENTITY) Long id;
```

- **`(Clase)` @Embeddable:** Marcar una clase como un tipo que puede ser embebido en una entidad. Esto significa que la clase no representa una tabla independiente en la base de datos.
```
@Embeddable
public class BookId implements Serializable {
    private String isbn;
    private int printing;
    
    ...
}
```
- **`(Atributo-Getter)` @EmbeddedId**: Indica que el atributo de una entidad representa la clave primaria y que esta clave está compuesta por múltiples columnas.
```
@Entity
public class Book {
    @EmbeddedId
    private BookId bookId;

    private String title;
    private String author;
    
    ...
}
```

- **`(Atributo-Getter)` @Version**: Marca un campo o método como el atributo de versión para implementar el control de bloqueo optimista en una entidad.
```
@Entity
public class Book {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;

    private String author;

    @Version
    private Integer version;

    // Getters y setters
}
```
- **`(Atributo-Getter)` @OptimisticLock(excluded = false)**: Indica que la entidad debe ser incluida o no en el control de bloqueo optimista, sin excluir sus atributos del proceso de verificación.
```
@Entity
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String username;

    private String email;

    @OptimisticLock(excluded = true)  // Excluir este campo del bloqueo optimista
    private LocalDateTime lastLogin;

    @Version
    private Integer version;

    // Getters y setters
}   
```
- **`(Clase)` @OptimisticLocking(type = OptimisticLockType.ALL)**: Configura el bloqueo optimista para verificar todos los campos de la entidad durante la validación de conflictos.
```
@Entity
@OptimisticLocking(type = OptimisticLockType.DIRTY)  // Solo verifica campos modificados
public class Book {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;

    private String author;

    @Version
    private Integer version;

    // Getters y setters
}
```
-  **`(Atributo-Getter)` @NaturalId:** Permite definir atributos únicos de la clase (Ej. ISBN de un libro).
```
@Entity
public class Book {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;  // clave primaria generada automáticamente

    @NaturalId
    private String isbn;  // clave natural

    @NaturalId
    private int printing;  // otra parte de la clave natural

    // Otros atributos y métodos
}
```
- **`(Atributo-Getter)`** **@Basic(optional = false)**: Se realiza la comprobación en la capa lógica, es decir, el atributo no debe ser nulo en el modelo de dominio de Java.
```
@Basic(optional=false) String firstName;  
```
- **`(Atributo-Getter)`** **@Column(nullable=false):** Pertenece a la capa de mapeo y afecta al esquema de la base de datos (DDL). No afecta la lógica de la aplicación directamente, solo al esquema.
```
@Column(nullable=true) String firstName;
```
- **`(Atributo-Getter)`** **@NotNull**: Bean Validation para garantizar que el atributo no sea nulo.
```
@NotNull private String firstName;
```
- **`(Atributo-Getter)`@JavaType(BitSetJavaType.class)**: Representa un tipo Java específico y define cómo manejarlo.
```
  @JavaType(BitSetJavaType.class) BitSet bitSet;
```
- **`(Atributo-Getter)` @JdbcType(VarcharJdbcType.class):** Representa cómo se debe leer y escribir un tipo SQL desde/hacia JDBC.
```
  @JdbcType(VarcharJdbcType.class) long currentTimeMillis;
```
- **`(Clase)` @Converter(autoApply = true)**: Cuando un JavaType no puede convertir instancias al tipo esperado por su JdbcType.
```
@Converter(autoApply = true)
public class DurationToLongConverter implements AttributeConverter<Duration, Long> {
}
```
- **`(Atributo-Getter)` @Convert(converter = LongToTimestampConverter.class)**: Especifica el convertidor a usar.
```
  @JdbcType(TimestampJdbcType.class) 
  @Convert(converter = LongToTimestampConverter.class) 
  long currentTimeMillis;
```
- **`(Atributo-Getter)` @OneToOne:** Relaciona una instancia única con otra (Ej. Persona-Pasaporte). 
```
@OneToOne(cascade = CascadeType.ALL)
@JoinColumn(name = "address_id", referencedColumnName = "id")
private Address address;
```
- **`(Atributo-Getter)` @OneToMany:** Relaciona una instancia a muchas (Ej. Departamento-Empleados).
```
@OneToMany(mappedBy = "university", cascade = CascadeType.ALL)
private List<Department> departments;
```
- **`(Atributo-Getter)`@ManyToMany:** Relaciona muchas instancias a muchos (Ej. Estudiantes-Curso).
```
@ManyToMany(mappedBy = "courses")
private List<Student> students;
```
- **`(Atributo-Getter)` @ManyToOne:** Relaciona varias instancias a una (Ej. Pedidos-Cliente).
```
@ManyToOne
@JoinColumn(name = "university_id", nullable = false)
private University university;
```



## Vistazo rápido:
- **Table 1. Declaring entities and embeddable types**

|Annotation|Purpose|JPA-standard|
|---|---|---|
|`@Entity`|Declare an entity class|✔|
|`@MappedSuperclass`|Declare a non-entity class with mapped attributes inherited by an entity|✔|
|`@Embeddable`|Declare an embeddable type|✔|
|`@IdClass`|Declare the identifier class for an entity with multiple `@Id` attributes|✔|
- **Table 2. Declaring basic and embedded attributes**

|Annotation|Purpose| |JPA-standard|
|---|---|---|---|
|`@Id`|Declare a basic-typed identifier attribute||✔|
|`@Version`|Declare a version attribute||✔|
|`@Basic`|Declare a basic attribute|Default|✔|
|`@EmbeddedId`|Declare an embeddable-typed identifier attribute||✔|
|`@Embedded`|Declare an embeddable-typed attribute|Inferred|✔|
|`@Enumerated`|Declare an `enum`-typed attribute and specify how it is encoded|Inferred|✔|
|`@Array`|Declare that an attribute maps to a SQL `ARRAY`, and specify the length|Inferred|✖|
|`@ElementCollection`|Declare that a collection is mapped to a dedicated table||✔|

- **Table 3. Converters and compositional basic types**

|Annotation|Purpose|JPA-standard|
|---|---|---|
|`@Converter`|Register an `AttributeConverter`|✔|
|`@Convert`|Apply a converter to an attribute|✔|
|`@JavaType`|Explicitly specify an implementation of `JavaType` for a basic attribute|✖|
|`@JdbcType`|Explicitly specify an implementation of `JdbcType` for a basic attribute|✖|
|`@JdbcTypeCode`|Explicitly specify a JDBC type code used to determine the `JdbcType` for a basic attribute|✖|
|`@JavaTypeRegistration`|Register a `JavaType` for a given Java type|✖|
|`@JdbcTypeRegistration`|Register a `JdbcType` for a given JDBC type code|✖|

- **Table 4. System-generated identifiers**

| Annotation             | Purpose                                                                                              | JPA-standard |
| ---------------------- | ---------------------------------------------------------------------------------------------------- | ------------ |
| `@GeneratedValue`      | Specify that an identifier is system-generated                                                       | ✔            |
| `@SequenceGenerator`   | Define an id generated backed by on a database sequence                                              | ✔            |
| `@TableGenerator`      | Define an id generated backed by a database table                                                    | ✔            |
| `@IdGeneratorType`     | Declare an annotation that associates a custom `Generator` with each `@Id` attribute it annotates    | ✖            |
| `@ValueGenerationType` | Declare an annotation that associates a custom `Generator` with each `@Basic` attribute it annotates | ✖            |

- **Table 5. Declaring entity associations**

|Annotation|Purpose|JPA-standard|
|---|---|---|
|`@ManyToOne`|Declare the single-valued side of a many-to-one association (the owning side)|✔|
|`@OneToMany`|Declare the many-valued side of a many-to-one association (the unowned side)|✔|
|`@ManyToMany`|Declare either side of a many-to-many association|✔|
|`@OneToOne`|Declare either side of a one-to-one association|✔|
|`@MapsId`|Declare that the owning side of a `@OneToOne` association maps the primary key column|✔|
- **Table 6. Annotations for mapping tables**

| Annotation         | Purpose                                                                |
| ------------------ | ---------------------------------------------------------------------- |
| `@Table`           | Map an entity class to its primary table                               |
| `@SecondaryTable`  | Define a secondary table for an entity class                           |
| `@JoinTable`       | Map a many-to-many or many-to-one association to its association table |
| `@CollectionTable` | Map an `@ElementCollection` to its table                               |
- Table 7. Declaring entities and embeddable types

|Annotation|Purpose|JPA-standard|
|---|---|---|
|`@Entity`|Declare an entity class|✔|
|`@MappedSuperclass`|Declare a non-entity class with mapped attributes inherited by an entity|✔|
|`@Embeddable`|Declare an embeddable type|✔|
|`@IdClass`|Declare the identifier class for an entity with multiple `@Id` attributes|✔|

- **Table 8. Declaring basic and embedded attributes**

|Annotation|Purpose| |JPA-standard|
|---|---|---|---|
|`@Id`|Declare a basic-typed identifier attribute| |✔|
|`@Version`|Declare a version attribute| |✔|
|`@Basic`|Declare a basic attribute|Default|✔|
|`@EmbeddedId`|Declare an embeddable-typed identifier attribute| |✔|
|`@Embedded`|Declare an embeddable-typed attribute|Inferred|✔|
|`@Enumerated`|Declare an `enum`-typed attribute and specify how it is encoded|Inferred|✔|
|`@Array`|Declare that an attribute maps to a SQL `ARRAY`, and specify the length|Inferred|✖|
|`@ElementCollection`|Declare that a collection is mapped to a dedicated table| |✔|

- **Table 9. Converters and compositional basic types**

|Annotation|Purpose|JPA-standard|
|---|---|---|
|`@Converter`|Register an `AttributeConverter`|✔|
|`@Convert`|Apply a converter to an attribute|✔|
|`@JavaType`|Explicitly specify an implementation of `JavaType` for a basic attribute|✖|
|`@JdbcType`|Explicitly specify an implementation of `JdbcType` for a basic attribute|✖|
|`@JdbcTypeCode`|Explicitly specify a JDBC type code used to determine the `JdbcType` for a basic attribute|✖|
|`@JavaTypeRegistration`|Register a `JavaType` for a given Java type|✖|
|`@JdbcTypeRegistration`|Register a `JdbcType` for a given JDBC type code|✖|

- **Table 10. System-generated identifiers**

|Annotation|Purpose|JPA-standard|
|---|---|---|
|`@GeneratedValue`|Specify that an identifier is system-generated|✔|
|`@SequenceGenerator`|Define an id generated backed by on a database sequence|✔|
|`@TableGenerator`|Define an id generated backed by a database table|✔|
|`@IdGeneratorType`|Declare an annotation that associates a custom `Generator` with each `@Id` attribute it annotates|✖|
|`@ValueGenerationType`|Declare an annotation that associates a custom `Generator` with each `@Basic` attribute it annotates|✖|

- **Table 20. Declaring entity associations**

|Annotation|Purpose|JPA-standard|
|---|---|---|
|`@ManyToOne`|Declare the single-valued side of a many-to-one association (the owning side)|✔|
|`@OneToMany`|Declare the many-valued side of a many-to-one association (the unowned side)|✔|
|`@ManyToMany`|Declare either side of a many-to-many association|✔|
|`@OneToOne`|Declare either side of a one-to-one association|✔|
|`@MapsId`|Declare that the owning side of a `@OneToOne` association maps the primary key column|✔|
## Ejemplo completo:
```
@Entity
@Table(name = "Books")
public class Book {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String title;

    @Column(nullable = false, unique = true)
    private String isbn;

    @ManyToOne
    @JoinColumn(name = "publisher_id", nullable = false)
    private Publisher publisher;

    @ManyToMany
    @JoinTable(
        name = "BooksAuthors",
        joinColumns = @JoinColumn(name = "book_id"),
        inverseJoinColumns = @JoinColumn(name = "author_id")
    )
    private Set<Author> authors;

    // Getters and setters omitted for brevity
}

@Entity
@Table(name = "Authors")
public class Author {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;

    @ManyToMany(mappedBy = "authors")
    private Set<Book> books;

    // Getters and setters omitted for brevity
}

@Entity
@Table(name = "Publishers")
public class Publisher {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;

    @OneToMany(mappedBy = "publisher")
    private Set<Book> books;

    // Getters and setters omitted for brevity
}

@Repository
@Transactional
public class BookRepository {

    @PersistenceContext
    private EntityManager entityManager;

    public Book save(Book book) {
        entityManager.persist(book);
        return book;
    }

    public Book findById(Long id) {
        return entityManager.find(Book.class, id);
    }

    public List<Book> findAll() {
        return entityManager.createQuery("SELECT b FROM Book b", Book.class).getResultList();
    }

    public void deleteById(Long id) {
        Book book = findById(id);
        if (book != null) {
            entityManager.remove(book);
        }
    }
}

@Service
public class LibraryService {

    private final BookRepository bookRepository;

    @Autowired
    public LibraryService(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }

    public Book createBook(Book book) {
        return bookRepository.save(book);
    }

    public Book getBookById(Long id) {
        return bookRepository.findById(id);
    }

    public List<Book> getAllBooks() {
        return bookRepository.findAll();
    }

    public void deleteBook(Long id) {
        bookRepository.deleteById(id);
    }
}

@RestController
@RequestMapping("/api/books")
public class BookController {

    private final LibraryService libraryService;

    @Autowired
    public BookController(LibraryService libraryService) {
        this.libraryService = libraryService;
    }

    @PostMapping
    public Book createBook(@RequestBody Book book) {
        return libraryService.createBook(book);
    }

    @GetMapping("/{id}")
    public Book getBookById(@PathVariable Long id) {
        return libraryService.getBookById(id);
    }

    @GetMapping
    public List<Book> getAllBooks() {
        return libraryService.getAllBooks();
    }

    @DeleteMapping("/{id}")
    public void deleteBook(@PathVariable Long id) {
        libraryService.deleteBook(id);
    }
}


```