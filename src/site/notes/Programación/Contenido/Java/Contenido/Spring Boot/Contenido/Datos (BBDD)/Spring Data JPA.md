---
{"dg-publish":true,"permalink":"/programacion/contenido/java/contenido/spring-boot/contenido/datos-bbdd/spring-data-jpa/"}
---

# Enlaces interés
- **Documentación:** https://docs.spring.io/spring-data/jpa/reference/jpa/getting-started.html
- **Ejemplos:** https://github.com/spring-projects/spring-data-examples
# Inicio
## Conceptos clave
**JPA (Java Persistence API):** JPA es una especificación que define una serie de interfaces y anotaciones para facilitar el acceso y el mapeo entre los objetos de Java y las bases de datos relacionales. A través de JPA, se estandarizan operaciones comunes de persistencia como el mapeo de clases a tablas y la gestión del ciclo de vida de las entidades. El objetivo principal, es no perder las ventajas de POO. Se trabaja con de objetos llamados Entidades que se asocian a una tabla de la BBDD. Cada instancia de una entidad se corresponde a un registro de un tabla de la BBDD. Es una especificación, es decir, define los métodos que se pueden usar/implementar.

**Spring Data JPA:** Proporciona un conjunto de abstracciones y convenciones para simplificar la implementación de repositorios de datos. Spring Data JPA genera automáticamente implementaciones para los métodos de consulta comunes. Sin embargo, Spring Data JPA requiere que uses por debajo alguna implementación de JPA (por defecto utiliza Hibernate) para realizar las operaciones de base de datos.

Como se ha comentado anteriormente, Spring Data JPA especifica distintas implementaciones para JPA, para simplificarlo, pero todas ellas parten de la misma abstracción, Repository.
## Dependencias
### Dependencia específica de Spring Data JPA

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">



```
<dependency>
	<groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```

</div></div>

### Dependencia de Conexiones a BBDD (Ej. MySQL)

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">



```
<dependency>
	<groupId>com.mysql</groupId>
	<artifactId>mysql-connector-j</artifactId>
	<scope>runtime</scope>
</dependency>
```

</div></div>


## Repository e implementaciones
### Repository
```
@NoRepositoryBean
public interface Repository<T, ID> {}
```

**T:** Tipo de objeto de la clase que se usará en el Repository
**ID:** Tipo de dato que se usa como Id de la clase

### CrudRepository
**Descripción:** Interfaz para operaciones CRUD genéricas en un repositorio para un tipo específico.
**Javadoc:** https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/repository/CrudRepository.html
```
public interface CrudRepository<T, ID> extends Repository<T, ID> {

    <S extends T> S save(S entity);      

    Optional<T> findById(ID primaryKey);

    Iterable<T> findAll();              

    long count();                        

    void delete(T entity);              

    boolean existsById(ID primaryKey);  

    // … more functionality omitted.

}
```
**Ejemplo:**
```
public interface CountryCrudRepository extends CrudRepository<Country,Long> {}
```
**Métodos:**

| Modificador/Tipo              | Método                                      | Descripción                                                 |
| ----------------------------- | ------------------------------------------- | ----------------------------------------------------------- |
| long                          | count()                                     | Devuelve el número total de entidades.                      |
| void                          | delete(T entity)                            | Elimina una entidad dada.                                   |
| void                          | deleteAll()                                 | Borra todas las entidades.                                  |
| void                          | deleteAll(Iterable\<? extends T\> entities) | Elimina las entidades dadas.                                |
| void                          | deleteAllById(Iterable\<? extends ID\> ids) | Elimina todas las instancias del tipo T con los IDs dados.  |
| void                          | deleteById(ID id)                           | Elimina la entidad con el id dado.                          |
| boolean                       | existsById(ID id)                           | Devuelve si existe una entidad con el id dado.              |
| Iterable\<T\>                 | findAll()                                   | Devuelve todas las instancias del tipo.                     |
| Iterable\<T\>                 | findAllById(Iterable\<ID\> ids)             | Devuelve todas las instancias del tipo T con los IDs dados. |
| Optional\<T\>                 | findById(ID id)                             | Recupera una entidad por su id.                             |
| \<S extends T\> S             | save(S entity)                              | Guarda una entidad dada.                                    |
| \<S extends T\> Iterable\<S\> | saveAll(Iterable\<S\> entities)             | Guarda todas las entidades dadas.                           |
### ListCrudRepository
**Descripción:** Interfaz para operaciones CRUD genéricas en un repositorio para un tipo específico. Es una extensión de CrudRepository que devuelve List en lugar de Iterable cuando es aplicable.
**Javadoc:** https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/repository/ListCrudRepository.html
```
@NoRepositoryBean
public interface ListCrudRepository<T,ID> extends CrudRepository<T,ID>{}
```
**Ejemplo:**
```
public interface CountryCrudRepository extends ListCrudRepository<Country,Long> {}
```
**Métodos:** A todos los incluidos en CrudRepository se añaden los siguientes:

