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

| Keyword                | Sample                               | JPQL snippet                                                    |
| ---------------------- | ------------------------------------ | --------------------------------------------------------------- |
| `Distinct`             | `findDistinctByLastnameAndFirstname` | `select distinct …​ where x.lastname = ?1 and x.firstname = ?2` |
| And                    |                                      |                                                                 |
| Or                     |                                      |                                                                 |
| `Is`, `Equals`         |                                      |                                                                 |
| Between                |                                      |                                                                 |
| LessThan               |                                      |                                                                 |
| LessThanEqual          |                                      |                                                                 |
| GreaterThan            |                                      |                                                                 |
| GreaterThanEqual       |                                      |                                                                 |
| After                  |                                      |                                                                 |
| Before                 |                                      |                                                                 |
| `IsNull`, `Null`       |                                      |                                                                 |
| `IsNotNull`, `NotNull` |                                      |                                                                 |
| Like                   |                                      |                                                                 |
| NotLike                |                                      |                                                                 |
| StartingWith           |                                      |                                                                 |
| EndingWith             |                                      |                                                                 |
| Containing             |                                      |                                                                 |
| OrderBy                |                                      |                                                                 |
| Not                    |                                      |                                                                 |
| In                     |                                      |                                                                 |
| NotIn                  |                                      |                                                                 |
| True                   |                                      |                                                                 |
| False                  |                                      |                                                                 |
| IgnoreCase             |                                      |                                                                 |

| \|Keyword\|Sample\|JPQL snippet\|<br>\|---\|---\|---\|<br>\|`Distinct`\|`findDistinctByLastnameAndFirstname`\|`select distinct …​ where x.lastname = ?1 and x.firstname = ?2`\|<br>\|`And`\|`findByLastnameAndFirstname`\|`… where x.lastname = ?1 and x.firstname = ?2`\|<br>\|`Or`\|`findByLastnameOrFirstname`\|`… where x.lastname = ?1 or x.firstname = ?2`\|<br>\|`Is`, `Equals`\|`findByFirstname`,`findByFirstnameIs`,`findByFirstnameEquals`\|`… where x.firstname = ?1` (or `… where x.firstname IS NULL` if the argument is `null`)\|<br>\|`Between`\|`findByStartDateBetween`\|`… where x.startDate between ?1 and ?2`\|<br>\|`LessThan`\|`findByAgeLessThan`\|`… where x.age < ?1`\|<br>\|`LessThanEqual`\|`findByAgeLessThanEqual`\|`… where x.age <= ?1`\|<br>\|`GreaterThan`\|`findByAgeGreaterThan`\|`… where x.age > ?1`\|<br>\|`GreaterThanEqual`\|`findByAgeGreaterThanEqual`\|`… where x.age >= ?1`\|<br>\|`After`\|`findByStartDateAfter`\|`… where x.startDate > ?1`\|<br>\|`Before`\|`findByStartDateBefore`\|`… where x.startDate < ?1`\|<br>\|`IsNull`, `Null`\|`findByAge(Is)Null`\|`… where x.age is null`\|<br>\|`IsNotNull`, `NotNull`\|`findByAge(Is)NotNull`\|`… where x.age is not null`\|<br>\|`Like`\|`findByFirstnameLike`\|`… where x.firstname like ?1`\|<br>\|`NotLike`\|`findByFirstnameNotLike`\|`… where x.firstname not like ?1`\|<br>\|`StartingWith`\|`findByFirstnameStartingWith`\|`… where x.firstname like ?1` (parameter bound with appended `%`)\|<br>\|`EndingWith`\|`findByFirstnameEndingWith`\|`… where x.firstname like ?1` (parameter bound with prepended `%`)\|<br>\|`Containing`\|`findByFirstnameContaining`\|`… where x.firstname like ?1` (parameter bound wrapped in `%`)\|<br>\|`OrderBy`\|`findByAgeOrderByLastnameDesc`\|`… where x.age = ?1 order by x.lastname desc`\|<br>\|`Not`\|`findByLastnameNot`\|`… where x.lastname <> ?1`\|<br>\|`In`\|`findByAgeIn(Collection<Age> ages)`\|`… where x.age in ?1`\|<br>\|`NotIn`\|`findByAgeNotIn(Collection<Age> ages)`\|`… where x.age not in ?1`\|<br>\|`True`\|`findByActiveTrue()`\|`… where x.active = true`\|<br>\|`False`\|`findByActiveFalse()`\|`… where x.active = false`\|<br>\|`IgnoreCase`\|`findByFirstnameIgnoreCase`\|`… where UPPER(x.firstname) = UPPER(?1)`\| |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |




### @Query/@NativeQuery
## Ejemplo completo
