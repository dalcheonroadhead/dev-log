# 1. 개요

4월 5일부로 특화 프로젝트가 끝났다. 이번 프로젝트에서 보안을 맡았는데, 항상 내가 맡은 파트를 정리해야겠다는 생각을 했다. 벌써 2주 반이 흘렀기에, 더 까먹기 전에 미리 정리해두려한다.

해당 프로젝트의 전문을 보고 싶다면, 다음을 확인하자. 
[SSAFY 10기 구미 2반 특화프로젝트 WALK_WALK](https://github.com/dalcheonroadhead/WALKWALK)

# 2. 전개도 

![image-20240425093453098](C:\Users\SSAFY\AppData\Roaming\Typora\typora-user-images\image-20240425093453098.png)

# 3. 코드 분석

## (1) JwtAuthFilter 안에 있는 JwtUtil 분석

 ```java
 import io.jsonwebtoken.*;
 import io.jsonwebtoken.security.Keys;
 import jakarta.annotation.PostConstruct;
 import jakarta.servlet.http.HttpServletRequest;
 import lombok.RequiredArgsConstructor;
 import lombok.extern.slf4j.Slf4j;
 import org.springframework.beans.factory.annotation.Value;
 import org.springframework.core.env.Environment;
 import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
 import org.springframework.security.core.Authentication;
 import org.springframework.security.core.userdetails.UserDetails;
 import org.springframework.stereotype.Component;
 import org.springframework.util.StringUtils;
 import org.ssafy.d210._common.service.UserDetailsServiceImpl;
 import org.ssafy.d210.members.entity.Members;
 import java.security.Key;
 import java.util.Base64;
 import java.util.Date;
 import java.util.Objects;
 
 
 @Slf4j
 @Component
 @RequiredArgsConstructor
 public class JwtUtil {
 
     
     private final Environment env;
     private final UserDetailsServiceImpl userDetailsService;
     public static final String AUTHORIZATION_HEADER = "Authorization";
     private static final String BEARER_PREFIX = "Bearer ";
 
     @Value("${jwt.secret}")
     private String secretKey;
     private Key key;
     private final SignatureAlgorithm signatureAlgorithm = SignatureAlgorithm.HS256; // A. JWT C 파트(전자서명)만들 때, 쓸 알고리즘
 
     @PostConstruct // B. 객체 생성 이후에 바로 동작
     public void init() {
         byte [] bytes = Base64.getDecoder().decode(secretKey);
         key = Keys.hmacShaKeyFor(bytes);
     }
 
     // C. 요청 Header에서 토큰 가져와서 Bearer 접두사 때기
     public String resolveToken(HttpServletRequest request) {
         String bearerToken = request.getHeader(AUTHORIZATION_HEADER);
         if(StringUtils.hasText(bearerToken) && bearerToken.startsWith(BEARER_PREFIX)){
             return bearerToken.substring(7);
         }
         return null;
     }
 
     /* D. 토큰 만들기 */
     public String createToken (Members member, boolean isRT ){
 
         int TOKEN_TIME = 0;
 
         // 해당 값으로 [AccessToken]을 만들 것인지, [RefreshToken]을 만들 것인지 확인한다.
         if(!isRT){
             TOKEN_TIME = Integer.parseInt(Objects.requireNonNull(env.getProperty("jwt.token.access-expiration-time")));
         }else{
             TOKEN_TIME = Integer.parseInt(Objects.requireNonNull(env.getProperty("jwt.token.refresh-expiration-time")));
         }
 
         Date now = new Date();
 
 
         return BEARER_PREFIX +
                 Jwts.builder()
                         .setSubject(member.getEmail())  // 이메일로 token의 주인을 찾고 정보 얻어올 것이다.
                         .setIssuedAt(new Date(now.getTime())) // 발행일
                         .setExpiration(new Date(now.getTime() + TOKEN_TIME)) // 토큰 수명
                         .signWith(key, signatureAlgorithm)  // 전자 서명
                         .compact();
     }
 
     // E. 토큰 검증하기
     public int validateToken(String token) {
 
         try {
             Jwts.parserBuilder().setSigningKey(key).build().parseClaimsJws(token);
             return 1;
         } catch (io.jsonwebtoken.security.SignatureException | SecurityException | MalformedJwtException e){
             log.error("Invalid JWT signature, 유효하지 않는 JWT 서명 입니다.");
             log.error("관련에러: {}", e.getMessage());
             return -1;
         } catch (UnsupportedJwtException e) {
             log.error("Unsupported JWT token, 지원되지 않는 JWT 토큰 입니다.");
             log.error("관련에러: {}", e.getMessage());
             return -1;
         } catch (IllegalArgumentException e) {
             log.error("JWT claims is empty, 잘못된 JWT 토큰 입니다.");
             log.error("관련에러: {}", e.getMessage());
             return -1;
         } catch (ExpiredJwtException e) {
             log.error("Expired JWT token, 만료된 JWT token 입니다.");
             log.error("관련에러: {}", e.getMessage());
             return -2;
         }
     }
 
     // F. 토큰 해부해서 사용자 정보 다시 빼내기
     public Claims getUserInfoFromToken(String token) {
         return Jwts.parserBuilder().setSigningKey(key).build().parseClaimsJws(token).getBody();
     }
 
     // E. [userEmail]을 받아서 [DB]에서 정보를 가져와 인증 객체 생성
  	//    해당 인증 객체가 Security context에 적혀서 하나의 Thread 내에서 전역으로 사용할 수 있게 된다.
     
     public Authentication createAuthentication(String userEmail) {
         UserDetails userDetails = userDetailsService.loadUserByUsername(userEmail);
         return new UsernamePasswordAuthenticationToken(userDetails, null,                                   userDetails.getAuthorities());
     }
 }
 
 ```

## (2) JwtAuthFilter 

JwtUtil을 이용하여, 들어온 요청의 JWT 토큰을 인증한다. 인증에 성공하면, 해당 JWT 토큰을 이용해 인증 객체를 만들어서, SecurityContextHolder에 기록해놓는다. 
 SecurityContextHolder는 하나의 명부로 해당 명부에 기록해놓으면, 해당 인증객체는 동일 Thread 내의 프로젝트 전역에서 쓸 수 있다.

```java

import io.jsonwebtoken.Claims;
import jakarta.servlet.FilterChain;
import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.context.SecurityContext;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.web.filter.OncePerRequestFilter;
import org.ssafy.d210._common.exception.ErrorType;

import java.io.IOException;

@Slf4j
@RequiredArgsConstructor
public class JwtAuthFilter extends OncePerRequestFilter {

    private final   JwtUtil jwtUtil;
    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        // A. request 에 담긴 토큰의 실제 부분(Bearer 뺀 부분)을 가져온다.
        String token = jwtUtil.resolveToken(request);

        // B. 토큰이 null 이면 다음 필터로 넘어간다
        if (token == null) {

            filterChain.doFilter(request, response);
            return;
        }

        // C. 토큰이 유효하지 않으면 Exception 체크하고 다음 필터로 넘어간다

        int validationCheck = jwtUtil.validateToken(token);

        if (validationCheck < 0) {
            if(validationCheck == -1) {
                request.setAttribute("exception", ErrorType.NOT_VALID_TOKEN.toString());
                filterChain.doFilter(request, response);
                return;
            }else if (validationCheck == -2){
                request.setAttribute("exception", ErrorType.EXPIRED_TOKEN.toString());
            }
        }

        // D. 유효한 토큰이라면, 토큰으로부터 사용자 정보를 가져온다.
        Claims info = jwtUtil.getUserInfoFromToken(token);  // 토큰 해부

        log.info("토큰에 들어있는 값={}",info.toString());


        try {

            setAuthentication(info.getSubject());   // 이메일로 사용자 정보를 얻어오고, 그 사용자 정보로 인증 객체 만든다.

        } catch (UsernameNotFoundException e) {
            request.setAttribute("exception", ErrorType.NOT_FOUND_MEMBER.toString());
            log.error("관련에러: {}", e.getMessage());
        }
        // 다음 필터로 넘어간다.
        filterChain.doFilter(request, response);

    }

    private void setAuthentication(String userEmail) {

        SecurityContext context = SecurityContextHolder.createEmptyContext();   // 서버에서 인증 객체용 [Thread]를 만듬
        Authentication authentication = jwtUtil.createAuthentication(userEmail); // 인증 객체 만들기
        context.setAuthentication(authentication);  // 해당 [Thread]에 객체를 기록

        SecurityContextHolder.setContext(context); // [Thread]를 서버 전체 컨테이너에 기록
    }
}
```

## (2) Spring Security Config

```java
import jakarta.servlet.DispatcherType;
import jakarta.servlet.http.HttpServletRequest;
import lombok.RequiredArgsConstructor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.HttpMethod;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configurers.AbstractHttpConfigurer;
import org.springframework.security.config.annotation.web.configurers.HeadersConfigurer;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.SecurityFilterChain;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.CorsConfigurationSource;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;
import org.ssafy.d210._common.exception.AccessDeniedHandler;
import org.ssafy.d210._common.exception.AuthenticationEntryPoint;
import org.ssafy.d210._common.service.jwt.JwtAuthFilter;
import org.ssafy.d210._common.service.jwt.JwtUtil;

import java.util.Arrays;
import java.util.Collections;

@Configuration
@EnableWebSecurity
@RequiredArgsConstructor
public class SecurityConfig {


    // * Security용
    private final AuthenticationEntryPoint authenticationEntryPoint;
    private final AccessDeniedHandler accessDeniedHandler;
    private final JwtUtil jwtUtil;

    private final String [] whiteList =
            {"/api/**", "/ws-stomp/**",
                    "/swagger-ui/**",
                    "/api-docs/**","/swagger-resources/**", "/webjars/**"
    ,"/oauth2/**", "/error"};


    @Bean
    // 1. 비밀번호를 안전하게 저장할 수 있도록 비밀번호의 단방향 암호화를 지원하는 인터페이스
    // 우리는 해당 인터페이스의 구현체 중 하나인 BCryptPasswordEncoder를 사용한다.
    // 해당 함수는 Bcrypt라는 해시 함수를 사용해 비밀번호를 암호화 한다.
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Bean
    /* 2.  SpringSecurity에서 제공하는 인증, 인가를 위한 필터들의 모음
     *     가장 핵심이 되는 기능을 제공, 거의 대부분의 Security 서비스는 이 Filter Chain에서 실행됨
     *     개발 취지에 따라 기본 제공되는 필터들 사이에 커스텀 필터를 넣어도 된다.
     */
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
                .formLogin(AbstractHttpConfigurer::disable);       // 1)
        http
                .httpBasic(AbstractHttpConfigurer::disable);       // 2)
        http
                .csrf(AbstractHttpConfigurer::disable);            // 3)

        http
                .cors((corsCustomizer -> corsCustomizer.configurationSource(request -> {

                    CorsConfiguration configuration = new CorsConfiguration();

                    configuration.setAllowedOriginPatterns(Arrays.asList("*"));
                    configuration.setAllowedMethods(Arrays.asList("GET","POST", "PUT", "DELETE", "OPTIONS", "PATCH"));
                    configuration.setAllowCredentials(true);
                    configuration.setAllowedHeaders(Arrays.asList("*"));
                    configuration.setExposedHeaders(Collections.singletonList("Authorization"));

                    return configuration;
                })));
        http
                .headers((headers) -> headers.frameOptions(
                        HeadersConfigurer.FrameOptionsConfig::sameOrigin
                ));                                                 // 4)
        http
                .sessionManagement((sessionManagement) ->
                        sessionManagement.sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                );                                                  // 5)

        http                                                        // 6)
                .authorizeHttpRequests(
                        (auth) ->
                                auth
                                        .dispatcherTypeMatchers(DispatcherType.FORWARD).permitAll()
                                        .dispatcherTypeMatchers(DispatcherType.ASYNC).permitAll()

                                        .requestMatchers(whiteList).permitAll()
                                        .anyRequest().authenticated()

                )
                   .exceptionHandling(authentication ->        // 7)
                           authentication.authenticationEntryPoint(authenticationEntryPoint)
                                  .accessDeniedHandler(accessDeniedHandler))

                // 8)
                .addFilterBefore(new JwtAuthFilter(jwtUtil), UsernamePasswordAuthenticationFilter.class);

        return http.build();


    }
```

1. **formLogin(AbstractHttpConfigurer::disable)**
   : Spring에서 지원하는 Login 화면을 쓰지 않겠다. 
    해당 로그인 화면은 사용자가 접근하고자 하는 URL에 인증이 필요할 경우 반환되는 화면이다.

    

2. **httpBasic(AbstractHttpConfigurer::disable)**
   : httpBasic 인증 방식을 비활성화 한다. 
    httpBasic을 활성화 하면 formLogin과 똑같이, 인증 받아야 접근이 가능한 리소스일 경우, 접근 시 Http 프롬프트 인증창이 뜬다. 
    httpBasic의 경우, requestHeader에 Authorization : "Basic ddjlsflsfdfdf..." 과 같은 유효한 Basic 토큰이 있는 경우, 프롬프트 창 없이 바로 인증도 가능하다. 

   우리는 Bearer 형식의 JWT 토큰을 쓸 것임으로 필요없다.

    

3. **CORS**

   :  도메인 - 프로토콜 - 포트가 같은 리소스는 모두 SAME ORIGIN이라고 함. 
     이것이 다른 곳에서 리소스를 얻어오는 것을 CROSS-ORIGIN이라고 한다. 포트가 다른 프론트에서 백엔드 리소스를 얻어오려면 다음과 같은 CORS 설정을 해줘야 한다.  

       // 1. 해당 서버의 resource를 쓸 수 있는 다른 출처의 목록을 정한다. (여기선 모든 다른 출처가 가능하게 했다.)
       configuration.setAllowedOriginPatterns(Arrays.asList("*"));
       
       // 2. 허용되는 Method 방법을 정한다.
       configuration.setAllowedMethods(Arrays.asList("GET","POST", "PUT", "DELETE", "OPTIONS", "PATCH"));
       
       // 3. 자격증명을 허용한다. 기본적으로는 다른 출처의 리소스로 요청을 보낼 때, 쿠키나 authorization Header들, http 인증 등이 전송되지 않는데, 클라이언트 쪽에서 해당 내용들을 담아서 오면, 그것을 자동 BAN 하지 않고, 읽게 하겠다는 뜻이다. 
       // 사실 이것을 허용하면 출처 목록을 *로 표기할 수 없다. CORS 정책상 허용되는 요청 출처 목록을 특정해야함. 하지만, 1번의 함수를 쓰면, 그것을 우회하여, 모든 요청을 허용할 수 있는 듯 하다. 
       configuration.setAllowCredentials(true);
       
       // 4. 다른 출처의 요청에 들어 있어도 되는 Header의 목록을 명시한다.
       configuration.setAllowedHeaders(Arrays.asList("*"));
       
       // 5. 응답 헤더에 들어가는 값들을 명시한다. 여기 명시해주지 않으면, header에 값을 집어 넣더라도 안된다. 
       //    고정 불변의 딱 하나의 인자만 가지는 경우 singletonList를 쓴다. -> 해당 리스트에 무엇인가 추가하려 하면 Error가 난다.
       configuration.setExposedHeaders(Collections.singletonList("Authorization"));

4. **X-Frame-Options**

   현 웹페이지가 다른 웹사이트의 프레임(iframe) 내에 표시되는 것을 방지하기 위해 사용 -> ClickJacking 공격을 보안하기 위한 보안 메커니즘임. 

   ```java
   headers((headers) -> headers.frameOptions(
                           HeadersConfigurer.FrameOptionsConfig::sameOrigin
                   ));   
   ```

   해당 코드를 보면, 프레임 내 표시를 자신과 같은 출저의 웹사이트에서만 가능하도록 허용함.  다른 출처의 Iframe 내에서는 해당 서버의 페이지를 로드할 수 없음. API 서버라, iframe으로 걸릴 일이 많지 않겠지만, 그냥 API 요청을 Iframe으로 걸어서 계속 누르게 하는 것을 방지하기 위함인 것 같음. 

5. **경로 별 허용**
   Spring Security 6으로 바뀌면서 서버 내 서블릿에서 서블릿 호출 (Forward, Redirect), 서블릿 비동기 호출 등 또한 Spring Filter에 검증을 받도록 변경이 되었다. 따라서 에러가 나서 스프링 자체 제공 에러 페이지로 Redirection 하는 상황이나 비동기 연결 같은 경우, 인증 실패하여, 실행이 되지 않는 일이 생기기 마련이다. 

     이러한 현상을 없애기 위하여, FOWARD와 ASYNC 모두 permitAll()을 해준다. 

```java
// 1. from Servlet to Servlet Forward는 무조건 허용
.dispatcherTypeMatchers(DispatcherType.FORWARD).permitAll()

// 2. Servlet 비동기 호출은 무조건 허용 
.dispatcherTypeMatchers(DispatcherType.ASYNC).permitAll()
```



# (3) AuthenticationEntryPoint 

```java
import com.fasterxml.jackson.databind.ObjectMapper;
import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import lombok.extern.slf4j.Slf4j;
import org.springframework.security.core.AuthenticationException;
import org.springframework.stereotype.Component;
import org.ssafy.d210._common.response.ResponseUtils;

import java.io.IOException;
import java.util.Enumeration;

/*
 * (〜￣△￣)〜    인증 되지 않은 사용자의 요청에 대한 처리       〜(￣△￣〜)
 *  A.  시큐리티 필터체인에서 인증 되지 않은 사용자의 요청이 들어오면 에러가 생기고
 *      해당 EntryPoint로 들어온다.
 *
 * */
@Component
@Slf4j
public class AuthenticationEntryPoint implements org.springframework.security.web.AuthenticationEntryPoint {


    @Override
    public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException authException) throws IOException, ServletException {


        String exception = (String) request.getAttribute("exception");


        if(exception != null) {
            if (ErrorType.valueOf(exception).equals(ErrorType.TOKEN_DOESNT_EXIST)) {
                exceptionHandler(response, ErrorType.TOKEN_DOESNT_EXIST);
                return;
            }

            if (ErrorType.valueOf(exception).equals(ErrorType.NOT_VALID_TOKEN)) {
                exceptionHandler(response, ErrorType.NOT_VALID_TOKEN);
                return;
            }

            if(ErrorType.valueOf(exception).equals(ErrorType.EXPIRED_TOKEN)) {

                exceptionHandler(response, ErrorType.EXPIRED_TOKEN);
                return;
            }

            if (ErrorType.valueOf(exception).equals(ErrorType.NOT_FOUND_MEMBER)) {
                exceptionHandler(response, ErrorType.NOT_FOUND_MEMBER);
            }

        }else {
            authException.printStackTrace();
            exceptionHandler(response, ErrorType.ANOTHER_ERROR);
        }
    }


    // B. 에러에 대한 답변을 만드는 공간이다.
    public  void exceptionHandler(HttpServletResponse response, ErrorType error) {
        // B-1 에러에 대한 MetaData
        response.setStatus(error.getCode());
        response.setContentType("application/json");
        response.setCharacterEncoding("UTF-8");


        try {
            // B-2 에러가 무엇인지에 대한 Body를 Response에 쓴다.
            String json = new ObjectMapper().writeValueAsString(ResponseUtils.error(ErrorResponse.of(error)));
            response.getWriter().write(json);

            //      에러 내용 로그 확인
            log.error(" 에러 내용은 다음과 같습니다.={}", error.getMsg());
        } catch (Exception e){
            // B-3 Response 작성 과정에서 에러가 났을 경우 알려준다.
            log.error(e.getMessage());
        }
    }
}
```

