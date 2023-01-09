# 서비스 구현

## 기본 시나리오 구현

기장 기본이 되는 시나리오를 구현해보자.

```java
@Service
public class MemberCreateService {
    private final MemberRepository memberRepository;

    public MemberCreateService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }

    public MemberCreateResponse create(MemberCreateRequest request) {
        Member member = request.toModel();
        
        return MemberCreateResponse.fromModel(
                memberRepository.save(member)
        );
    }
}
```

서비스에 Repository 디펜던시가 발생했다 테스트를 mockito 기능을 이용해서 아래와 같이 수정해보자.

```java
@DisplayName("회원 등록")
class MemberCreateServiceTest {
    private MemberRepository memberRepository = Mockito.mock(MemberRepository.class);
    private MemberCreateService memberCreateService = new MemberCreateService(memberRepository);
}
```

테스트를 돌려보자. 그래도 실패한다.

## Mock이란?

실제 객체를 만들어 사용하기에 시간, 비용 등의 Cost가 높거나 혹은 객체 서로간의 의존성이 강해 구현하기 힘들 경우 가짜 객체를 만들어 사용하는 방법이다.

위의 코드에 Mockito를 이용해 특정 행위을 정의해보자.

```java
    @Test
    @DisplayName("회원은 등록될 수 있다")
    public void should_created_when_correct() {
        // given
        String name = "todd";
        LocalDate birthDate = LocalDate.of(1990, 11, 11);
        String email = "todd@kakao.com";

        Mockito.when(memberRepository.save(Member.of(name, email, birthDate))).thenReturn(new Member(2, name, email, birthDate));

        MemberCreateRequest request = new MemberCreateRequest(
                name, email, birthDate
        );

        MemberCreateResponse expected = new MemberCreateResponse(
                2, name
        );

        // when
        MemberCreateResponse actual = memberCreateService.create(request);

        // then
        assertEquals(expected, actual);
    }
```

다시 테스트를 수행하면 기본 시나리오의 테스트가 성공한다.



