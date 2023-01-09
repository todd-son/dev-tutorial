# Transactional

## 트랜잭션 이란?

- 데이터베이스의 상태를 변경시키는 하나의 논리적 기능을 수행하는 작업의 단위

### ACID

- 원자성 (Atomicity) : 트랜잭션이 데이터베이스에 모두 반영되던가, 아니면 전혀 반영되지 않아야 한다는 것
- 일관성 (Consistency) : 트랜잭션의 작업 처리 결과가 항상 일관성이 있어야 한다는 것
- 독립성 (Isolation) : 어떤 하나의 트랜잭션이라도, 다른 트랜잭션의 연산에 끼어들 수 없다는 점을 가리킨다.
- 지속성 (Durability) : 트랜잭션이 성공적으로 완료됬을 경우, 결과는 영구적으로 반영되어야 한다는 점

### Propagation

- REQUIRED : default propagation, 트랜잭션이 없으면 새로 생성하고, 이미 시작된 트랜잭션이 있으면 해당 트랜잭션을 사용함.
- REQUIRES_NEW : 항상 새로운 트랜잭션을 시작. 즉, 이전에 시작되어 실행중인 트랜잭션에 상관없이 항상 새로운 트랜잭션을 만들어 시작시킴.
- NOT_SUPPORTED : 현재 진행중인 트랜잭션이 있어도 사용x, 무시함. 트랜잭션이 없으면 그냥 진행, 새로 생성x. (여러 메서드 동작 시 특정 메서드만 트랜잭션 적용을 제외시키기 위함)

### Isolation

- READ_UNCOMMITED : 해당 트랜잭션에서 커밋되지 않은 데이터를 다른 트랜잭션이 접근할 수 있도록 허용함
- READ_COMMITED : 해당 트랜잭션으로부터 커밋된 확정 데이터만 다른 트랜잭션에서 접근 허용 (외부 트랜잭션에서 해당 데이터를 삭제, 수정할 경우 Non-Repeatable-Read를 막을 수 없음)
- REPEATABLE_READ : 한 트랜잭션 내에서 실행되는 모든 같은 SELECT문의 결과가 같도록 조회할 수 있는 격리수준 (선행 트랜잭션에서 SELECT문에 사용된 데이터들에 Shared Lock을 걸어 후행 트랜잭션이 해당 데이터의 수정,삭제 연산을 하지 못하게 막는 격리수준, 후행 트랜잭션에서 선행 트랜잭션의 SELECT 조건에 일치하는 데이터를 추가할 시 유령 데이터가 생겨 Phantom Read가 발생할 수 있음)
- SERIALIZABLE : 가장 엄격한 격리수준, 트랜잭션의 동시성이 떨어져 성능에 치명적일 수 있음 (Reapeatable_Read와 다르게 Insert도 막는 기법이다. 즉, Phantom Read를 막을 수 있음

## Transactional 

습관적으로 Service에는 Transactional 어노테이션이 붙는 것을 알 수 있다.  
스프링은 @Transactional 을 사용한 클래스에 프록시를 객체를 생성하고, 프록시는 트랜잭션 로직을 메소드 앞뒤에 넣어준다. 즉 하나의 서비스가 트랜잭션의 단위가 된다.

```
프록시 객체는 @Transactional이 포함된 메소드가 호출 될 경우, PlatformTransactionManager를 사용하여 트랜잭션을 시작하고, 정상 여부에 따라 Commit 또는 Rollback 한다.
```

default는 UnCheckedException과 Error에 대해서 롤백 정책을 설정된다.


### 특정 오류에 대한 롤백 및 롤백 제외

```
[특정 오류에 대한 롤백]
@Transactional(rollbackFor = Exception.class)

[복수의 롤백]
@Transactional(rollbackFro = {RuntimeException.class, Exception.class})

[롤백 제외]
@Transactional(noRollbackFor={IgnoreRollbackException.
```
