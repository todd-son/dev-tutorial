# JWT

JWT(Json Web Token)란 Json 포맷을 이용하여 사용자에 대한 속성을 저장하는 Claim 기반의 Web Token이다.

## JWT 구조

JWT는 Header, Payload, Signature의 3 부분으로 이루어지며, Json 형태인 각 부분은 Base64Url로 인코딩 되어 표현된다. 또한 각각의 부분을 이어 주기 위해 (.) 구분자를 사용하여 구분한다. 추가로 Base64Url는 암호화된 문자열이 아니고, 같은 문자열에 대해 항상 같은 인코딩 문자열을 반환한다.

## JWT 적용해보기

application.yaml에 다음과 같이 secret를 하나 등록하자.  
secret은 온라인에서 발급받을수 있다.

```yaml
jwt:
  secret: eyJhbGciOiJIUzI1NiJ9.eyJSb2xlIjoiQWRtaW4iLCJJc3N1ZXIiOiJJc3N1ZXIiLCJVc2VybmFtZSI6IkphdmFJblVzZSIsImV4cCI6MTY3Mzc4ODM2OCwiaWF0IjoxNjczNzg4MzY4fQ.WYUUtc2Y7yVveHf_Z9i5_6xaI_QcN_1IMzYhWO5tzOA
```

jwt 라이브러리는 다음을 사용하자.

```groovy
    implementation group: 'io.jsonwebtoken', name: 'jjwt-api', version: '0.11.2'
    implementation group: 'io.jsonwebtoken', name: 'jjwt-impl', version: '0.11.2'
    implementation group: 'io.jsonwebtoken', name: 'jjwt-jackson', version: '0.11.2'
```

### Jwt 발급하기

JwtTokenProvider를 아래와 같이 만들어주자.

```java
@Component
public class JwtTokenProvider {
    private String jwtSecret;
    private Key key;

    public JwtTokenProvider(@Value("${jwt.secret}") String jwtSecret) {
        this.jwtSecret = jwtSecret;
        this.key = Keys.hmacShaKeyFor(jwtSecret.getBytes());
    }

    public String generate(String id) {
        long now = (new Date()).getTime();

        return Jwts.builder()
                .setSubject(id)
                .signWith(key, SignatureAlgorithm.HS256)
                .setExpiration(new Date(now + 60 * 60 * 1000))
                .compact();
    }
}
```

인증 서비스도 만들어보자. 
별도의 인증을 만들어도 되지만 Spring Security의 인증을 사용한다면 아래와 같다.

```java
@Service
public class AuthService {
    private final JwtTokenProvider jwtTokenUtil;
    private final AuthenticationManagerBuilder authenticationManagerBuilder;

    public AuthService(JwtTokenProvider jwtTokenUtil, AuthenticationManagerBuilder authenticationManagerBuilder) {
        this.jwtTokenUtil = jwtTokenUtil;
        this.authenticationManagerBuilder = authenticationManagerBuilder;
    }

    public AuthResponse auth(AuthRequest authRequest) {
        UsernamePasswordAuthenticationToken usernamePasswordAuthenticationToken = new UsernamePasswordAuthenticationToken(authRequest.getEmail(), authRequest.getPassword());
        authenticationManagerBuilder.getObject().authenticate(usernamePasswordAuthenticationToken);
        return new AuthResponse(jwtTokenUtil.generate(authRequest.getEmail()));
    }
}

```

컨트롤러도 만들어보자.

```java
@RestController
@RequestMapping("/api/auth")
public class AuthController {
    private final AuthService authService;

    public AuthController(AuthService authService) {
        this.authService = authService;
    }

    @PostMapping
    public AuthResponse auth(@RequestBody AuthRequest authRequest)  {
        return authService.auth(authRequest);
    }
}
```

테스트를 해보면 Id/Password가 맞을때 정상 발급되고, 틀리면 발급이 되지 않는 것을 알 수 있다.





