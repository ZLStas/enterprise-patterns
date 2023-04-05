# Spring Data Rest

``` java
@Entity
public class Employee {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String firstName;

    private String lastName;
    
    ...

    // constructors, getters and setters
}
```

```java
@RepositoryRestResource(path = "employees")
public interface EmployeeRepository extends JpaRepository<Employee, Long> {

}
```

``` json
POST /employees HTTP/1.1
Host: example.com
Content-Type: application/json

{
"firstName": "John",
"lastName": "Doe",
"email": "johndoe@example.com",
"department": "Sales"
}
```

``` json
HTTP/1.1 201 Created
Location: https://example.com/employees/1
Content-Type: application/json

{
  "id": 1,
  "firstName": "John",
  "lastName": "Doe",
  "email": "johndoe@example.com",
  "department": "Sales",
  "_links": {
    "self": {
      "href": "https://example.com/employees/1"
    },
    "employee": {
      "href": "https://example.com/employees/1"
    },
    "update": {
      "href": "https://example.com/employees/1",
      "method": "PUT"
    },
    "delete": {
      "href": "https://example.com/employees/1",
      "method": "DELETE"
    },
    "manager": {
      "href": "https://example.com/employees/1/manager"
    },
    "department": {
      "href": "https://example.com/departments/Sales"
    }
  }
}
```