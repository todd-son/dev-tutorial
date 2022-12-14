# Mapping

Mapping은 사실 JPA의 가장 기본적이면서 핵심적인 기능이다. 

## 객체와 테이블 매핑

Entity 어노테이션은 해당 객체가 엔티티임을 명시한다.

Table 어노테이션은 해당 객체가 저장될 테이블의 매핑을 지정한다. 테이블명, 카탈로그명, 스키마명등을 명시할 수 있다. 카탈로그와 스키마등의 개념은 각 데이터베이스마다 다소 차이가 있다. 테이블을 묶어서 관리하는 단위이다.

```java
@Entity
@Table(name = "order")
public class Order {
    
}
```

### 도대체 Entity가 무엇인가?
엔티티 VS VO

Entity와 Value Object는 식별성에 의해 구분된다. 

- 엔티티의 개념적 식별성이 있고, VO는 개념적 식별성이 없다.
- 엔티티는 속성이 변경될 수 있고, VO는 속성이 변경되지 않는 불변객체이다.

## 멤버변수 매핑

Id 어노테이션으로 PK 칼럼 매핑을 한다. GenerateValue 어노테이션으로 자동생성을 해줄수 있다.

Column 어노테이션으로 멤버변수와 칼럼을 매핑한다.

가장 기본적인 속성은 name으로 칼럼명을 명시하고, nullable이나 length등의 속성으로 제약조건을 명시할 수 있다.

Enumerated 어노테이션으로 Enum 타입의 멤버변수를 칼럼으로 매핑할 수 있다. EnumType.STRING 으로 name값을 매핑할 수 힜고, EnumType.ORDINAL로 enum 순서를 매핑할 수 있지만, 왠만하면 이름을 저장하는게 유지보수에 좋다. 


```java
@Entity
@Table(name = "cafe_order")
public class Order {
    @Id
    @GeneratedValue
    @Column(name = "order_id")
    private Integer orderId;

    @Column(name = "menu_id", nullable = false)
    private int menuId;

    @Column(name = "order_quantity", nullable = false, length = 4)
    private int orderQuantity;

    @Enumerated(EnumType.STRING)
    @Column(name = "order_status")
    private OrderStatus orderStatus;

    @Column(name = "created_at")
    private LocalDateTime createdAt;
```



