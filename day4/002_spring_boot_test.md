# SpringBootTest

다양한 기능들을 학습하기에 통합테스트를 수행하는 방법에 대해서 알아보자.

스프링의 가장 큰 장점이 IoC라고 할 수 있다. 하지만 컨테이너가 의존성을 관리해주다보니 테스트를 구성하는게 힘들다. 여러 빈들이 복합적으로 디펜던시를 가지고 있기 때문에 그러하다.

## @SpringBootTest

통합테스트를 위한 모든 빈들을 스캔하고, 어플리케이션 컨텍스트를 생성하여 준비해준다. 

아래는 실제 데이터베이스에 접속해서 해당 데이터를 가지고 오고 비교를 해본 테스트 케이스의 예이다.

일반적으로 테스트케이스 given-when-then으로 나눠서 부분을 나눠서 진행하며, 각각의 과정은 다음을 의미한다.

- Given : 테스트를 준비하는 과정. 변수 및 결과 세팅. Mock 정의
- When : 실제로 테스틑 실행하는 부분
- Then : 테스트를 검증하는 과정. 실제 실행으로 나온 값을 검증


```java
@SpringBootTest
class MemberRepositoryIntegrationTest {
    @Autowired
    private MemberRepository memberRepository;

    @Test
    public void findByEmail() {
        // given
        Member expected = new Member(50, "손정욱", "devsun98@gmail.com");

        // when
        Member actual = memberRepository.findByEmail("devsun98@gmail.com").orElseThrow(() -> new IllegalStateException(""));

        // then
        Assertions.assertEquals(expected, actual);
    }
}
```

아래와 같은 오류가 뜬다면 equals 메소드를 구현해주면 된다.

```
expected: <com.example.v5.domain.model.Member@1b2e841> but was: <com.example.v5.domain.model.Member@6f2b608e>
Expected :com.example.v5.domain.model.Member@1b2e841
Actual   :com.example.v5.domain.model.Member@6f2b608e
```

사실 다음 시간에 테스트에 대해서 더 얘기를 해볼텐데 이런식으로 웹앱을 안 띄우고 테스트를 할 수 있다는 것을 알아두고 활용해보자.