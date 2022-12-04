# MVC

## MVC 패턴이란?

MVC패턴은 Model-View-Controller의 약자로서 개발을 할 때 3가지 형태로 역학을 나누어 개발하는 방법론입니다.

Model은 비즈니스 로직의 핵심을 처리하는 부분

View는 사용자에게 시각적으로 보여주는 부분(UI), API에서는 json 응답

Controller는 View와 Model을 연결하는 역할. View의 요청을 모델에 전달. 모델의 응답을 View에 전달 


## Controller, Service, Repository

컨트롤러 : @Controller (프레젠테이션 레이어, 웹 요청과 응답을 처리함)  
로직 처리 : @Service (서비스 레이어, 내부에서 자바 로직을 처리함)  
외부I/O 처리 : @Repository (퍼시스턴스 레이어, DB나 파일같은 외부 I/O 작업을 처리함)

## Service

그럼 처음으로 서비스를 만들어보자.

```java
public interface OrderRepository {
    Order save(Order order);
}

```

```java
public class OrderService {
    private MemberRepository memberRepository;
    private OrderRepository orderRepository;

    public OrderService(MemberRepository memberRepository, OrderRepository orderRepository) {
        this.memberRepository = memberRepository;
        this.orderRepository = orderRepository;
    }

    public Order order(CreateOrderDto createOrderDto) {
        Long memberId = createOrderDto.getMemberId();
        Long coffeeId = createOrderDto.getCoffeeId();
        int numberOfOrders = createOrderDto.getNumberOfOrders();

        Member member = memberRepository.findById(memberId).orElseThrow(() -> new IllegalArgumentException("Couldn't find member. Member ID is %s".formatted(memberId)));

        Order order = Order.of(member, coffeeId, numberOfOrders);
        
        orderRepository.save(order);

        return order;
    }
}
```

그리고 빈으로 등록해보자.

```java
package com.example.v2.config;

@Configuration
public class OrderConfig {
    @Bean
    public OrderService orderService(MemberRepository memberRepository, OrderRepository orderRepository) {
        return new OrderService(memberRepository, orderRepository);
    }
}
```

```
Parameter 1 of method orderService in com.example.v2.config.OrderConfig required a bean of type 'com.example.v2.model.domain.OrderRepository' that could not be found.
```

OrderRepository로 구현체를 등록하여 해결해보자. OrderRepository 인터페이스를 구현해서 그냥 Order를 저장하지 말고 적당한 출력만 해보자. 

성공했다면 Controller도 구현해보자.

