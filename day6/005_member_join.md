# Member Join

## 회원 가입 기능 개발

Spring Security의 인증을 활용하기 위해서 유저 정보를 저장한 후 추가로 userDetailsManager에도 추가를 해준다.

```java
@Service
public class MemberCreateService {
private final MemberRepository memberRepository;
private final UserDetailsManager userDetailsManager;
private final PasswordEncoder passwordEncoder;

    public MemberCreateService(MemberRepository memberRepository, UserDetailsManager userDetailsManager, PasswordEncoder passwordEncoder) {
        this.memberRepository = memberRepository;
        this.userDetailsManager = userDetailsManager;
        this.passwordEncoder = passwordEncoder;
    }

    @Transactional
    public MemberResponse create(MemberRequest request) {
        if (request.getEmail() == null)
            throw new EmailShouldBeNotNullException();

        memberRepository.save(request.toModel());

        UserDetails userDetails = User.builder().username(request.getEmail())
                .password(passwordEncoder.encode(request.getPassword()))
                .roles("USER")
                .build();

        userDetailsManager.createUser(userDetails);

        return new MemberResponse(request.getEmail(), request.getPassword());
    }
}
```

```java
@RestController
@RequestMapping("/api")
public class MemberController {
    private final MemberCreateService memberCreateService;

    public MemberController(MemberCreateService memberCreateService) {
        this.memberCreateService = memberCreateService;
    }

    @PostMapping("/member")
    public MemberResponse create(@RequestBody MemberRequest request) {
        return memberCreateService.create(request);
    }
}
```