# Mockito

## Mock이란?

실제 객체를 만들어 사용하기에 시간, 비용 등의 Cost가 높거나 혹은 객체 서로간의 의존성이 강해 구현하기 힘들 경우 가짜 객체를 만들어 사용하는 방법이다.

### Mock 객체는 언제 필요한가?
1. 테스트 작성을 위한 환경 구축이 어려운 경우
2. 테스트가 특정 경우나 순간에 의존적인 경우
3. 테스트 시간이 오래 걸리는 경우
4. 개인 PC의 성능이나 서버의 성능문제로 오래 걸릴수 있는 경우 시간을 단축하기 위해 사용한다. 

## Stub

Mockito의 가장 큰 기능을 Stub을 쉽게 만들 수 있다는 점이다.

### 테스트 스텁(Test Stub)

더미 객체 보다 좀더 구현된 객체로 더미 객체가 마치 실제로 동작하는 것처럼 보이게 만들어 놓은 객체이다.  
객체의 특정 상태를 가정해서 만들어 특정 값을 리턴해 주거나 특정 메시지를 출력해 주는 작업을 한다.  
특정 상태를 가정해서 하드코딩된 형태이기 때문에 로직에 따른 값의 변경은 테스트 할 수 없다.  
즉, 어떤 행위가 호출됐을 때 특정 값으로 리턴시켜주는 형태가 Stub이다.  

```java
import static org.mockito.Mockito.*;

// you can mock concrete classes, not only interfaces
LinkedList mockedList = mock(LinkedList.class);
// or even simpler with Mockito 4.10.0+
// LinkedList mockedList = mock();

// stubbing appears before the actual execution
when(mockedList.get(0)).thenReturn("first");

// the following prints "first"
System.out.println(mockedList.get(0));

// the following prints "null" because get(999) was not stubbed
System.out.println(mockedList.get(999));
```

## Verify

Mockito의 두번쨰 가장 큰 기능은 해당 기능을 수행했는지를 확인할 수 있다.

```java
import static org.mockito.Mockito.*;

// mock creation
List mockedList = mock(List.class);
// or even simpler with Mockito 4.10.0+
// List mockedList = mock();

// using mock object - it does not throw any "unexpected interaction" exception
mockedList.add("one");
mockedList.clear();

// selective, explicit, highly readable verification
verify(mockedList).add("one");
verify(mockedList).clear();
```