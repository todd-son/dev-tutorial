# Embeddable 

어쩌면 1-N도 거의 보기 힘들다.

이유를 생각해보면 아까 주문의 경우는 OrderItem 굳이 Entity일 필요가 없다. 그냥 단순한 값이고, 라이프사이클을 Order와 같이 한다면 VO면 충분하다.

VO을 이용한다면 Embeddable을 활용할 수 있다.

N-1에서는 Embeddable을 활용할 수 있을까? 없다. 그 이유는 참조 객체가 별도의 라이프사이클을 가지고, 리파지토리를 이용해 persistence 되기 떄문이다.

## ElementCollection

OrderItem은 VO가 되므로, 더이상 ID 값이 필요 없다.

```java
@Embeddable
public class OrderItem implements Serializable {
    @Column(name = "menu_id")
    private int menuId;

    private int orderQuantity;
    
    // 중략
}
```

Order는 엔티티티 연관관계가 아닌 ElementCollection이라는 어노테이션을 활용해 참조한다.
CollectionTable 어노테이션을 활용해 매핑 정보를 넣어준다.

```java
@Entity(name = "orderV4")
@Table(name = "order_v4")
public class Order {
    @Id
    @GeneratedValue
    @Column(name = "order_id")
    private Integer orderId;

    @ElementCollection
    @CollectionTable(
            name = "order_item_v4",
            joinColumns = @JoinColumn(name = "order_id")
    )
    private List<OrderItem> items;
}
```

객체 생성을 해서 save를 하면 아래와 같은 쿼리가 날아간다.

```
Hibernate: insert into order_v4 (created_at, order_status, order_id) values (?, ?, ?)
Hibernate: insert into order_item_v4 (order_id, menu_id, order_quantity) values (?, ?, ?)
Hibernate: insert into order_item_v4 (order_id, menu_id, order_quantity) values (?, ?, ?)
```

## VO와 1 - 1 관계의 표현

단순히 Embedded라는 어노테이션만 붙이면 같은 테이블에 다른 칼럼으로 매핑된다.

```java
@Entity(name = "orderV4")
@Table(name = "order_v4")
public class Order {
    @Id
    @GeneratedValue
    @Column(name = "order_id")
    private Integer orderId;

    @Embedded
    private OrderItem orderItem;
}
```

## 결론

사실 JPA에는 수많은 매핑 방식이 있다. M-N을 매핑할 수도 있고, 1-N 양방향 매핑도 가능하다.

예를 들어 VO와 1-1 연관관계를 가지고 다른 테이블에 저장하고 싶다면, SecondaryTable 어노테이션을 이용해야 한다. 이러한 모든 매핑은 사실 외우는 것이 아니라 사용할떄 마다 검색해서 참조하는 것이다.

```java
@SecondaryTables({
        @SecondaryTable(name = "order_item",
                pkJoinColumns = @PrimaryKeyJoinColumn(name = "order_id", referencedColumnName = "order_id")
        )
})
public class Order {
    
}
```

하지만 어쩌면 가장 중요한 것은 Entity와 VO를 구분하고, 각 객체간의 관계를 일관성 있게 정의하는 것이다.

그리고 JPA의 다양한 테크닉을 이용해서 데이터베이스에 객체를 저장하는 것이다. 이때도 일관된 방식을 사용하자.  


