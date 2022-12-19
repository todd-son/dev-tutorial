# Paging And Sorting

데이터양이 많아지면 필수적으로 들어가는 기능이 페이징이다.

스프링 Data JPA를 사용하면, Paging과 Sorting을 편리하게 구현할 수 있다.

## Paging

Pageable를 Repository 메소드의 파라미터로 전달하고 Page로 리턴을 받기만 하면 페이징이 수행된다.

```java
@Repository("memberV5Repository")
public interface MemberRepository extends CrudRepository<Member, Integer> {
    Page<Member> findAll(Pageable pageable);
}
```

스프링 Data에서 Paging은 제로베이스다 0페이지 요청을 하면 실제 수행된 쿼리는 다음과 같다.

```
select member0_.id as id1_2_, member0_.email as email2_2_, member0_.name as name3_2_ from member_v5 member0_ limit ?
```

1페이지 이상을 요청을 수행하면 다음과 같다.

```
select member0_.id as id1_2_, member0_.email as email2_2_, member0_.name as name3_2_ from member_v5 member0_ limit ?, ?
```

Pageable과 Page는 각각 인터페이스인데 서비스나 다른 영역에서 사용한다면 각각의 구현체는 PageRequest와 PageImpl을 사용하면 된다.

아래는 컨트롤러에서 PageRequest를 만들어서 서비스로 전달했을때의 예이다.

```java
    @GetMapping
    public FindAllMemberResponse findAll(@RequestParam(value = "page") int page) {
        Pageable pageable = PageRequest.of(page, PAGE_SIZE);

        return FindAllMemberResponse.fromModel(
                memberService.findAll(pageable)
        );
    }
```

그리고 다음은 Page로 받은 엔티티 객체를 PageImpl을 활용해 DTO 객체로 변환된 예이다.

```java
    public static FindAllMemberResponse fromModel(Page<Member> members) {
        List<MemberItem> items = members.getContent().stream().map(MemberItem::fromModel).toList();
        
        Page<MemberItem> page = new PageImpl(
                items,
                members.getPageable(),
                members.getTotalElements()
        );

        return new FindAllMemberResponse(page);
    }
```

## Sorting

Sorting도 해당 정보를 Sort라는 객체에 잘 담아서 전달하기만 하면 스프링 Data Jpa에서 자동으로 지원을 해준다.

사용하는 방법은 크게 두가지인데, 첫번째 방법은 Pageable에 Sort 정보를 포함해서 전달하는 것이다.

아래는 컨트롤러에서 이름으로 정렬해달라는 기능을 추가한 것인다.

```java
    @GetMapping
    public FindAllMemberResponse findAll(@RequestParam(value = "page") int page) {
        Pageable pageable = PageRequest.of(page, PAGE_SIZE, Sort.by(Sort.Order.asc("name")));

        return FindAllMemberResponse.fromModel(
                memberService.findAll(pageable)
        );
    }
```

```
select member0_.id as id1_2_, member0_.email as email2_2_, member0_.name as name3_2_ from member_v5 member0_ order by member0_.name asc limit ?, ?
```

페이징과 마찬가지로 Repository에 Sort를 메소드 인자에 추가하면 자동으로 소팅 기능이 제공된다.

```java
@Repository("orderV4Repository")
public interface OrderRepository extends CrudRepository<Order, Integer> {
    List<Order> findAll(Sort sort);
}
```
