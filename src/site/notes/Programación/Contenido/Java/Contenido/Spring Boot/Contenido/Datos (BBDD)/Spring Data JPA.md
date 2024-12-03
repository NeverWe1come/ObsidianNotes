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
**Métodos:** Todos los incluidos en 

| Modificador/Tipo             | Método                                                                                                                                                                                                                                                                                                                                                | Descripción                                                                                |
| ---------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| Page\<T\>                    | findAll(Pageable pageable)                                                                                                                                                                                                                                                                                                                            | Returns a Page of entities meeting the paging restriction provided in the Pageable object. |
| List\<T\>                    | [findAll](https://docs.spring.io/spring-data/commons/docs/3.4.0/api//org/springframework/data/repository/PagingAndSortingRepository.html#findAll(org.springframework.data.domain.Sort))([Sort](https://docs.spring.io/spring-data/commons/docs/3.4.0/api//org/springframework/data/domain/Sort.html "class in org.springframework.data.domain") sort) | Devuelve todas las instancias del tipo T con los IDs dados.                                |
| \<S extends T\><br>List\<S\> | saveAll(Iterable\<S\> entities)                                                                                                                                                                                                                                                                                                                       | Guarda todas las entidades dadas.                                                          |

### ListPagingAndSortingRepository
**Javadoc:** https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/repository/ListPagingAndSortingRepository.html
```
@NoRepositoryBean
public interface ListPagingAndSortingRepository<T,ID> extends PagingAndSortingRepository<T,ID>{}
```
**Ejemplo:**
```
public interface CountryCrudRepository extends ListCrudRepository<Country,Long> {}
```
**Métodos:** Todos los incluidos en 

| Modificador/Tipo             | Método                          | Descripción                                                 |
| ---------------------------- | ------------------------------- | ----------------------------------------------------------- |
| List\<T\>                    | findAll()                       | Devuelve todas las instancias del tipo.                     |
| List\<T\>                    | findAllById(Iterable\<ID\> ids) | Devuelve todas las instancias del tipo T con los IDs dados. |
| \<S extends T\><br>List\<S\> | saveAll(Iterable\<S\> entities) | Guarda todas las entidades dadas.                           |

## Ejemplo completo
