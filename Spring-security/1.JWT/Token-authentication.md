# 토큰 인증 방식에 대하여

> 목차
>
> 1. Token 인증 방식이 어떻게 이루어지는가 
> 2. 그렇다면 기존의 Session 방식이랑 어떻게 다른가? 

## 1. Token 인증 방식이 어떻게 이루어 지는가

회원이 로그인을 시도한다고 가정해보자.

ⓐ Client가 ID/PW 로그인 창에 써서 제출한다. 

![image-20240313204349679](C:\Users\SSAFY\AppData\Roaming\Typora\typora-user-images\image-20240313204349679.png)

ⓑ Server가 DB를 통해 해당 ID/PW를 가진 회원이 있는지 확인한다. 
   여기서 해당 회원의 정보가 우리 DB에 있어서 우리 회원인 것이 확인 되었다고 가정해보자. 

![image-20240313204751998](C:\Users\SSAFY\AppData\Roaming\Typora\typora-user-images\image-20240313204751998.png)

ⓒ Server에서 해당 회원의 정보를 이용하여 고유한 Token을 만든다. 

![image-20240313205247394](C:\Users\SSAFY\AppData\Roaming\Typora\typora-user-images\image-20240313205247394.png)

ⓓ 그리고 해당 토큰을 아까 유효한 요청을 한 클라이언트에게 보내준다. 

![image-20240313205344981](C:\Users\SSAFY\AppData\Roaming\Typora\typora-user-images\image-20240313205344981.png)

ⓔ 이제 클라이언트는 인증이 필요한 API를 이용할 경우 해당 Token을 요청 Header에 동봉하여 같이 보낸다. 

![image-20240313205541004](C:\Users\SSAFY\AppData\Roaming\Typora\typora-user-images\image-20240313205541004.png)

ⓕ 이제 서버에서는 해당 Token의 유효성을 검증 후 유효한 토큰이 맞다면 응답으로 요청한 내용에 대한 정상 답변을 보내주면 된다.



## 그렇다면 기존의 Session 방식과는 어떻게 다른가? 

세션방식의 경우 서버라는 공무원이 개인 사업자 명부를 가지고 있는 형태와 같다. 개인 사업자가 자신의 허가증을 들고 동사무소에 찾아와서 뭔가 정보를 얻어간다고 해보자. 그러면 창구의 직원은 사업자 명부라는 두꺼운 책을 일일히 뒤져가며 해당 요청자가 진짜 허가를 받은 개인 사업자가 맞는지 확인해야한다. 
  만약 이렇게  동사무소에 찾아오는 개인 사업자가  많다면 어떻게 될까? 
동사무소를 가득채워서 불만을 터트릴 것이다. 왜냐하면 해당 일을 처리하는 공무원이 한 명이기 때문이다. 세션 방식 또한 똑같이 우리 회원의 Session 정보를 서버에서 계속 유지하고 있어야 하므로(Stateful), 인증 요청이 증가함에 따라 성능이 저하된다. 

![image-20240313210839688](C:\Users\SSAFY\AppData\Roaming\Typora\typora-user-images\image-20240313210839688.png)

**반면 Token 인증 방식의 경우** 인증 정보를 클라이언트가 분산해서 가지고 있기 때문에, 사용자가 증가한다고 해서 성능이 저하되지 않는다. (stateless) 서버는 그저 토큰 정보의 유효성만 확인해주면 되기 때문이다. 그 후 인증 요청이 유효하다면 DB로 들어가서 필요한 정보를 꺼내서 주면 된다. 
  근데 여기서 Token 인증 방식으로 JWT를 쓴다면 DB까지 가는 횟수가 현저하게 준다. 왜냐하면 Token 자체에 사용자에 대한 개인 정보를 담고 있어서 간단한 요청의 경우, Token에서 꺼내서 쓰면 되기 때문이다. 다음에는 JWT를 이용한 Token 인증 방식에 대해 공부해보겠다. 