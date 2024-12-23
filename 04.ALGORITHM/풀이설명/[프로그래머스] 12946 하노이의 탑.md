# 1. 문제 설명 

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/12946)

하노이 탑(Tower of Hanoi)은 퍼즐의 일종입니다. 세 개의 기둥과 이 기동에 꽂을 수 있는 크기가 다양한 원판들이 있고, 퍼즐을 시작하기 전에는 한 기둥에 원판들이 작은 것이 위에 있도록 순서대로 쌓여 있습니다. 게임의 목적은 다음 두 가지 조건을 만족시키면서, 한 기둥에 꽂힌 원판들을 그 순서 그대로 다른 기둥으로 옮겨서 다시 쌓는 것입니다.

1. 한 번에 하나의 원판만 옮길 수 있습니다.
2. 큰 원판이 작은 원판 위에 있어서는 안됩니다.

하노이 탑의 세 개의 기둥을 왼쪽 부터 1번, 2번, 3번이라고 하겠습니다. 1번에는 n개의 원판이 있고 이 n개의 원판을 3번 원판으로 최소 횟수로 옮기려고 합니다.

1번 기둥에 있는 원판의 개수 n이 매개변수로 주어질 때, n개의 원판을 3번 원판으로 최소로 옮기는 방법을 return하는 solution를 완성해주세요.

##### 제한사항



- n은 15이하의 자연수 입니다.

------

##### 입출력 예



| n    | result                  |
| ---- | ----------------------- |
| 2    | [ [1,2], [1,3], [2,3] ] |

##### 입출력 예 설명

입출력 예 #1
다음과 같이 옮길 수 있습니다.

