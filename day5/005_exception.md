# 서비스 구현

## 14세 미만 예외 케이스 구현

어디에서 구현을 해야할까?   
Member 엔티티에 구현을 해야 한다. 객체지향의 기본은 본인이 할 수 있는 일은 본인이 해야된다.

멤버에 기능을 추가해야 하니까 먼저 멤버테스트를 추가하자.

```java
@DisplayName("멤버")
class MemberTest {
    @Test
    @DisplayName("14세 미만은 멤버가 될 수 없다.")
    public void should_throwAgeUnder14Exception_when_AgeUnder14() {
        // given
        LocalDate birthDate = LocalDate.now().minusYears(12);

        // when & then
        assertThrows(AgeUnder14Exception.class, () -> Member.of(
                "todd",
                "todd@kakao.com",
                birthDate
        ));
    }
}
```

테스트를 수행해보면 당연히 실패한다.

팩토리 메서드에 해당 기능을 구현해보자. 

```java
public class Member {
    // 중략

    public static Member of(String name, String email, LocalDate birthDate) {
        if (birthDate.isAfter(LocalDate.now().minusYears(14)))
            throw new AgeUnder14Exception();

        return new Member(null, name, email, birthDate);
    }
    
    // 중략
}
```

그리고 테스트를 돌려보면 성공하게 된다. 

## 이메일 중복 예외 케이스 구현

해당 기능은 어느 영역에 있어야 할까 객체간의 행위이므로 서비스에 작성하는게 맞다.

```java
@Service
public class MemberCreateService {
    private final MemberRepository memberRepository;

    public MemberCreateService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }

    public MemberCreateResponse create(MemberCreateRequest request) {
        Member member = request.toModel();

        if (memberRepository.findByEmail(member.getEmail()).isPresent())
            throw new EmailDuplicatedException();

        return MemberCreateResponse.fromModel(
                memberRepository.save(member)
        );
    }
}
```

테스트를 수행해보면 실패를 한다. 기존 MockRepository가 제대로 동작을 못했기 때문이다.  
해당 동작을 테스트케이스에 추가 정의를 해주자.

```java
    @Test
    @DisplayName("이메일이 중복된 회원은 등록될 수 없다")
    public void should_throwEmailDuplicatedException_when_duplicatedEmail() {
        // given
        String name = "todd";
        LocalDate birthDate = LocalDate.now().minusYears(15);
        String email = "duplicatin@kakao.com";

        Mockito.when(memberRepository.findByEmail(email))
                .thenReturn(
                        Optional.of(
                                new Member(1, "sujin", email, LocalDate.of(2002, 11, 11))
                        )
                );

        MemberCreateRequest request = new MemberCreateRequest(
                name, email, birthDate
        );

        // when & then
        assertThrows(EmailDuplicatedException.class, () -> memberCreateService.create(request));
    }
```