# JWT 인증

전체적인 구조는 유저ID/Password 검증 필터 앞에서 JWT 검증을 미리 수행해버리는 것이다.


jwtTokenProvider에 validate와 parseId 메소드를 추가하자.

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

        return Jwts.builder().setSubject(id)
                .signWith(key, SignatureAlgorithm.HS256)
                .setExpiration(new Date(now + 60 * 60 * 1000))
                .compact();
    }

    public boolean validate(String token) {
        Jwts.parserBuilder().setSigningKey(key).build().parseClaimsJws(token).getBody();
        return true;
    }

    public String parseId(String token) {
        Claims claims = Jwts
                .parserBuilder()
                .setSigningKey(key)
                .build()
                .parseClaimsJws(token)
                .getBody();

        return claims.getSubject();
    }
}
```

아래와 같이 필터를 작성해보자.

```java
@Component
public class JwtTokenFilter extends OncePerRequestFilter {

    private final JwtTokenProvider jwtTokenProvider;
    private final UserDetailsManager userDetailsManager;

    public JwtTokenFilter(JwtTokenProvider jwtTokenUtil, UserDetailsManager userDetailsManager) {
        this.jwtTokenProvider = jwtTokenUtil;
        this.userDetailsManager = userDetailsManager;
    }

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain chain)
            throws ServletException, IOException {
        // Get authorization header and validate
        final String header = request.getHeader(HttpHeaders.AUTHORIZATION);

        if (isEmpty(header) || !header.startsWith("Bearer ")) {
            chain.doFilter(request, response);
            return;
        }

        // Get jwt token and validate
        final String token = header.split(" ")[1].trim();

        if (!jwtTokenProvider.validate(token)) {
            chain.doFilter(request, response);
            return;
        }

        // Get user identity and set it on the spring security context
        UserDetails userDetails = userDetailsManager.loadUserByUsername(jwtTokenProvider.parseId(token));

        UsernamePasswordAuthenticationToken authentication = new UsernamePasswordAuthenticationToken(
                userDetails, null,
                userDetails == null ?
                        List.of() : userDetails.getAuthorities()
        );

        authentication.setDetails(
                new WebAuthenticationDetailsSource().buildDetails(request)
        );

        SecurityContextHolder.getContext().setAuthentication(authentication);
        chain.doFilter(request, response);
    }
}
```

Session을 사용하지 않을 것이므로 세션 기능을 끄고, filter를 추가함으로써 설정은 마무리 된다.

```java
@Configuration
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http, JwtTokenFilter jwtTokenFilter) throws Exception {
        http
                .formLogin();

        http
                .cors()
                .and()
                .csrf()
                .disable();

        http
                .logout();

        http
                .sessionManagement()
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS);

        http.addFilterBefore(
                jwtTokenFilter,
                UsernamePasswordAuthenticationFilter.class
        );

        return http.build();
    }
    
    //... 중략 ...
}
```
