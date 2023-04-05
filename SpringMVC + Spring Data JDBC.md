```java

@AllArgsConstructor
public class Order {

    @Id
    private Long id;

    private String name;

    private Address  shippingAddress;

    private Money moneyAmount;

    private Status status;

    public static Order of(String name) {
        return new Order(null, name, null, null, Status.NEW);
    }

    public void ship(Address address) {
        if (this.status != Status.PAID) {
            throw new IllegalStateException("Order must be paid before shipping");
        }
        this.shippingAddress = address;
        this.status = Status.SHIPPED;
    }

    public void pay(Money money) {
        if (this.status != Status.NEW) {
            throw new IllegalStateException("You can't pay twice!");
        }
        this.moneyAmount = money;
        this.status = Status.PAID;
    }

    public enum Status {
        NEW("New order"),
        PAID("Order has been paid"),
        SHIPPED("Order has been shipped"),
        DELIVERED("Order has been delivered"),
        CANCELLED("Order has been cancelled");

        private String description;

        Status(String description) {
            this.description = description;
        }
    }

}

```

``` java
@Service
@RequiredArgsConstructor
public class OrderService {

    private final OrderRepository orderRepository;

    public Order ship(Long orderId, Address address) {
        var order = orderRepository.findById(orderId).orElseThrow(() -> new OrderNotFoundException(orderId));
        order.ship(address);
        orderRepository.save(order);
        // sent some notification or produce event

        return order;
    }

    public Iterable<Order> getAllOrders() {
        return orderRepository.findAll();
    }

    public Order getOrderById(Long id) {
        return orderRepository.findById(id).orElseThrow(() -> new OrderNotFoundException(id));
    }

    public Order orderProduct(String name){
        Order order = Order.of(name);
        orderRepository.save(order);

        return order;
    }

    public Order pay(Long orderId, Money money) {
        Order order = orderRepository.findById(orderId).orElseThrow(() -> new OrderNotFoundException(orderId));
        order.pay(money);
        return order;
    }

}
```

```java
@AllArgsConstructor
@RestController
public class OrderController {

    private final OrderService orderService;

    @PostMapping("/order-shipment/{orderId}")
    public ResponseEntity<Order> shipOrder(@PathVariable Long orderId, @RequestBody Address address) {
        Order order = orderService.ship(orderId, address);
        // logic to add hypermedia
        return ResponseEntity.ok(order);
    }

    @PostMapping("/order-payment/{orderId}")
    public ResponseEntity<Order> payForOrder(@PathVariable Long orderId, @RequestBody Money money) {

        Order order = orderService.pay(orderId, money);
        // logic to add hypermedia
        return ResponseEntity.ok(order);
    }

    @GetMapping
    public Iterable<Order> getAllOrders() {
        // logic to add hypermedia
        return orderService.getAllOrders();
    }


    @GetMapping("orders/{id}")
    public ResponseEntity<Order> getOrderById(@PathVariable(value = "id") Long orderId) {
        Order order = orderService.getOrderById(orderId);
        // logic to add hypermedia
        return ResponseEntity.ok().body(order);
    }

    @PostMapping("orders/{name}")
    public ResponseEntity<Order> orderProduct(@PathVariable(value = "name") String name) {
        Order order = orderService.orderProduct(name);
        // logic to add hypermedia
        return ResponseEntity.ok().body(order);
    }


}
```