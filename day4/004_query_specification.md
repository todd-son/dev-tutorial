# Query & Specification

## @Query

스프링 Data JPA에서는 @Query 메소드를 이용해서 JPQL 및 SQL의 직접 사용을 지원하기도 한다.

```java
@Repository("orderV4Repository")
public interface OrderRepository extends CrudRepository<Order, Integer> {
    @Query("select o from Order o where o.orderStatus = :orderStatus")
    List<Order> findByOrderStatus(@Param("orderStatus") OrderStatus orderStatus);
}
```

nativeQuery 옵션을 통해 지원하기도 하지만 native query를 쓸바엔 JPA를 쓰지 않는게 나을수도 있다.

## Specification

간단히 스펙이라고 부르기도 하는데 스펙을 통해서 동적인 조건을 지정할 수 있다.

스펙을 조건으로 넣기 위해서는 specification을 메서드의 인자로 지정하면 된다.

```java
@Repository
public interface MemberRepository extends CrudRepository<Member, Integer>, JpaSpecificationExecutor<Member> {
    List<Member> findAll(Specification<Member> spec);
}
```

간단하게 스펙을 만들어보자. 

스펙의 인터페이스는 다음과 같다.

```java
public interface Specification<T> {
  Predicate toPredicate(Root<T> root, CriteriaQuery<?> query,
            CriteriaBuilder builder);
}
```

메소드가 하나이므로 람다로 깔끔하게 생성할 수 있다. root는 엔터티의 루트를 지칭하며, criteriaBuilder을 통해 조건을 생성할 수 있다.
다음은 이름으로 like 조건을 가진 스펙을 생성한 예이다.

```java
public class MemberSpec {
    public static Specification<Member> nameLike(String name) {
        return (root, query, criteriaBuilder) ->
            criteriaBuilder.like(root.get("name"), "%" + name + "%");
    }
}
```

리파지토리 호출은 다음과 같이 될 것 이다.

```java
List<Member> members = memberRepository.findAll(
        MemberSpec.nameLike("정욱")
);
```

사실 스펙의 최대 강점은 각각의 specification이 and와 or로 조합될 수 있다는 것이다.

기존 페이징 기능에 검색 기능을 추가한다고 가정해보자.

스펙은 pageable과 같이 사용될 수 있다.

```java
@Repository
public interface MemberRepository extends CrudRepository<Member, Integer>, JpaSpecificationExecutor<Member> {
    Page<Member> findAll(Specification<Member> spec, Pageable pageable);
}
```

서비스에서 검색 조건으로 값이 이름이 전달되었을 때, 선택적으로 조건을 넣고 싶다면 다음과 같이 작성할 수 있다.

```java
public Page<Member> findAll(Pageable pageable, String name) {
    Specification<Member> spec = Specification.where(null);

    if (name != null) {
        spec = spec.and(MemberSpec.nameLike(name));
    }

    return memberRepository.findAll(spec, pageable);
}
```