| Modificador/Tipo             | Método                          | Descripción                                                 |
| ---------------------------- | ------------------------------- | ----------------------------------------------------------- |
| List\<T\>                    | findAll()                       | Devuelve todas las instancias del tipo.                     |
| List\<T\>                    | findAllById(Iterable\<ID\> ids) | Devuelve todas las instancias del tipo T con los IDs dados. |
| \<S extends T\><br>List\<S\> | saveAll(Iterable\<S\> entities) | Guarda todas las entidades dadas.                           |
### PagingAndSortingRepository
**Descripción:** Fragmento de repositorio que proporciona métodos para recuperar entidades utilizando la abstracción de paginación y ordenación. En muchos casos esto se combinará con CrudRepository o similar o con métodos añadidos manualmente para proporcionar funcionalidad CRUD.
**Javadoc:** https://docs.spring.io/spring-data/commons/docs/3.4.0/api//org/springframework/data/repository/PagingAndSortingRepository.html
```
@NoRepositoryBean
public interface PagingAndSortingRepository<T,ID> extends Repository<T,ID>{}
```
**Ejemplo:**
```
@Entity
public class EmployeeEntity {
    @Id @GeneratedValue
    private Long id;
    private String firstName;
    private String lastName;
    private String email;
    //Setters, getters and toString()
}

@Repository
public interface EmployeeRepository
        extends PagingAndSortingRepository<EmployeeEntity, Long> {
	List<EmployeeEntity> findEmployeesByDepartmentId(long departmentId, Pageable pageable);
}


@RestController
@RequestMapping("/employees")
public class EmployeeController
{
    @Autowired
    EmployeeService service;

    @GetMapping
    public ResponseEntity<List<EmployeeEntity>> getAllEmployees(
                        @RequestParam(defaultValue = "0") Integer pageNo,
                        @RequestParam(defaultValue = "10") Integer pageSize,
                        @RequestParam(defaultValue = "id") String sortBy)
    {
        List<EmployeeEntity> list = service.getAllEmployees(pageNo, pageSize, sortBy);

        return new ResponseEntity<List<EmployeeEntity>>(list, new HttpHeaders(), HttpStatus.OK);
    }
}


@Service
public class EmployeeService
{
    @Autowired
    EmployeeRepository repository;

    public List<EmployeeEntity> getAllEmployees(Integer pageNo, Integer pageSize, String sortBy)
    {
        Pageable paging = PageRequest.of(pageNo, pageSize, Sort.by(sortBy));

        Page<EmployeeEntity> pagedResult = repository.findAll(paging);

        if(pagedResult.hasContent()) {
            return pagedResult.getContent();
        } else {
            return new ArrayList<EmployeeEntity>();
        }
    }
}

```
**Métodos:** 

| Modificador/Tipo             | Método                          | Descripción                                                                                |
| ---------------------------- | ------------------------------- | ------------------------------------------------------------------------------------------ |
| Page\<T\>                    | findAll(Pageable pageable)      | Returns a Page of entities meeting the paging restriction provided in the Pageable object. |
| List\<T\>                    | findAll(Sort sort)              | Devuelve todas las instancias del tipo T con los IDs dados.                                |
### ListPagingAndSortingRepository
**Descripción:** Proporciona métodos para recuperar entidades utilizando la abstracción de paginación y ordenación. Se trata de una extensión de PagingAndSortingRepository que devuelve List en lugar de Iterable cuando procede.
**Javadoc:** https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/repository/ListPagingAndSortingRepository.html
```
@NoRepositoryBean
public interface ListPagingAndSortingRepository<T,ID> extends PagingAndSortingRepository<T,ID>{}
```
**Métodos:** 

