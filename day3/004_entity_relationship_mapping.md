# Entity RelationShip Mapping

## 따라하기

지금까지는 단순하게 하나의 객체와 하나의 테이블만을 매핑하였다. 객체는 여러 다른 객체와 연관 관계를 갖는다. 다른 객체와 연관 관계를 가지고 있는 객체를 여러 테이블에 매핑하는 방법을 알아보자.

기존 주문 클래스를 보면 한 주문당 하나의 메뉴밖에 주문할 수 없었다. 이것을 하나의 주문번호에 다양한 주문을 포함할 수 있도록 개선해보자.

단순하다. OneToMany라는 어노테이션을 달고 생명주기를 cascade로 지정하고, joinColumn으로 조인될 칼럼을 명시하면 된다.

```java
@Entity(name = "orderV3")
@Table(name = "order_v3")
public class Order {
    @Id
    @GeneratedValue
    @Column(name = "order_id")
    private Integer orderId;

    @OneToMany(cascade = CascadeType.ALL)
    @JoinColumn(name = "order_id")
    private List<OrderItem> items;

    private OrderStatus orderStatus;

    private LocalDateTime createdAt;
    
    // 중략
}
```

```java
@Entity
@Table(name = "order_item_v3")
public class OrderItem implements Serializable {
    @Id
    @GeneratedValue
    private int orderItemId;
    
    @Column(name = "menu_id")
    private int menuId;

    private int orderQuantity;
    
    // 중략
}
```

스프링 데이터 리파지토리에서 save를 호출하면 아래와 같이 한번에 데이터가 저장되는 것을 알수 있다. 

```
Hibernate: insert into order_v3 (created_at, order_status, order_id) values (?, ?, ?)
Hibernate: insert into order_item_v3 (menu_id, order_quantity, order_item_id) values (?, ?, ?)
Hibernate: insert into order_item_v3 (menu_id, order_quantity, order_item_id) values (?, ?, ?)
Hibernate: update order_item_v3 set order_id=? where order_item_id=?
Hibernate: update order_item_v3 set order_id=? where order_item_id=?
```

## Table Module vs Domain Model

마틴 파울러는 테이블을 각각 객체로 형상화 해서 Data Access 모듈을 개발하는 방식을 Table Module, 그리고 객체를 표현하고 그것을 테이블로 매핑하는 방식을 Domain Model 방식이라고 했다.

Dao를 사용하고 테이블과 객체를 1 대 1로 가져가는 방식을 Table Module 방식.

Orm을 사용하고 객체와 테이블의 관계를 매핑하는 것은 Domain Model 방식이라고 한다.

Domain Model 방식의 개발을 하면 비즈니스 로직을 객체에 한정시킬 수 있다. 이것이 사실 우리가 JPA, ORM을 공부하는 이유이다. 

## 연관 종류

1-1, 1-N, N-1, M-N, M-1/1-N의 종류가 있다.

사실 JPA에서는 모든 연관 관계를 지원한다. 그러나 가장 중요한 것은 언제 해당 연관을 사용하는 것인가 이다.

특히 1-N과 N-1은 헷갈릴 수 있다.

위와 같이 주문이 생성될 떄 여러 주문 내역이 같이 생성된다면, 즉 라이프 사이클을 같이 한다면 1-N을 고려해보자, 주문내역이 없는 주문은 성립할 수 없다.

```java
public class Order {
    private List<OrderItem> items;

    public static Order of(List<CustomerOrder> customerOrders) {
        if (customerOrders.isEmpty())
            throw new IllegalStateException("Order Items shouldn't be empty");

        List<OrderItem> orderItems = customerOrders.stream().map(o -> new OrderItem(o.getMenuId(), o.getOrderQuantity())).toList();
        return new Order(orderItems, OrderStatus.REQUESTED, LocalDateTime.now());
    }
}
```

유저 정보를 먼저 생성하고, 가족사항을 하나씩 추가한다면 즉 라이프사이클이 다르다면 N-1을 고려해보자.

눈치 챘겠지만, 사실 테이블로 표현하면 1-N이나, N-1이나 같다.

```java
public class FamilyMember {
    private User user;
}
```

1-1 관계는 1-N 에서 복수가 아닌 단수와 관계를 맺을때 사용한다.

M-N의 관계는 거의 사용하지 않는다. M-N 관계는 상호참조가 생기고, 서로의 라이프사이클이 꼬인다. 이럴 경우에는 M-1/1-N 관계의 중간 객체를 만들어서 풀어보자.

```java
public class CourseSubscription {
    private Course course;
    private Member member;
}
```

## 라이프 사이클과 서비스 구현

1-N의 경우 리파지토리는 하나만 필요하며, 생성을 한다면 다음과 같이 한번에 생성한다.

```java
@Service("orderV3Service")
public class OrderService {
    private final OrderRepository orderRepository;

    public OrderService(OrderRepository orderRepository) {
        this.orderRepository = orderRepository;
    }

    public Order createOrder(List<CustomerOrder> customerOrders)  {
        return orderRepository.save(Order.of(customerOrders));
    }
}
```

N-1의 경우 연관 객체가 이미 생성되어 있으므로, 각각의 리파지토리가 필요하며, 생성을 한다면 다음과 같은 형식으로 나타난다.

```java
@Service
public class FamilyMemberService {
    private final UserRepository userRepository;
    private final FamilyMemberRepository familyMemberRepository;

    public FamilyMemberService(UserRepository userRepository, FamilyMemberRepository familyMemberRepository) {
        this.userRepository = userRepository;
        this.familyMemberRepository = familyMemberRepository;
    }

    public FamilyMember registerMember(Integer userId, String familyName) {
        User user = userRepository.findById(userId).orElseThrow(() -> new IllegalArgumentException("Couldn't find user."));

        return familyMemberRepository.save(
                new FamilyMember(user, familyName)
        );

    }
}
```