[![Imgur](https://camo.githubusercontent.com/eb878551e069bfbceab45a7901906158bfd39d2440c84abf681aaa36ee63a70b/68747470733a2f2f692e696d6775722e636f6d2f535745714430382e706e67)](https://camo.githubusercontent.com/eb878551e069bfbceab45a7901906158bfd39d2440c84abf681aaa36ee63a70b/68747470733a2f2f692e696d6775722e636f6d2f535745714430382e706e67)
[![Imgur](https://camo.githubusercontent.com/fc39d8e166ef494c460ed5b982e568de95b738418ca427d39a8a5d3af345a22e/68747470733a2f2f692e696d6775722e636f6d2f6d726d4f7a56322e706e67)](https://camo.githubusercontent.com/fc39d8e166ef494c460ed5b982e568de95b738418ca427d39a8a5d3af345a22e/68747470733a2f2f692e696d6775722e636f6d2f6d726d4f7a56322e706e67)
[![Imgur](https://camo.githubusercontent.com/9a1677f4ba8db5d473d87eaa30bab472fa570f30cb46c4e8ac565e1919290ab9/68747470733a2f2f692e696d6775722e636f6d2f456e74383367412e706e67)](https://camo.githubusercontent.com/9a1677f4ba8db5d473d87eaa30bab472fa570f30cb46c4e8ac565e1919290ab9/68747470733a2f2f692e696d6775722e636f6d2f456e74383367412e706e67)
[![Imgur](https://camo.githubusercontent.com/e9fb20c066e120d860b07b6f79f5d6f5db39a3188ab97723d8c7aa7935dc03a2/68747470733a2f2f692e696d6775722e636f6d2f6f734a466668462e706e67)](https://camo.githubusercontent.com/e9fb20c066e120d860b07b6f79f5d6f5db39a3188ab97723d8c7aa7935dc03a2/68747470733a2f2f692e696d6775722e636f6d2f6f734a466668462e706e67)

> 출처: 프로그래머스 코딩 테스트 연습, https://school.programmers.co.kr/learn/challenges

# 2. 풀이 설명

#### keyword

: 재귀 

풀이법은 다음과 같다. 
편의를 위해서 현재 블록이 있는 위치를 `시작점`, 블록이 가야할 위치를 `목적지` 그 외의 장소를 `중간 지점`이라고 하겠다. 

ⓐ 최저 블록을 제외한 모든 블록을 중간 지점으로 옮긴다. (Top-down 재귀 이용)

ⓑ 최저 블록을 목적지로 옮긴다. (배열에 log 저장)

ⓒ 나머지 블록을 모두 목적지로 옮긴다. (Top-down 재귀 이용)

실질적으로 작동하는 코드는 ⓑ 밖에 없다. 나머지는 계속 재귀이다.
따라서 목적지에 최저 블록을 옮기는 저 ⓑ로 모든 이동 작업이 순서대로 찍혀야 하는 것이다. 따라서 중요한 점은 저 목적지를 재귀가 깊어짐에 따라 다 다르게 설정 해줘야 한다는 것이다.   수도 코드로 작성 해보면,

```java
// 모든 블럭을 하노이탑 규칙에 의거해서 시작점에서 목적지로 옮기는 함수
public void hanoi(int 시작점, int 중간 지점, int 끝점, int 남은 블럭의 수) {
    
    // 기저조건
    if (움직여야할 블럭이 남지 않았다면,) {
       그냥 return 해서 원래의 재귀로 되돌아 간다.    
    }
    
    // 계산하는 곳
    
    // 1. 최저 블록 제외한 모든 블록을 중간지점으로 옮긴다. 
    //	  최저 블록을 제외한 모든 블록들은 중간지점으로 가야하기 때문에 중간 지점이 목적지가 되고, 
    //    끝점이 중간 지점이 된다.
    hanoi(시작점, 끝점, 중간지점, 남은 블럭의 수 - 1);
    
    // 2. 최저 블록 옮기기
    최저 블록을 옮기는 코드: 로그 찍기 혹은 배열에 값 저장 
        
    // 3. 중간지점으로 옮긴 나머지 블록들을 끝 점의 최저 블록 위로 모두 옮긴다. 
    hanoi(중간지점, 시작점, 끝점, 남은 블럭의 수 - 1);
        
}
```

# 3. 코드 분석

```java
import java.io.*;
import java.util.*;

class Solution {
    
    static ArrayList<int[]> list = new ArrayList<>();
    
    public ArrayList<int[]> solution(int n) {
        
        hanoi(1,2,3,n);
        
        return list;
    }
    
    public void hanoi(int start, int mid, int end, int stack) {
        
        if(stack == 0){
            return;
        }
        hanoi(start, end, mid, stack-1);    // 최저 블록 제외 전부 start -> mid로 이동
        list.add(new int[]{start,end});     // 최저 블록 start -> end 이동 
        hanoi(mid,start,end,stack-1);       // 최저 블록 제외 전부 mid -> end 이동 
    }
    
}
```

#### 내 코드의 아쉬운 점

문제를 풀 때, 반환형을 int[][] [ ] [ ] 에서 ArrayList<int [ ]> 로 바꾸었다. 연유는 배열의 크기를 명확하게 구하지 못했기 때문이다. 이는 다른 사람의 풀이를 통해 공부해서 보완 했다. 

# 4. 좋아요 많이 받은 다른 사람의 풀이 

```java
class Solution {
    private int index = 0;
        public int[][] solution(int n) {
            int[][] answer = new int[(int) (Math.pow(2,n)-1)][2];

            move(n, 1, 3, 2, answer);
            return answer;
        }

        public void move(int n, int start, int end, int between, int[][] answer) {
            if (n == 1) {
                answer[index++] = new int[]{start, end};
                return;
            }
            move(n - 1, start, between, end, answer);
            answer[index++] = new int[]{start, end};
            move(n - 1, between, end, start, answer);
        }
}
```

배열의 크기는 [ n^2 - 1 ] [ 2 ]  이다. 재귀가 깊어질수록 계산 하여야 할 값이 2배가 되기 떄문에 행은 2^n -1 의 크기이다. (root는 계산을 한번만 하면 되서 1 빼준다.) 열은 start와 end 두 개가 적혀야 하므로 크기가 2이다. 

또한 여기서는 기저 조건을 n == 1로 해주었는데, 내꺼는 n == 0으로 해서 불 필요한 재귀를 2^n번 더 했다. 이 부분을 바꾸면 시간적 성능이 개선될 거 같다.  