| Modificador/Tipo | Método    | Descripción                                                      |
| ---------------- | --------- | ---------------------------------------------------------------- |
| List\<T\>        | findAll() | Devuelve todas las entidades ordenadas según las opciones dadas. |
### ReactiveCrudRepository
**Javadoc:** https://docs.spring.io/spring-data/commons/docs/3.4.0/api//org/springframework/data/repository/reactive/ReactiveCrudRepository.html
### ReactiveSortingRepository
**Javadoc:** https://docs.spring.io/spring-data/commons/docs/3.4.0/api//org/springframework/data/repository/reactive/ReactiveSortingRepository.html
### RevisionRepository
**Javadoc:** https://docs.spring.io/spring-data/commons/docs/3.4.0/api//org/springframework/data/repository/history/RevisionRepository.html
### RxJava3CrudRepository
**Javadoc:** https://docs.spring.io/spring-data/commons/docs/3.4.0/api//org/springframework/data/repository/reactive/RxJava3CrudRepository.html
### RxJava3SortingRepository
**Javadoc:** https://docs.spring.io/spring-data/commons/docs/3.4.0/api//org/springframework/data/repository/reactive/RxJava3SortingRepository.html

## Creación de Querys
**Enlace:** https://docs.spring.io/spring-data/jpa/reference/jpa/query-methods.html#jpa.query.other-methods
### JPA Named Querys
#### Ejemplo:
```
interface PersonRepository extends Repository<Person, Long> {

  List<Person> findByEmailAddressAndLastname(EmailAddress emailAddress, String lastname);

  // Enables the distinct flag for the query
  List<Person> findDistinctPeopleByLastnameOrFirstname(String lastname, String firstname);
  List<Person> findPeopleDistinctByLastnameOrFirstname(String lastname, String firstname);

  // Enabling ignoring case for an individual property
  List<Person> findByLastnameIgnoreCase(String lastname);
  // Enabling ignoring case for all suitable properties
  List<Person> findByLastnameAndFirstnameAllIgnoreCase(String lastname, String firstname);

  // Enabling static ORDER BY for a query
  List<Person> findByLastnameOrderByFirstnameAsc(String lastname);
  List<Person> findByLastnameOrderByFirstnameDesc(String lastname);
}
```

#### ¿Cómo se forman?
El análisis sintáctico de los nombres de los métodos de consulta se divide en sujeto y predicado. La primera parte (find...By, exists...By) define el sujeto de la consulta, la segunda parte forma el predicado.
#### Clases contienen otras clases como atributos
Las expresiones de propiedades solo pueden referirse a una propiedad directa de la entidad gestionada, como se muestra en el ejemplo anterior. Al momento de crear la consulta, ya te aseguras de que la propiedad analizada pertenece a la clase de dominio gestionada. Sin embargo, también puedes definir restricciones atravesando propiedades anidadas. Considera la siguiente firma de método:

`List<Person> findByAddressZipCode(ZipCode zipCode);`

Supongamos que una clase `Person` tiene una propiedad `Address` que, a su vez, contiene un `ZipCode`. En ese caso, el método crea una referencia a la propiedad `x.address.zipCode` a través de una "traversal" o recorrido de propiedades.

El algoritmo de resolución comienza interpretando toda la parte del nombre (`AddressZipCode`) como una propiedad y verifica si la clase de dominio tiene una propiedad con ese nombre (en minúsculas iniciales). Si el algoritmo tiene éxito, utiliza esa propiedad. Si no, divide el nombre en partes siguiendo la notación camel-case desde el lado derecho, separándolo en un prefijo (`head`) y un sufijo (`tail`), e intenta encontrar la propiedad correspondiente. Por ejemplo, en nuestro caso: `AddressZip` y `Code`. Si encuentra una propiedad que coincide con el prefijo, toma el sufijo y continúa construyendo el árbol a partir de ahí, dividiendo nuevamente el sufijo de la misma manera. Si la primera división no coincide, el algoritmo mueve el punto de separación más a la izquierda (en este caso: `Address` y `ZipCode`) y continúa.

Aunque este proceso debería funcionar en la mayoría de los casos, es posible que el algoritmo seleccione una propiedad incorrecta. Supongamos que la clase `Person` también tiene una propiedad `addressZip`. En este caso, el algoritmo coincidiría con esta propiedad en la primera división, elegiría la propiedad incorrecta y fallaría (ya que probablemente el tipo de `addressZip` no tendría una propiedad `code`).

Para resolver esta ambigüedad, puedes usar un guion bajo (`_`) dentro del nombre del método para definir manualmente los puntos de recorrido. Entonces, el nombre de nuestro método sería el siguiente:

`List<Person> findByAddress_ZipCode(ZipCode zipCode);`

