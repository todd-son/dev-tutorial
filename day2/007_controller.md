# Controller

Controller는 View와 Model을 연결하는 역할을 담당한다.

## 일반적인 형식

사실 정답은 없는 얘기긴 하지만 일반적인 형태는 아래와 같고 각 요소에 대해서 알아보자.

```java
@RestController
@RequestMapping("/api")
public class OrderController {
    private OrderService orderService;

    public OrderController(OrderService orderService) {
        this.orderService = orderService;
    }

    @PostMapping("/orders")
    public CreateOrderResponse createOrder(@RequestBody CreateOrderRequest createOrderRequest)  {
        return CreateOrderResponse.fromModel(
                orderService.order(createOrderRequest.toModel())
        );
    }
}
```

## DTO(Data Transfer Object)

데이터를 전달하는 용도로 사용한다. 아래 메소드에서는 두 개의 DTO가 사용되며 CreateOrderRequest, CreateOrderResponse 이다.
DTO를 사용하는 이유는 OrderService가 재활용될 수 있다고 생각하기 때문이다. 왜냐하면 비즈니스 로직은 일관성을 지키기 위해서 재활용이 된다.

예를 들어 키오스크에서도 주문을 생성할 수 있고, 전화로도 생성할 수 있고, 웹페이지에서도 생성할 수 있다고 생각하면 각각의 입력과 출력은 다를 것이다. 
이럴 경우 다수의 컨트롤러 객체는 생기지만 우리는 주문 생성이라는 비즈니스 로직을 재활용하기 위해서 각각의 인터페이스를 위해 추가작성하고, 해당 인터페이스용 DTO를 모델 객체로 변경하고 모델 객체를 DTO로 변환해야 한다.

그리고 특정 인터페이스의 메시지를 구분하기 위해 DTO의 PostFix를 다양하게 구성하기도 한다. 일단 나는 API 요청은 XXXRequest, 응답은 XXXResponse의 형식으로 작성해보았다. 

그리고 중요한 점은 DTO는 Model을 알아서 Model로 변경될 수 있고, Model로 부터 생성될 수 있지만, Model은 DTO을 알면 안된다.


```java

public class CreateOrderRequest {
    private Long memberId;
    private Long coffeeId;
    private int numberOfOrders;

    public CreateOrderRequest(Long memberId, Long coffeeId, int numberOfOrders) {
        this.memberId = memberId;
        this.coffeeId = coffeeId;
        this.numberOfOrders = numberOfOrders;
    }

    public Long getMemberId() {
        return memberId;
    }

    public Long getCoffeeId() {
        return coffeeId;
    }

    public int getNumberOfOrders() {
        return numberOfOrders;
    }

    public CustomerOrder toModel() {
        return new CustomerOrder(
                memberId,
                coffeeId,
                numberOfOrders
        );
    }
}
```

```java
public class CreateOrderResponse {
    private String coffeeName;
    private int numberOfOrders;

    public CreateOrderResponse() {}

    public CreateOrderResponse(String coffeeName, int numberOfOrders) {
        this.coffeeName = coffeeName;
        this.numberOfOrders = numberOfOrders;
    }

    public static CreateOrderResponse fromModel(Order order) {
        return new CreateOrderResponse(
                order.getCoffeeName(),
                order.getNumberOfOrders()
        C) ;
    }

    public String getCoffeeName() {
        return coffeeName;
    }

    public int getNumberOfOrders() {
        return numberOfOrders;
    }
}
```

### DTO를 썼을 떄 좋은 예

만약에 HTTP가 아닌 요청(예 카프카)가 들어오더라도 Controller를 재작성함으로써 비즈니스 로직을 재활용할 수 있다.

이벤트 큐라서 리턴이 없어서 response가 없다.

```java
@EventConsumer
public class OrderConsumer {
    private OrderService orderService;

    public OrderConsumer(OrderService orderService) {
        this.orderService = orderService;
    }

    @EventMapping(EventType.CREATE_ORDER)
    public void createOrder(@RequestBody CreateOrderMessage createOrderMessage)  {
        return orderService.order(createOrderRequest.toModel());
    }
}
```