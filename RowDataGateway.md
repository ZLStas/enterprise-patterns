# Row Data Gateway

```java
public class UserRowDataGateway {
    private Connection connection;
    private int id;
    private String email;
    private String password;

    public UserRowDataGateway(Connection connection, int id) throws SQLException {
        this.connection = connection;
        this.id = id;
        PreparedStatement statement = connection.prepareStatement("SELECT * FROM users WHERE id = ?");
        statement.setInt(1, id);
        ResultSet resultSet = statement.executeQuery();
        if (resultSet.next()) {
            this.email = resultSet.getString("email");
            this.password = resultSet.getString("password");
        }
        resultSet.close();
        statement.close();
    }

    public int getId() {
        return id;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) throws SQLException {
        this.email = email;
        PreparedStatement statement = connection.prepareStatement("UPDATE users SET email = ? WHERE id = ?");
        statement.setString(1, email);
        statement.setInt(2, id);
        statement.executeUpdate();
        statement.close();
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) throws SQLException {
        this.password = password;
        PreparedStatement statement = connection.prepareStatement("UPDATE users SET password = ? WHERE id = ?");
        statement.setString(1, password);
        statement.setInt(2, id);
        statement.executeUpdate();
        statement.close();
    }

    public void delete() throws SQLException {
        PreparedStatement statement = connection.prepareStatement("DELETE FROM users WHERE id = ?");
        statement.setInt(1, id);
        statement.executeUpdate();
        statement.close();
    }
}
```