#### Métodos reservados:
- deleteAllById(Iterable\<ID\> identifiers)
- deleteById(ID identifier)
- existsById(ID identifier)
- findAllById(Iterable\<ID\> identifiers)
- findById(ID identifier)
#### Palabras reservadas:
###### Table 1. Query subject keywords

| **Palabras**                                                 | **Descripción**                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| find…By, read…By, get…By, <br>query…By, search…By, stream…By | General query method returning typically the repository type, a Collection or Streamable subtype or a result wrapper such as Page, GeoResults or any other store-specific result wrapper. Can be used as findBy…, findMyDomainTypeBy… or in combination with additional keywords. |
| exists…By                                                    | Exists projection, returning typically a boolean result.                                                                                                                                                                                                                          |
| count…By                                                     | Count projection returning a numeric result.                                                                                                                                                                                                                                      |
| delete…By, remove…By                                         | Delete query method returning either no result (void) or the delete count.                                                                                                                                                                                                        |
| …First\<number\>…, …Top\<number\>…                           | Limit the query results to the first \<number\> of results. This keyword can occur in any place of the subject between find (and the other keywords) and by.                                                                                                                      |
| …Distinct…                                                   | Use a distinct query to return only unique results. Consult the store-specific documentation whether that feature is supported. This keyword can occur in any place of the subject between find (and the other keywords) and by.                                                  |
###### Table 3. Query predicate keywords

| **Palabras**        | Palabras Querys                          |
| ------------------- | ---------------------------------------- |
| AND                 | And                                      |
| OR                  | Or                                       |
| AFTER               | After, IsAfter                           |
| BEFORE              | Before, IsBefore                         |
| CONTAINING          | Containing, IsContaining, Contains       |
| BETWEEN             | Between, IsBetween                       |
| ENDING_WITH         | EndingWith, IsEndingWith, EndsWith       |
| EXISTS              | Exists                                   |
| FALSE               | False, IsFalse                           |
| GREATER_THAN        | GreaterThan, IsGreaterThan               |
| GREATER_THAN_EQUALS | GreaterThanEqual, IsGreaterThanEqual     |
| IN                  | In, IsIn                                 |
| IS                  | Is, Equals, (or no keyword)              |
| IS_EMPTY            | IsEmpty, Empty                           |
| IS_NOT_EMPTY        | IsNotEmpty, NotEmpty                     |
| IS_NOT_NULL         | NotNull, IsNotNull                       |
| IS_NULL             | Null, IsNull                             |
| LESS_THAN           | LessThan, IsLessThan                     |
| LESS_THAN_EQUAL     | LessThanEqual, IsLessThanEqual           |
| LIKE                | Like, IsLike                             |
| NEAR                | Near, IsNear                             |
| NOT                 | Not, IsNot                               |
| NOT_IN              | NotIn, IsNotIn                           |
| NOT_LIKE            | NotLike, IsNotLike                       |
| REGEX               | Regex, MatchesRegex, Matches             |
| STARTING_WITH       | StartingWith, IsStartingWith, StartsWith |
| TRUE                | True, IsTrue                             |
| WITHIN              | Within, IsWithin                         |

**Table 4. Query predicate modifier keywords**

| **Palabras**                       | **Descripción**                                                                                                         |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------- |
| IgnoreCase, IgnoringCase       | Used with a predicate keyword for case-insensitive comparison.                                                      |
| AllIgnoreCase, AllIgnoringCase | Ignore case for all suitable properties. Used somewhere in the query method predicate.                              |
| OrderBy…                       | Specify a static sorting order followed by the property path and direction (e. g. OrderByFirstnameAscLastnameDesc). |
**Table 5. Complete examples with findBy**

