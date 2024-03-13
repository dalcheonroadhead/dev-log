# Google OAuth2 social Login without Security

*해당 내용은 [OAuth2.0 스프링부트로 Google 로그인 구현해보기-2](https://darrenlog.tistory.com/40) 를 보며 클론 코딩하였습니다.*

> 1. 큰 흐름 다시 보기 

## 1. 큰 흐름 다시 보기 

자세한 내용은 [[01] -OAuth2 이론 정리](https://dalcheonroadhead.tistory.com/381)에 정리해두었으니 OAuth2 흐름이 궁금하다면 해당 글을 보면 될 것 같다.  
이번 글에서는 Google 로그인을 구현한 프로젝트를 클론코딩하고, 흐름에 맞게 설명할려고 한다. 그러면 저번 글에서 사용했던 OAuth2 흐름에 따라 설명하겠다. 

![image-20240312214420781](https://github.com/dalcheonroadhead/Theory-study/assets/102154788/98ca550a-02bf-4490-aa80-6129dc04450e)


## 2. (1)~(4)의 과정 

**(1) Resource Owner가 Client App으로 로그인 요청한다. **

**(2) 그러면 클라이언트 APP은 Google Authorization Server의 로그인 검증 페이지로 요청을 Redirect 시킨다. ** 

**(3) Redirect된 주소로 가면 구글은 로그인 검증 페이지를 제공한다.**

> https://accounts.google.com/o/oauth2/auth?client_id=[CLIENT_ID&redirect_uri=REDIRECT_URI&response_type=code&scope=](https://accounts.google.com/o/oauth2/auth?client_id=CLIENT_ID&redirect_uri=REDIRECT_URI&response_type=code&scope=https://www.googleapis.com/auth/userinfo.email https://www.googleapis.com/auth/userinfo.profile)https://www.googleapis.com/auth/userinfo.email https://www.googleapis.com/auth/userinfo.profile

-> 해당 주소는 Google Authorization Server의 로그인 검증 페이지 주소이다. 해당 주소에서 CLIENT_ID와 REDIRECT_URI를 자신이 할당받은 것으로 바꾸면 사용가능 하다.
(1)에서 (2)번까지의 과정을 그림으로 표현하면, 
![image-20240312220113625](https://github.com/dalcheonroadhead/Theory-study/assets/102154788/75b64a59-2fa9-4c48-a8ea-8112ddb68d34)


사용자의 화면은 구글 인증 서버에서 제공하는 로그인 페이지로 이동할 것이다. 자, 이제 사용자가 자기 계정 중 하나를 선택했다고 가정 해보자. 그러면
**(4) Resource Owner의 ID/PW 제공** 까지 된 것이다. 

구글로 예를 들 때, Resource Owner가 저기 계정 중 하나를 골라 클릭하면 구글은 Authroization Code를 Client APP이 자신들에게 등록한 RedirectURL (클라이언트 앱으로의 주소)로 보내준다. 나는 다음과 같이 설정했다. 

![image-20240312221154840](https://github.com/dalcheonroadhead/Theory-study/assets/102154788/e823706b-9371-4cb8-8f91-cafda0306c5e)


## 3. (5) ~(8) 인가코드로 구글 인증 서버에서 Access Token 받기 

자신이 설정한 주소로 정상적으로 인가 코드(임시 허가증)이 왔다면, 
**(5) 인가코드 발급 받기, (6) 인가코드를 Redirect 주소로 보내기** 또한 마무리 된 것이다.  Redirect는 Get 요청이다. 따라서 나는 해당 요청이 들어오는 Controller를 만들고 해당 요청을 받았다. 

```java
@RestController
@AllArgsConstructor
@RequestMapping(value = "/login/oauth2", produces = "application/json")
public class LoginController {

    private final LoginService loginService;

    @GetMapping("/code/{registrationId}")
    public void googleLogin(@RequestParam String code, @PathVariable String registrationId){
        loginService.socialLogin(code, registrationId);

    }

}
```

registrationId는 확장성을 위해서 pathVariable로 달리 할 수 있게 했다. 예를 들어) "/code/naver"면 naver에서 들어온 인가 코드이고, "/code/google" 이면 구글에서 들어온 인가코드 이다. 해당 형식으로 여러 기업의 OAuth API에 Redirect 주소를 통일되게 적어놓으면 확장하기 편하다. 

  요청이 들어오면, QueryString으로 인가코드가 들어온다. 나는 들어온 값들을 바로 Service로직으로 넘겼다. 다음은 Service 로직을 살펴보자.
### ⓐ Service의 멤버 변수 

```java
    // 0) resource에 있는 설정 파일을 가져오기 위한 객체
    private final Environment env;

    // 0) Client로서의 역할을 하기 위한 RestTemplate
    private final RestTemplate restTemplate = new RestTemplate();

```



### ⓑ socialLogin Method

```java
    // 1) 로그인 프로세스 -> 인가코드로 Access Token을 얻고, 
	//    Access Token으로 Resource Server에서 필요한 값을 빼내서 온다.
    public void socialLogin(String code, String registrationId) {

        // (2) AT를 받는 로직, (3) User 정보를 얻는 로직
        String accessToken = getAccessToken(code, registrationId);
        JsonNode userResourceNode = getUserResource(accessToken, registrationId);


        System.out.println("userResourceNode = " + userResourceNode);

        String id = userResourceNode.get("id").asText();
        String email = userResourceNode.get("email").asText();
        String nickname = userResourceNode.get("name").asText();
        System.out.println("id = " + id);
        System.out.println("email = " + email);
        System.out.println("nickname = " + nickname);
    }
```

나는 해당 매소드에서 accessToken을 받는 것과, 받은 AccessToken으로 해당 user의 id, name, email을 Google Resource Server에 요청하여 받아오는 것까지 했다. 

### ⓒ getAccessToken

```java
    // 2) 인가코드를 주고 AccessToken 받는 매소드
    private String getAccessToken(String authorizationCode, String registrationId) {

        // 2-1) 각 OAuth2 API 별 Client id, Client-secret을 가져오기
        String clientId = env.getProperty("oauth2." + registrationId + ".client-id");
        String clientSecret = env.getProperty("oauth2."+registrationId +".client-secret");
        String redirectUri = env.getProperty("oauth2." + registrationId + ".redirect-uri");
        String tokenUri = env.getProperty("oauth2." + registrationId + ".token-uri");

        // 2-2) Access Token을 가져오기 위한 Request Params 세팅
        MultiValueMap<String, String> params = new LinkedMultiValueMap<>();
        params.add("code", authorizationCode);
        params.add("client_id", clientId);
        params.add("client_secret", clientSecret);
        params.add("redirect_uri", redirectUri);
        params.add("grant_type", "authorization_code");

        // 2-3) Header 설정
        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_FORM_URLENCODED);

        // 2-4) Body와 Header를 넣기
        HttpEntity entity = new HttpEntity(params, headers);

        // JsonNode == json tree 구조를 가진 객체
        // ResponseEntity == HttpEntity의 확장 클래스 -> HttpStatus 상태 코드를 추가한 전체 Http 응답 표현 클래스
        ResponseEntity<JsonNode> responseNode = restTemplate.exchange(tokenUri, HttpMethod.POST, entity, JsonNode.class);

        // 2-5) 요청을 보낸 뒤에 받은 body를 뜯어보고, 그거의 key 이름이 "access_token"인 녀석을 text형태로 바꾸어서 넣는다.
        JsonNode accessTokenNode = responseNode.getBody();
        return accessTokenNode.get("access_token").asText();
    }
```

임시허가증(인가 코드), 사업자 등록증(client-id, client-secret)을 가지고 진짜 허가권 (AccessToken)을 가져오는 절차이다. 해당 코드를 클론하면서 몰랐던 부분에 대해서 한 번 알아보겠다. 

**☆ MultiValueMap 이란?**

MultiValueMap은 Spring에서 제공하는 API이다. 그 안을 들여다보면, 
![image-20240313002153132](https://github.com/dalcheonroadhead/Theory-study/assets/102154788/9547bb24-7c6f-4d0e-a20e-e9c6d53fa192)

![image-20240313002425471](https://github.com/dalcheonroadhead/Theory-study/assets/102154788/66d6277c-58cc-41e5-b8f1-40b0be392290)


Map을 확장한 형태인데, Value 부분이 List로 이루어진 것을 알 수 있다. 해당 자료구조는 값을 넣는 명령어가 'put'이 아닌 'add' 인 지점에서도 List를 가지고 있음을 알 수 있다.  MultiValueMap는 하나의 Key가 여러개의 Value를 List 형태로 가질 수 있다. 예시 코드는 다음과 같다. 
```java
		Map<String, Integer> basicMap = new HashMap<>();
		MultiValueMap<String,Integer> multiValueMap = new LinkedMultiValueMap<>();
		basicMap.put("test",1);
		basicMap.put("test",2);
		multiValueMap.add("test",1);
		multiValueMap.add("test",2);
		System.out.println("basicMap = " + basicMap);
		System.out.println("multiValueMap = " + multiValueMap);
        
        => 실행결과
        multiValueMap = {test=[1, 2]}
		basicMap = {test=2}
```



**★RestTemplate란?** 

Spring에서 지원하는 객체로 간편하게 REST 방식의 API를 호출할 수 있는 Spring의 내장 클래스다. 여기서는 해당 RestTemplate의 exchange("목표URI", Method 방식, requestEntity(안에 담기는 내용), responseType)을 사용하여 구글 인증 서버로부터 Token을 받았고, 그걸 ResponseEntity에 넣어서 사용하고 있다. 



## 4. (10) ~ (14) AccessToken을 이용해 Google Resource 서버에 접근하여 원하는 값을 가져오기

이제 AccessToken도 있겠다. 해당 사용자의 구글 내 정보 중 가능한 것은 다 긁어올 수 있다 ㅎㅎ 여기서는 간단하게 해당 사용자의 프로필사진과 이메일, 아이디를 긁어와보자. 

### ⓓ get UserResource

```java
    // 3) 얻은 Access Token으로 resource 서버에서 원하는 값을 가져오기
    private JsonNode getUserResource(String accessToken, String registrationId){

        // 3-1) Resource Server는 어디지?
        String resourceUri = env.getProperty("oauth2." + registrationId + ".resource-uri");

        // 3-2) Header에 AccessToken 넣기
        HttpHeaders headers = new HttpHeaders();
        headers.set("Authorization", "Bearer" + accessToken);

        // 3-3) restTemplate 로 헤더를 넣어서 값을 보내고, 내가 Google API 로 미리 설정한, 값들을 가져온다.
        HttpEntity entity = new HttpEntity(headers);
        return restTemplate.exchange(resourceUri, HttpMethod.GET, entity, JsonNode.class).getBody();
    }
```

해당 부분은 주석에 다 설명했기에, 더 설명을 자세히 하지는 않겠다. 
  아까 socialLogin 서비스 맨 마지막 부분이 Resource Server에서 가져온 내용들을 그냥 콘솔에 찍는 것이었다. 결과는 다음과 같이 나온다. 

```json
{"id":"--------",
 "email":"-------","verified_email":true,"name":"전수민","given_name":"수민","family_name":"전","picture":"-------","locale":"ko"}

```

개인정보는 가렸다. 
