# Spring Data JPA

Spring Boot + Spring Data JPA를 사용하면 거의 설정 없이 사용 가능

## DataSource 설정

[https://docs.spring.io/spring-boot/docs/current/reference/html/data.html#data](https://docs.spring.io/spring-boot/docs/current/reference/html/data.html#data)

자바에서는 jdbc라는 스펙이 존재하며, 각 데이터베이스 벤더사에서는 해당 스펙을 구현 해당 데이터소스를 연결에 활용한다.

Spring Boot에서는 spring.datasource prefix로 데이터 소스를 정의를 커스터마이징할 수 있도록 지원한다.

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    username: root
    password: 1234
    url: jdbc:mysql://localhost:3306/board
```

## Connection Pool 설정

데이터베이스 커넥션을 매번 새로 연결하고 하는것은 성능상 불리해서, 커넥션 풀을 활용한다. Spring Boot에서는 기본 설정으로 HikariCP 데이터베이스 커넥션 풀을 제공한다. 
커넥션 풀의 속성은 사실 다수의 사용자가 요청이 있어서 유효할 것이다. 그래도 각 속성의 개념 자체는 알아놓아야 한다.

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/board
    username: root
    password: 1234
    hikari:
      maximum-pool-size: 5
```

## JPA 설정

마찬가지로 각 속성에 대한 의미들은 파악해놓도록 하자.

```yaml
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
```