| Keyword                | Sample                                                        | JPQL snippet                                                                            |
| ---------------------- | ------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| `Distinct`             | `findDistinctByLastnameAndFirstname`                          | `select distinct …​ where x.lastname = ?1 and x.firstname = ?2`                         |
| And                    | findByLastnameAndFirstname                                    | … where x.lastname = ?1 and x.firstname = ?2                                            |
| Or                     | findByLastnameOrFirstname                                     | … where x.lastname = ?1 or x.firstname = ?2                                             |
| `Is`, `Equals`         | `findByFirstname`,`findByFirstnameIs`,`findByFirstnameEquals` | `… where x.firstname = ?1` (or `… where x.firstname IS NULL` if the argument is `null`) |
| Between                | findByStartDateBetween                                        | `   … where x.startDate between ?1 and ?2`                                              |
| LessThan               | findByAgeLessThan                                             | … where x.age < ?1                                                                      |
| LessThanEqual          | findByAgeLessThanEqual                                        | `   … where x.age <= ?1`                                                                |
| GreaterThan            | findByAgeGreaterThan                                          | `   … where x.age > ?1`                                                                 |
| GreaterThanEqual       | findByAgeGreaterThanEqual                                     | … where x.age >= ?1                                                                     |
| After                  | findByStartDateAfter                                          | `   … where x.startDate > ?1`                                                           |
| Before                 | findByStartDateBefore                                         | `   … where x.startDate < ?1`                                                           |
| `IsNull`, `Null`       | findByAge(Is)Null                                             | `   … where x.age is null`                                                              |
| `IsNotNull`, `NotNull` | findByAge(Is)NotNull                                          | … where x.age is not null                                                               |
| Like                   | findByFirstnameLike                                           | … where x.firstname like ?1                                                             |
| NotLike                | findByFirstnameNotLike                                        | `   … where x.firstname not like ?1`                                                    |
| StartingWith           | findByFirstnameStartingWith                                   | `   … where x.firstname like ?1` (parameter bound with appended `%`)                    |
| EndingWith             | findByFirstnameEndingWith                                     | `… where x.firstname like ?1` (parameter bound with prepended `%`)                      |
| Containing             | findByFirstnameContaining                                     | `   … where x.firstname like ?1` (parameter bound wrapped in `%`)                       |
| OrderBy                | findByAgeOrderByLastnameDesc                                  | `   … where x.age = ?1 order by x.lastname desc`                                        |
| Not                    | findByLastnameNot                                             | `   … where x.lastname <> ?1`                                                           |
| In                     | findByAgeIn(Collection\<Age\> ages)                           | `   … where x.age in ?1`                                                                |
| NotIn                  | findByAgeNotIn(Collection\<Age\> ages)                        | `   … where x.age not in ?1`                                                            |
| True                   | findByActiveTrue()                                            | `   … where x.active = true`                                                            |
| False                  | findByActiveFalse()                                           | `   … where x.active = false`                                                           |
| IgnoreCase             | findByFirstnameIgnoreCase                                     | `   … where UPPER(x.firstname) = UPPER(?1)`                                             |
### @Query/@NativeQuery
**Ejemplos @Query:**
```
public interface UserRepository extends JpaRepository<User, Long> { 
	@Query("select u from User u where u.emailAddress = ?1") 
	User findByEmailAddress(String emailAddress); 
}
```

```
public interface MyRepository extends JpaRepository<User, Long>, QueryRewriter {

		@Query(value = "select original_user_alias.* from SD_USER original_user_alias",
                nativeQuery = true)
		List<User> findByNativeQuery(String param);
}
```

**Ejemplo @NativeQuery:**
```
public interface UserRepository extends JpaRepository<User, Long> { 
	@Query("select u from User u where u.emailAddress = ?1") 
	User findByEmailAddress(String emailAddress); 
}
```
### QueryRewriter
En ocasiones, por más que intentes aprovechar las características de Spring Data JPA, parece imposible ajustar completamente una consulta antes de que se envíe al EntityManager. Para estos casos, tienes la capacidad de interceptar la consulta justo antes de que se envíe al EntityManager y "reescribirla". Esto significa que puedes realizar cualquier alteración de último momento.

```
public interface MyRepository extends JpaRepository<User, Long>, QueryRewriter {

    @Query(
        value = "select original_user_alias.* from SD_USER original_user_alias",
        nativeQuery = true,
        queryRewriter = MyRepository.class
    )
    List<User> findByNativeQuery(String param);

    @Query(
        value = "select original_user_alias from User original_user_alias",
        queryRewriter = MyRepository.class
    )
    List<User> findByNonNativeQuery(String param);

    @Override
    default String rewrite(String query, Sort sort) {
        return query.replaceAll("original_user_alias", "rewritten_user_alias");
    }
}
```

También se pueden crear varios QueryRewiters distintos, deben incluirse en el contexto de la ampliación Spring, añadiéndoles la anotación @Component o incluyéndolos como @Bean en la propia configuración de la aplicación:

