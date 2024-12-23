# 1. 문제 설명

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/12902#)

### 문제 설명



가로 길이가 2이고 세로의 길이가 1인 직사각형 모양의 타일이 있습니다. 이 직사각형 타일을 이용하여 세로의 길이가 3이고 가로의 길이가 n인 바닥을 가득 채우려고 합니다. 타일을 채울 때는 다음과 같이 2가지 방법이 있습니다

- 타일을 가로로 배치 하는 경우
- 타일을 세로로 배치 하는 경우

예를들어서 n이 8인 직사각형은 다음과 같이 채울 수 있습니다.

[![Imgur](https://camo.githubusercontent.com/0ba77033475c9f1d4a904a48dcbf3b19632658671cd0be091e4d52c01534d390/68747470733a2f2f692e696d6775722e636f6d2f7a4257377065492e706e67)](https://camo.githubusercontent.com/0ba77033475c9f1d4a904a48dcbf3b19632658671cd0be091e4d52c01534d390/68747470733a2f2f692e696d6775722e636f6d2f7a4257377065492e706e67)

직사각형의 가로의 길이 n이 매개변수로 주어질 때, 이 직사각형을 채우는 방법의 수를 return 하는 solution 함수를 완성해주세요.

##### 제한사항



- 가로의 길이 n은 5,000이하의 자연수 입니다.
- 경우의 수가 많아 질 수 있으므로, 경우의 수를 1,000,000,007으로 나눈 나머지를 return해주세요.

##### 입출력 예



| n    | result |
| ---- | ------ |
| 4    | 11     |

##### 입출력 예 설명



입출력 예 #1
다음과 같이 11가지 방법이 있다.
[![Imgur](https://camo.githubusercontent.com/06a924fa5b4c377c16bfb95bc3c1e8fa1dc9fa94cc9949f84c0a5ab937061edf/68747470733a2f2f692e696d6775722e636f6d2f6e6e6f54396b4c2e706e67)](https://camo.githubusercontent.com/06a924fa5b4c377c16bfb95bc3c1e8fa1dc9fa94cc9949f84c0a5ab937061edf/68747470733a2f2f692e696d6775722e636f6d2f6e6e6f54396b4c2e706e67)
[![Imgur](https://camo.githubusercontent.com/cd9e5a6b6526e59f78ad1671116f98b6ed6dedf38c0b792edad4c3c2894bf8a7/68747470733a2f2f692e696d6775722e636f6d2f51545a467254482e706e67)](https://camo.githubusercontent.com/cd9e5a6b6526e59f78ad1671116f98b6ed6dedf38c0b792edad4c3c2894bf8a7/68747470733a2f2f692e696d6775722e636f6d2f51545a467254482e706e67)
[![Imgur](https://camo.githubusercontent.com/484e16d96c15f4eac6580334f6c8077da2b386c7d4c973eec49f64bfd8202342/68747470733a2f2f692e696d6775722e636f6d2f5945314a664a6e2e706e67)](https://camo.githubusercontent.com/484e16d96c15f4eac6580334f6c8077da2b386c7d4c973eec49f64bfd8202342/68747470733a2f2f692e696d6775722e636f6d2f5945314a664a6e2e706e67)
[![Imgur](https://camo.githubusercontent.com/81104c42643726f82d7ccc66eb5121b68eafdcdb8bbd883b50c5d0ef6b931ebe/68747470733a2f2f692e696d6775722e636f6d2f516859765254722e706e67)](https://camo.githubusercontent.com/81104c42643726f82d7ccc66eb5121b68eafdcdb8bbd883b50c5d0ef6b931ebe/68747470733a2f2f692e696d6775722e636f6d2f516859765254722e706e67)
[![Imgur](https://camo.githubusercontent.com/845dec9e07d0ecff4262ebc6ef769bcbbf1c4821a09ff1db63b77511e387bbdd/68747470733a2f2f692e696d6775722e636f6d2f4e4b674b5449522e706e67)](https://camo.githubusercontent.com/845dec9e07d0ecff4262ebc6ef769bcbbf1c4821a09ff1db63b77511e387bbdd/68747470733a2f2f692e696d6775722e636f6d2f4e4b674b5449522e706e67)
[![Imgur](https://camo.githubusercontent.com/87af0b0f8832e773a0bece7e4f3b02dda5401b45fa1e8d10981bad754ba2643a/68747470733a2f2f692e696d6775722e636f6d2f33756f624678652e706e67)](https://camo.githubusercontent.com/87af0b0f8832e773a0bece7e4f3b02dda5401b45fa1e8d10981bad754ba2643a/68747470733a2f2f692e696d6775722e636f6d2f33756f624678652e706e67)
[![Imgur](https://camo.githubusercontent.com/7c07465b4c34522b9756ef0c92d20c0e79d438705af0bc94cc07d0bb70826f9b/68747470733a2f2f692e696d6775722e636f6d2f73454b396f6f722e706e67)](https://camo.githubusercontent.com/7c07465b4c34522b9756ef0c92d20c0e79d438705af0bc94cc07d0bb70826f9b/68747470733a2f2f692e696d6775722e636f6d2f73454b396f6f722e706e67)
[![Imgur](https://camo.githubusercontent.com/08691a0e1dbdb67530a0f8810b50b1d284fc624849f58680150138f023015279/68747470733a2f2f692e696d6775722e636f6d2f753664706965702e706e67)](https://camo.githubusercontent.com/08691a0e1dbdb67530a0f8810b50b1d284fc624849f58680150138f023015279/68747470733a2f2f692e696d6775722e636f6d2f753664706965702e706e67)
[![Imgur](https://camo.githubusercontent.com/a462a70ddadebe621f2dd2cda8ce7e2de11e70b7e5a495bdbb0fe00d48f59f23/68747470733a2f2f692e696d6775722e636f6d2f7265334331394e2e706e67)](https://camo.githubusercontent.com/a462a70ddadebe621f2dd2cda8ce7e2de11e70b7e5a495bdbb0fe00d48f59f23/68747470733a2f2f692e696d6775722e636f6d2f7265334331394e2e706e67)
[![Imgur](https://camo.githubusercontent.com/1cf32eec2a8effae70f3c9c12901232f4d207f92a7e76c2e1cf3fe0996657d82/68747470733a2f2f692e696d6775722e636f6d2f47657264414a422e706e67)](https://camo.githubusercontent.com/1cf32eec2a8effae70f3c9c12901232f4d207f92a7e76c2e1cf3fe0996657d82/68747470733a2f2f692e696d6775722e636f6d2f47657264414a422e706e67)
[![Imgur](https://camo.githubusercontent.com/e84701bc4920954c37cce598e38f5def6d8259b28e4191e53fe1b95c9bed2b39/68747470733a2f2f692e696d6775722e636f6d2f49546362576a302e706e67)](https://camo.githubusercontent.com/e84701bc4920954c37cce598e38f5def6d8259b28e4191e53fe1b95c9bed2b39/68747470733a2f2f692e696d6775722e636f6d2f49546362576a302e706e67)

> 출처: 프로그래머스 코딩 테스트 연습, https://school.programmers.co.kr/learn/challenges

# 2. 문제 풀이

#### keyword

: stack-up 하는 DP, 모듈러 분배 법칙 

안 풀려서 다른 사람의 풀이를 봤다. 그 중에서도 이해가 제일 잘가는 풀이로 설명 하겠다. 

## (1) 사전 지식

먼저 사용 되는 타일이 1 x 2 짜리 타일이므로, 홀수 칸의 바닥은 무조건 채우지 못한다. 
따라서, DP의 stackup 되는 규칙성을 구하기 위해서는, 짝수 칸으로만 생각 해야한다. 그럼 n= 2부터 n = 8 까지 같이 알아보고 규칙성을 찾아보자. 

### ⓐ n=2 일 때,

![image-20240616181414170](https://github.com/dalcheonroadhead/algo/assets/102154788/46f638ed-42b2-4df6-8e9e-3a2473505f7e)

n=2인 바닥을 전부 채우는 경우는 위의 3가지이다. 

### ⓑ n=4 일 때,

![image-20240616181555852](https://github.com/dalcheonroadhead/algo/assets/102154788/dbcdffef-fb2e-492d-a3e1-588820c32482)


자, 이제부터 집중해야 한다. 

n=4인 바닥을 타일로 채울 때는 다음 2가지의 유형이 존재한다. 

**첫 번째로, 이전 것들을 조합해 채우는 경우이다.**
n=2인 타일에서 2칸 더 추가해서 이어나간다고 가정하자. 이때 저 추가된 물음표에 무엇이 들어갈까? 
당연하게도, n=2에서 나왔던 3가지 경우의 수이다. 그렇다면 각 경우마다 3가지 버전이 더 생기므로, 3 x 3 = 9가지 경우가 나온다. 

**두 번째로, 이전 것들을 조합하지 않는 완전히 새로운 경우이다.**
원래는 이전 경우의 수인 n=2 타일을 조합해서 만들었다면, 이번에는 그 각자의 영역을 침범하여 n=4 를 채우는 경우다. 이건 그냥 생각해내는 수 밖에 없다. 위와 같이 2가지 경우가 생겨난다. 

### ⓒ n=6 일 때,

![image-20240616182245691](https://github.com/dalcheonroadhead/algo/assets/102154788/5edb12d6-ede3-4304-aba3-4abc00f86e76)


이것도 n=4와 똑같이 2가지 유형으로 나누어서 생각해야한다.

**첫 번째, 이전에 계산한 것들 조합**

- n=4가 앞에 나오는 경우를 생각해보자. n=4인 타일을 가득 채우는 경우의 수마다, 나머지 n=2인 타일을 채우는 3가지 루트가 존재한다. 따라서 f(4)*f(2)가 된다. 

- n=2가 앞에 오는 경우도 생각해야한다. 
  `n=4를 채우는 경우의 수 중에 n=2인 경우 2가지를 조합해서 만든 것이 포함되어 있는데 왜 또 생각해야하죠?`라고 의문이 들 수 있겠다. 그 이유는, n=4의 2번째 유형인 `조합 하지 않고 타일을 전체 채우는 경우의 수` 때문이다.  n=2 타일링 + n=4의 (2)는 새로운 경우의 수라고 할 수 있겠다.
  *※주의점※*
  n=4의 두 번째 유형도  n=4 타일링 + n=2 타일링 조합에 포함된다! 다만 n=4의 두 번째 유형 + n=2 타일링과 n=2타일링 + n=4의 두번째 유형은 순서가 달라서 서로 다른 경우의 수로 체크되기 때문에 다시 세어준 것이다.

  

**두 번째, 이전 것들을 조합하지 않는 완전히 새로운 경우**
여기서도 2개다. 

### ⓓ n=8일 때, 

![image-20240616183543413](https://github.com/dalcheonroadhead/algo/assets/102154788/abac003d-e24c-4b94-b020-c63a16008e2f)

**첫 번째, 이전까지 나온 경우의 수들을 조합하는 경우**
더 설명하지 않아도 될 것 같다. 

- n=6 타일링 x n=2 타일링으로 만드는 조합  **(이전 타일링 경우의 수 x n=2의 타일링 경우의 수)**
- n=4, n=6 에서 나온 새로운 경우들을 이용한 조합 -> 이것은 지금까지 계속 2개씩만 나온 것으로 미루어 보아, 앞으로도 그럴 것이다.  **(가로가 n인 바닥의 타일링 x 2)**  

두 번째, 완전히 새로운 경우의 수 

**항상 2가지**

### 점화식 계산 

지금까지 알아본 규칙성으로 가로가 n인 바닥을 완전히 메우는 경우의 수는 다음과 같은 식으로 구할 수 있다.

![image-20240616184242481](https://github.com/dalcheonroadhead/algo/assets/102154788/cff9799e-0ac9-45b4-80c2-8a48d2d1d4fe)


# 3. 코드 분석

```java
class Solution {
    public long solution(int n) {      
        long [] dp = new long [n+1];
        // 초기 값 설정
        dp[2] = 3;
        
        for(int i = 4; i <= n; i+=2){
            // 초기 값 설정 (이전 꺼 * dp[2] 인 경우의 수 + 영역 범람하여 만들 수 있는 경우의 수 2개)
            dp[i] = dp[i-2]*dp[2] + 2;
            
            // dp 값 완성
            for(int j = 2; j <= i-4; j+=2) {
                dp[i] += dp[j]*2;    
            }
            
            dp[i] = dp[i]%1000000007;
        }
        
        return dp[n];
        
    }
}
```

모듈러 분배법칙은 잘 몰라서 사용하지 못했다. 이것도 이론 공부 해야겠다. 

