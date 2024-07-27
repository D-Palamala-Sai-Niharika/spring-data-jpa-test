# spring-data-jpa
- Jpa(Java Persistence Api) - specifications for object relational mapping
- Hibernate(ORM Framework) - jpa implementation

## Repository
- Abstraction and Encapsulation layer for database operations
- Default implementation
- CrudRepository
  - Extends Repository
  - provides CRUD functions
  - **Optional<T>** findById(ID primaryKey); - Optional return type basically forces to add exception explicitly. That is why you get error when you do not write orElseThrow for findById(id) method. To know more about use of Optional : https://www.perplexity.ai/search/optional-return-type-in-jpa-us-HkbMPqSES_G2IEfNRKkf8A 
 ```
<S extends T> S save(S entity); 
<S extends T> Iterable<S> saveAll(Iterable<S> entities);
Optional<T> findById(ID primaryKey);
boolean existsById(ID id);
Iterable<T> findAll();
Iterable<T> findAllById(Iterable<ID> ids);
long count();
void deleteById(ID id);
void delete(T entity);
void deleteAllById(Iterable<? extends ID> ids);
void delete(Iterable<? extends T> entities);
void deleteAll();
```

- PagingAndSortingRepository
  - Extends CrudRepository
  - provides methods to do pagination and sort records
 ```
findAll(Pageable pageable)
Pageable object with following properties 
Page size
Current page number
Sorting
findAll(Sort sort)
Sort Object with the Property on Which the sorting is to be done
Sort.by(propName)
```
- JpaRepository
  - Extends PagingandSortingRepository
  - Provides Methods such as flushing the persistence context and delete records in a batch
  - Querying methods return List's instead of Iterable’s

## @Embeddable and @Embedded
- used to embed the attributes of one class ( without creating any entity) and map them to an entity
- **@Embeddable** - declare that a class will be embedded by other entities
- **@Embedded** - used to embed a type into another entity
- **@AttributeOverrides**, **@AttributeOverride** - override the column properties of embedded type

## Custom / Derived Query Methods - Fetching data
- Spring data jpa implements respective jpql queries based on the method names defined
- simple queries
- **Subject** : findBy, queryBy, readBy, countBy, existsBy, getBy prefix and for limiting result: Distinct, Top/First
- **Predicate** :
   - Concatenate - And , Or
   - WHERE Condition
     - JPA Like Query - StartingWith, EndingWith, Containing, Like, NotLike, IgnoreCase
     - JPA Boolean Query - True, False
     - JPA Comparison Query - Between, LessThan, LessThanEqual, GreaterThan, GreaterThanEqual and for Date : After, Before, Between
     - Is, Equals, Not, IsNot, Null, IsNull, NotNull, IsNotNull
     - JPA Sorting Query - OrderBy, OrderBy Asc, OrderBy Desc 
- https://www.bezkoder.com/jpa-repository-query/
- **Remember JPQL queries are excuted based on class not based on tables.so 
you should consider class names and properties instead of table and column names**

## @Query Annotation
- complex queries
- Types:
  - **JPQL/ HQL Queries**
  - **Native Queries**
- Query Named Params
  -  named parameters instead of ‘?’ placeholder (ex: ?1 -> :email)
  -  Use **@Param("")** to match the name in the query - findByEmail(@Param("email") String emailId)
- **@Modifying** - JPQL/SQL queries should be annotated with @Modifying in order to modify the values in the database
- **@Transactional** - when modifying data there has to be transaction for it - insert, update, delete - Ideally added in service layer. can be used at both method level or class level where data being modified ex : delete, update(save), insert(save)
  
## Jpa Relational Mappings - P.K and F.K
- Types :
  - One to One - @OneToOne - https://www.bezkoder.com/jpa-one-to-one/
  - One to Many - @OneToMany - https://www.bezkoder.com/jpa-one-to-many/
  - Many to One - @ManyToOne
  - Many to Many - @ManyToMany - https://www.bezkoder.com/jpa-many-to-many/ 
- mappings :
  - Uni Directional - only one entity has reference(details) of other
  - Bi Directioanl - both shd have references(details) of each other but fk in only referencing table
- Referencing side that contains fk i.e owning side - having reference of referenced entity(ex: many) - unidirectional
  - @JoinColumn(name="", referencedColumnName="")
  - @OneToOne(cascade=CascadeType.ALL, fetch=FetchType.LAZY, optional = false )
  - @MapsId - The primary purpose of @MapsId is to indicate that the primary key of an entity is derived from the primary key of another entity.This annotation is particularly useful in one-to-one relationships and allows you to share the same primary key between two related entities 
- Referenced Side - bidirectional - just to get the refernce of other mapped entity details without any fk creation
  - @OneToOne(mappedBy="")
- cascade - pass properties or permissions from parent to child (trying to access child without creating parent/ avoid data integrety issues)