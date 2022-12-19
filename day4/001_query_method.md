# Query Methods

## Spring Data JPA

사실 스프링 데이터 JPA는 2011년에 첫 릴리이즈가 되었음. 스프링이 2002년 릴리이즈니까 스프링 데이터 JPA 없이도 JPA는 스프링과 함께 사용되었음.

시작하기전에 JPA는 결국 Hibernate에 모든 구현이 있고, 그것을 PSA로 제공하는 것 뿐이니 다양한 편의 기능을 익힌다고 생각하고 마음을 편하게 먹자

## Query Methods

쿼리 메소드는 스프링 데이터 JPA에서 제공하는 기능 중 가장 기본이자 핵심이다.

Spring Data Jpa에서는 메소드 이름과 리턴 타입으로 쿼리를 자동으로 생성하고 리턴 타입에 맞게 결과를 리턴해준다.

만약 이메일로 유저를 가지고 온다면 다음과 같다. 아래와 같이 Repository 인터페이스에 메소드를 추가하면 쿼리를 자동으로 생성해서 해당 기능을 제공해준다.

```java

@Repository("memberV5Repository")
public interface MemberRepository extends CrudRepository<Member, Integer> {
    Optional<Member> findByEmail(String email);
}
```

만약 위의 이름으로 검색을 유저를 Like 검색을 한다면 다음과 같다.

```java

@Repository("memberV5Repository")
public interface MemberRepository extends CrudRepository<Member, Integer> {
    List<Member> findByNameLike(String name);
}
```

아래와 같이 다양한 키워드들을 이용해 쿼리를 조합할 수 있다.

[링크](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods)


|Keyword           |Sample                                                 | JPQL snippet                                                  |
|------------------|-------------------------------------------------------|---------------------------------------------------------------|
|Distinct          |findDistinctByLastnameAndFirstname                     | select distinct … where x.lastname = ?1 and x.firstname = ?2  |
|And               |findByLastnameAndFirstname                             | … where x.lastname = ?1 and x.firstname = ?2                  |
|Or                |findByLastnameOrFirstname                              | … where x.lastname = ?1 or x.firstname = ?2                   |
|Is,Equals        |findByFirstname,findByFirstnameIs,findByFirstnameEquals| … where x.firstname = ?1                                      |
|Between           |findByStartDateBetween                                 | … where x.startDate between ?1 and ?2                         |
|LessThan          |findByAgeLessThan                                      | … where x.age < ?1                                            |
|LessThanEqual     |findByAgeLessThanEqual                                 | … where x.age <= ?1                                           |
|GreaterThan       |findByAgeGreaterThan                                   | … where x.age > ?1                                            |
|GreaterThanEqual  |findByAgeGreaterThanEqual                              | … where x.age >= ?1                                           |
|After             |findByStartDateAfter                                   | … where x.startDate > ?1                                      |
|Before            |findByStartDateBefore                                  | … where x.startDate < ?1                                      |
|IsNull,Null      |findByAge(Is)Null                                      | … where x.age is null                                         |
|IsNotNull,NotNull|findByAge(Is)NotNull                                   | … where x.age not null                                        |
|Like              |findByFirstnameLike                                    | … where x.firstname like ?1                                   |
|NotLike           |findByFirstnameNotLike                                 | … where x.firstname not like ?1                               |
|StartingWith      |findByFirstnameStartingWith                            | … where x.firstname like ?1 (parameter bound with appended%)  |
|EndingWith        |findByFirstnameEndingWith                              | … where x.firstname like ?1 (parameter bound with prepended%) |
|Containing        |findByFirstnameContaining                              | … where x.firstname like ?1 (parameter bound wrapped in%)     |
|OrderBy           |findByAgeOrderByLastnameDesc                           | … where x.age = ?1 order by x.lastname desc                   |
|Not               |findByLastnameNot                                      | … where x.lastname <> ?1                                      |
|In                |findByAgeIn(Collection<Age> ages)                      | … where x.age in ?1                                           |
|NotIn             |findByAgeNotIn(Collection<Age> ages)                   | … where x.age not in ?1                                       |
|TRUE              |findByActiveTrue()                                     | … where x.active = true                                       |
|FALSE             |findByActiveFalse()                                    | … where x.active = false                                      |
|IgnoreCase        |findByFirstnameIgnoreCase                              | … where UPPER(x.firstname) = UPPER(?1)                        |
