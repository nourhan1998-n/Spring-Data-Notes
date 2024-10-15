# Spring-Data-Notes
Spring Data's **repository abstraction** simplifies interaction with the data layer by allowing developers to define repository interfaces rather than writing boilerplate code. These interfaces provide automatic implementations of basic CRUD (Create, Read, Update, Delete) operations without writing any method logic. Below, I’ll explain how this works with examples, focusing on **CRUD operations**, **custom query methods**, and **pagination**.

### 1. **Basic CRUD Operations with `JpaRepository`**

Suppose you have an entity class `User` like this:

```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String firstName;
    private String lastName;
    private String email;
    
    // Getters and setters omitted for brevity
}
```

Now, you create a repository interface that extends `JpaRepository`:

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    // No need to implement any methods, everything is provided by Spring Data
}
```

Since `UserRepository` extends `JpaRepository`, you automatically get the following methods:
- `save(S entity)` – To save or update a user.
- `findById(Long id)` – To retrieve a user by their ID.
- `findAll()` – To retrieve all users.
- `deleteById(Long id)` – To delete a user by their ID.
- `count()` – To count the number of users in the database.

#### Example Usage of CRUD Operations:

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    // Create a new user
    public User createUser(User user) {
        return userRepository.save(user); // Save method will insert into the DB
    }

    // Retrieve user by ID
    public Optional<User> getUserById(Long id) {
        return userRepository.findById(id); // findById returns an Optional
    }

    // Retrieve all users
    public List<User> getAllUsers() {
        return userRepository.findAll(); // findAll fetches all users
    }

    // Update an existing user
    public User updateUser(User user) {
        return userRepository.save(user); // save method works for both insert and update
    }

    // Delete a user
    public void deleteUser(Long id) {
        userRepository.deleteById(id); // deleteById deletes a user by ID
    }
}
```

### 2. **Custom Query Methods**
Spring Data provides the ability to create custom query methods based on method naming conventions. Spring generates the queries behind the scenes.

#### Example: Find users by last name

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    // Spring Data will automatically generate the query for this method
    List<User> findByLastName(String lastName);
}
```

This query translates to `SELECT * FROM user WHERE last_name = :lastName`. The method name `findByLastName` is enough for Spring Data to understand how to generate the query.

#### Example: Find users by first name and last name

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    List<User> findByFirstNameAndLastName(String firstName, String lastName);
}
```

Spring Data will generate the query `SELECT * FROM user WHERE first_name = :firstName AND last_name = :lastName`.

### 3. **Custom Queries with `@Query` Annotation**
If the derived query methods aren't sufficient, you can define your custom queries using the `@Query` annotation.

#### Example: Custom JPQL Query

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {

    @Query("SELECT u FROM User u WHERE u.email = ?1")
    User findUserByEmail(String email);
}
```

This uses JPQL (Java Persistence Query Language), and Spring Data will generate the corresponding query.

#### Example: Native SQL Query

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {

    @Query(value = "SELECT * FROM user WHERE email = ?1", nativeQuery = true)
    User findUserByEmailNative(String email);
}
```

In this case, you’re directly using SQL instead of JPQL.

### 4. **Pagination and Sorting**
Spring Data also supports pagination and sorting out of the box. You just need to add `Pageable` or `Sort` parameters to your query methods.

#### Example: Pagination

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    Page<User> findByLastName(String lastName, Pageable pageable);
}
```

#### Example: Pagination Usage

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public Page<User> getUsersByLastNamePaginated(String lastName, int page, int size) {
        Pageable pageable = PageRequest.of(page, size);
        return userRepository.findByLastName(lastName, pageable);
    }
}
```

This would return a paginated result set. The `PageRequest.of(page, size)` specifies the page number and the number of records per page.

#### Example: Sorting

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    List<User> findByLastName(String lastName, Sort sort);
}
```

And you can use this like:

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public List<User> getUsersSortedByFirstName(String lastName) {
        return userRepository.findByLastName(lastName, Sort.by("firstName").ascending());
    }
}
```

### Summary of Examples:
- **Basic CRUD**: Methods like `save()`, `findById()`, `findAll()`, and `deleteById()` are automatically available.
- **Custom Query Methods**: Use method names like `findByLastName()` to automatically generate queries.
- **@Query**: Write custom JPQL or native SQL queries if needed.
- **Pagination and Sorting**: Spring Data supports paginated queries and sorting using `Pageable` and `Sort`.

Spring Data reduces the effort of writing boilerplate code for database access, making it more efficient to develop data-driven applications.
