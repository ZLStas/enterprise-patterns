# Table Module example

``` java
@Setter
@Getter
@ToString
@EqualsAndHashCode
@AllArgsConstructor
public class User {
private int id;
private String username;
private String password;
}
```

```java
public class UserTableModule {
  private final DataSource dataSource;
  private Connection connection = null;
  private ResultSet resultSet = null;
  private PreparedStatement preparedStatement = null;

  public UserTableModule(final DataSource userDataSource) {
    this.dataSource = userDataSource;
  }
  
  /**
   * Login using username and password.
   *
   * @param username the username of a user
   * @param password the password of a user
   * @return the execution result of the method
   * @throws SQLException if any error
   */
  public int login(final String username, final String password) throws SQLException {
  		// Method implementation, business logic + data access

  }

  /**
   * Register a new user.
   *
   * @param user a user instance
   * @return the execution result of the method
   * @throws SQLException if any error
   */
  public int registerUser(final User user) throws SQLException {
  		// Method implementation, business logic + data access
  }
}
```

```java
// Create data source and create the user table.
final var dataSource = createDataSource();
createSchema(dataSource);
userTableModule = new UserTableModule(dataSource);

//Initialize two users.
var user1 = new User(1, "123456", "123456");
var user2 = new User(2, "test", "password");

//Login and register using the instance of userTableModule.
userTableModule.registerUser(user1);
userTableModule.login(user1.getUsername(), user1.getPassword());
userTableModule.login(user2.getUsername(), user2.getPassword());
userTableModule.registerUser(user2);
userTableModule.login(user2.getUsername(), user2.getPassword());

deleteSchema(dataSource);
```

```
12:22:13.095 [main] INFO com.example.tablemodule.UserTableModule - Register successfully!
12:22:13.117 [main] INFO com.example.tablemodule.UserTableModule - Login successfully!
12:22:13.128 [main] INFO com.example.tablemodule.UserTableModule - Fail to login!
12:22:13.136 [main] INFO com.example.tablemodule.UserTableModule - Register successfully!
12:22:13.144 [main] INFO com.example.tablemodule.UserTableModule - Login successfully!
```