```
// QueryRewriter para cambiar alias en las consultas
public class AliasRewriter implements QueryRewriter {
    @Override
    public String rewrite(String query, Sort sort) {
        return query.replaceAll("original_alias", "updated_alias");
    }
}

// QueryRewriter para agregar filtros dinámicos
public class FilterRewriter implements QueryRewriter {
    @Override
    public String rewrite(String query, Sort sort) {
        // Agrega un filtro dinámico a todas las consultas
        return query + " WHERE is_active = true";
    }
}

// QueryRewriter para modificar el ordenamiento
public class SortRewriter implements QueryRewriter {
    @Override
    public String rewrite(String query, Sort sort) {
        // Si no hay un orden especificado, agregar uno predeterminado
        if (sort == null || sort.isUnsorted()) {
            return query + " ORDER BY created_at DESC";
        }
        return query;
    }
}
```

```
@Configuration
public class QueryRewriterConfig {

    @Bean
    public QueryRewriter aliasRewriter() {
        return new AliasRewriter();
    }

    @Bean
    public QueryRewriter filterRewriter() {
        return new FilterRewriter();
    }

    @Bean
    public QueryRewriter sortRewriter() {
        return new SortRewriter();
    }
}
```

```
public interface UserRepository extends JpaRepository<User, Long> {

    
    @Query(value = "SELECT original_alias.* FROM user_table original_alias",
           queryRewriter = AliasRewriter.class)
    List<User> findWithAliasRewriter();

    
    @Query(value = "SELECT u FROM User u",
           queryRewriter = FilterRewriter.class)
    List<User> findWithFilterRewriter();

    
    @Query(value = "SELECT u FROM User u",
           queryRewriter = SortRewriter.class)
    List<User> findWithSortRewriter(Sort sort);
}
```

### Transacciones (@Transactional)
Por defecto, los métodos heredados de CrudRepository adoptan la configuración transaccional de SimpleJpaRepository. Para las operaciones de lectura, la configuración transaccional tiene el atributo readOnly activado (true). Para el resto de las operaciones, se utiliza una anotación genérica @Transactional, de modo que se aplica la configuración de transacciones por defecto.
Si necesitas ajustar la configuración transaccional de un método declarado en un repositorio, puedes redeclarar el método en la interfaz del repositorio. Por ejemplo:
```
public interface UserRepository extends CrudRepository<User, Long> {

  @Override
  @Transactional(timeout = 10)
  public List<User> findAll();

}
```

También se puede tener una transacción cuando se usan múltiples repositorios, para ello se asigna @Transactional al propio servicio, que haga uso de múltiples repositorios. En este caso, la configuración transaccional de los repositorios se ignora, ya que la configuración de la transacción externa prevalece. Por ejemplo:
```
@Service
public class UserManagementImpl implements UserManagement {

  private final UserRepository userRepository;
  private final RoleRepository roleRepository;

  public UserManagementImpl(UserRepository userRepository, RoleRepository roleRepository) {
    this.userRepository = userRepository;
    this.roleRepository = roleRepository;
  }

  @Transactional
  public void addRoleToAllUsers(String roleName) {

    Role role = roleRepository.findByName(roleName);
    for (User user : userRepository.findAll()) {
      user.addRole(role);
      userRepository.save(user);
    }
  }
}

```

#### Métodos de consulta transaccionales
Los métodos de consulta declarados (incluyendo los métodos `default`) no tienen una configuración transaccional aplicada por defecto. Para ejecutarlos dentro de una transacción, utiliza la anotación `@Transactional` en la interfaz del repositorio:
```
@Transactional(readOnly = true)
interface UserRepository extends JpaRepository<User, Long> {

  List<User> findByLastname(String lastname);

  @Modifying
  @Transactional
  @Query("delete from User u where u.active = false")
  void deleteInactiveUsers();
}
```

- El método `findByLastname` se ejecutará con el atributo `readOnly` activado, ya que solo realiza lecturas de datos.
- El método `deleteInactiveUsers` utiliza la anotación `@Modifying` para indicar que ejecuta una consulta de modificación, y redefine la configuración transaccional para desactivar el atributo `readOnly`.

#### Consideraciones sobre transacciones de solo lectura
- Marcar una transacción como `readOnly` no garantiza que no se ejecuten consultas de modificación, aunque algunos motores de base de datos rechazarán operaciones como `INSERT` o `UPDATE` dentro de una transacción de solo lectura.
- El atributo `readOnly` sirve como una pista para el controlador JDBC subyacente, optimizando el rendimiento.
- En el caso de Hibernate, cuando se configura una transacción como `readOnly`, el modo de _flush_ se establece en `NEVER`, lo que evita verificaciones de cambios en objetos, mejorando significativamente el rendimiento en estructuras de datos grandes.