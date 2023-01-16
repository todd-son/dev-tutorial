# Security Getting Started

## SecurityConfig

시큐리티 필터 체인을 활성화 하고 인증 저장소를 Jdbc를 사용함으로 아래와 같이 설정을 해보자.

```java
@Configuration
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfig {
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
                .formLogin();

        // post 요청을 위함
        http
                .cors()
                .and()
                .csrf()
                .disable();

        http
                .logout();

        return http.build();
    }

    @Bean
    public PasswordEncoder getPasswordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Bean
    public UserDetailsManager users(DataSource dataSource) {
        JdbcUserDetailsManager users = new JdbcUserDetailsManager(dataSource);
        return users;
    }
}
```

로그인을 위해 필요한 유저 정보를 저장할 테이블도 아래와 같이 만들어준다.

```sql
create table users(
                      username varchar(50) not null primary key,
                      password varchar(500) not null,
                      enabled boolean not null
);

create table authorities (
                             username varchar(50) not null,
                             authority varchar(50) not null,
                             constraint fk_authorities_users foreign key(username) references users(username)
);

create unique index ix_auth_username on authorities (username,authority);
```

테스트를 위해 아래와 같이 컨트롤러에 PreAuthorize 어노테이션으로 인가를 걸어보자.  
GET 요청을 해보면 해당 API에 인가가 되지 않아 접속이 되지 않는 것을 알 수 있다.  
아래의 API에는 인증 요청없이 접근이 가능한 것을 알 수 있다.

```java
@RestController
@RequestMapping("/api")
public class HelloController {
    @GetMapping("/hello")
    @PreAuthorize("hasRole('ROLE_USER')")
    public String hello() {
        return "hello world!";
    }

    @GetMapping("/hi")
    public String hi() {
        return "hi world!";
    }
}

```

## PreAuthorize 

Expression-Based Access Control 즉 권한 제어를 위해서 쓰이는 기능이다.
메소드 기반 인가를 어노테이션과 expression으로 제공한다. 

- hasRole([role]) : 현재 사용자의 권한이 파라미터의 권한과 동일한 경우 true
- hasAnyRole([role1,role2]) : 현재 사용자의 권한디 파라미터의 권한 중 일치하는 것이 있는 경우 true
- permitAll() : 모든 접근 허용

- principal : 사용자를 증명하는 주요객체(User)를 직접 접근할 수 있다.
- authentication : SecurityContext에 있는 authentication 객체에 접근 할 수 있다.
- denyAll() : 모든 접근 비허용
- isAnonymous() : 현재 사용자가 익명(비로그인)인 상태인 경우 true
- isRememberMe() : 현재 사용자가 RememberMe 사용자라면 true
- isAuthenticated() : 현재 사용자가 익명이 아니라면 (로그인 상태라면) true
- isFullyAuthenticated() : 현재 사용자가 익명이거나 RememberMe 사용자가 아니라면 true

## 유저 등록 해보기

유저는 아래와 같은 방법으로 등록할 수 있다. 스프링 시큐리티가 원하는 유저 형태로 등록해주면 된다.

```java
@SpringBootTest
class UserCreationIntegrationTest {
    @Autowired
    private UserDetailsManager userDetailsManager;

    @Autowired
    private PasswordEncoder passwordEncoder;

    @Test
    public void createAdminUser() {
        UserDetails userDetails = User.builder()
                .username("sujin")
                .password(passwordEncoder.encode("1234"))
                .roles("ADMIN", "USER")
                .build();

        userDetailsManager.createUser(userDetails);
    }
}
```
