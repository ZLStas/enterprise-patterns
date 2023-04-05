```java
@RestController
@RequestMapping("/users")
public class UserController {

    private final JavaMailSender javaMailSender;
    private final PasswordEncoder passwordEncoder;

    public UserController(JavaMailSender javaMailSender, PasswordEncoder passwordEncoder) {
        this.javaMailSender = javaMailSender;
        this.passwordEncoder = passwordEncoder;
    }

    @PostMapping("/")
    @Transactional
    public ResponseEntity<User> createUser(@RequestBody CreateUserRequest request) {
        User user = new User(request.getEmail(), passwordEncoder.encode(request.getPassword()));

        // Save the user to the database
        try {
            PreparedStatement stmt = JdbcUtils.getConnection().prepareStatement("INSERT INTO users(email, password) VALUES (?, ?)");
            stmt.setString(1, user.getEmail());
            stmt.setString(2, user.getPassword());
            stmt.executeUpdate();
        } catch (SQLException e) {
            // handle exception
        }

        // Send a confirmation email to the user
        SimpleMailMessage message = new SimpleMailMessage();
        message.setTo(user.getEmail());
        message.setSubject("Welcome to our platform!");
        message.setText("Thank you for joining our platform!");
        javaMailSender.send(message);

        return ResponseEntity.created(URI.create("/users/" + user.getId())).body(user);
    }

}
```

