# 1. 문제 설명 📌

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/340211)

문제의 설명만 보면 이해하기가 어려운데, 밑의 시뮬레이터를 보면 문제에서 주어진 조건과 로봇의 작동원리는 이해할 만하다.
하지만 헷갈리는 것이 있다면 바로, `points`와 `routes`의 의미 및 관계라고 할 수 있겠다. 

## (1) 헷갈리는 것 바로 잡기

- `points`: 로봇이 방문해야하는 지점의 좌표를 배열 형태로 저장해놓았다.
  points[i]일 때, 이 `i`가 방문할 지점의 번호 이고, point[i]의 `value`가 각각 i란 지점의 행과 열 즉 좌표이다. 
- `routes`; 로봇별로 방문해야할 지점을 순서대로 나열한 1차원 배열의 묶음이다.
  routes[i]의 `i`는 로봇의 번호이고, routes[i]에는 로봇이 방문해야할 지점의 번호가 순서대로 저장되어 있다. 
  예를 들어, routes[2] = {3,4,1,2} 이면 2번 로봇은 지점을 3 ➜ 4 ➜ 1 ➜ 2 번 형태로 방문해야 하는 것이다. 

위의 내용을 그림으로 표현하면 다음과 같다. 
![image-20241017125942064](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[프로그래머스] Lv2 충돌 위험 찾기/image-20241017125942064.png)

다음과 같다면 1,2,3번 로봇은 어떻게 움직일까?
`1번 로봇`은 (2,2) ➜ (4,4)로, `2번 로봇`은 (1,3) ➜ (4,4)로, `3번 로봇`은  (2,5) ➜ (4,4) 로 움직인다. 이게 그림으로 보면 좀 나은데, 문제를 풀다보면 양쪽 다 배열로 이루어져 있어서, 코드 짤 때 헷갈리게 만든다. 

- 먼저 2차원 배열 형식이라, 좌표를 [0] [0], [0] [1] 이렇게 따로따로 불러 넣어야 하는 것
- `routes`에 보면 방문 지점 번호가 1부터 시작인데, `points` 는 0부터 시작하는 것 등이다. 

따라서 이렇게 바로 잡는다. 
(1) 좌표는 `Loc` 이란 객체로 묶어 놓는다. Loc은 Location의 약자로 클래스명으로 골랐고, 좌표로 쳤을 때, y값은 r, x값은 c에 넣는다. 
(2) `Points`는 `HashMap`으로 변모시킨다. 
그 이유는 원래라면 `points[routes[i] [j]] [0]`, `points[routes[i] [j]] [1]` 이런식으로 i번 로봇의 j번째 방문 지점의 좌표를 골라야 했는데, 가독성이 떨어져 실수하기가 쉽다. 따라서 `HashMap<Integer, Loc>`으로 두어서 지점 번호별 좌표를 저장해, `map.get(routes[i][j])` 하면 해당 좌표가 바로 나오게 하겠다.

# 2. 접근 방식 🗃️

`KEY WORD`: `SIMULATION`

2차원 배열 크기도 100 X 100 이 최대이고, 나머지 범위 값들도 100이 최대라서 단순 구현으로 풀어도 풀린다. (사실 처음에 3차원 배열로 푸는 방식도 단순 구현이었지만 시간초과가 나왔다... ㄷㄷ 이건 왜 안되었는지 뒤에 가서 설명하겠다.)

## (1) 논리 설명 (힌트만 볼 분은 여기까지만 읽기)

먼저 어떻게 접근할 것인지 대략적인 큰 그림을 설명하고, 그에 맞게 구현된 사항을 설명하려고 한다. 
**독자 중 힌트만 얻고 싶은 분은 이까지만 읽고 다시 문제를 풀러가기 바란다.**

앞에서 내가 3차원 배열로 풀어서 시간초과가 났다고 했는데, 내가 처음 생각한 방식은 다음과 같다.

- 모든 로봇 별로 자신의 움직임을 표현할 2차원 배열을 갖는다. 그러니까 3차원 배열의 깊이는 로봇의 개수이다.
- 로봇의 현재 방문 지점을 1, 나머지는 0으로 기록한다.
- 한 번의 움직임 이후에, 겹치는 부분을 체크한다. 만약 로봇 중 최소 두개가 (a,b) 지점에서 부딪혔다면,(a,b,0) ~ (a,b, routes.length)까지 보았을 때, 값이 1인 지점이 최소 2개 이상 나올 것이다. 

당연히 해당 방식은 움직임 한 번 마다 3차원 배열을 다 탐색해야 했음으로 아무리 범위가 작아도 시간초과가 났다. 

위의 시간초과가 주는 교훈은 `실시간으로 충돌여부를 탐색해서는 안된다.`이다. 이러면 매번 모든 부분에 대한 탐색이 필요해서 비효율적이다. 따라서 우리가 해줘야 할 것은 `로봇 별로 마지막 point 방문까지의 지나갔던 모든 좌표를 0초 ~ N초까지 순서대로 저장해두고 마지막에 몰아서 충돌여부 확인`으로 가야 한다. 그러니까 만약 우리가 물류 센터 직원이라면 충돌 날 때마다 로봇 관리 시스템에 기록하지 말고, 퇴근 전에 CCTV를 돌려보며 몰아서 충돌 횟수를 기록하는 것이다. (진짜 회사에서 이렇게하면 짤리겠지만 말이다.) 그럼 이제 어떻게 몰아서 확인할 것인지 알아보자 

## (2) 구현 설명

-  로봇 별로 Queue를 만들어서 매 초마다 움직인 좌표를 저장한다.
   (최단 경로라 하지만 쫄 필요 없다. 맨헤튼 거리인데다가, 도착지가 정해져 있다. 그리고 어디부터 우선해서 가라고도 알려준다. 이건 그냥 행으로 1, 열로 1씩 더하며 도착지까지 가는 계산을 직접 하라는 소리이다. (이렇게 모든 지점을 순회한다.))
-  모든 로봇의 움직임 저장이 끝나면, 1초마다 동시에 로봇 별 queue의 front 부분에서 값을 꺼내서 같은 좌표에 존재하는지 아닌지 여부를 검사한다. -> 같은 좌표가 존재하면 충돌이므로 충돌횟수를 1 올린다. 
   (같은 좌표가 존재하는지 확인이 어려울 수 있다. 이때는 `명부 작성`이 필요하다. <Loc, Integer> 즉 <좌표, 횟수> 형태의 map을 만들어서 좌표별로 로봇이 등장한 횟수를 세고 그것이 2 이상이면 충돌횟수를 올리면 된다.)

## (3) 필요한 사전 지식

- `HashMap이 KEY의 고유성을 보장하는 원리`: Key로 특정 클래스의 객체가 사용될 시, 멤버 변수가 동일해서 의미적으로 같은 객체는 HashMap이 동일한 Key로 간주하도록 hashCode()와 equals()를 재정의 해줘야 한다. 
- `ArrayDeque, HashMap 등 자료 구조 활용법`: 해당 문제는 80% 이상 자료구조를 이용해 문제를 풀고 있음으로 이해가 필요하다.

# 3. 코드 소개 🔎

먼저 전체 코드를 소개한 뒤에, 함수 별로 코드의 작동원리 및 의미를 알아보도록 하겠다. 

```java
import java.io.*;
import java.util.*;

class Solution {
    public int solution(int[][] points, int[][] routes) {
        // 0. initialize
        // (robotTraker = store the way robots move), (map = store point locations),
        // (longestDest = longest move among robot's move)
        ArrayDeque<Loc> [] robotTrackers = new ArrayDeque [routes.length];
        HashMap<Integer,Loc> dest = new HashMap<>();
        int longestDest = 0;
        for(int i = 0; i < points.length; i++){
            dest.put((i+1), new Loc(points[i][0], points[i][1]));
        }
        
        for(int i = 0; i < routes.length; i++){
            robotTrackers[i] = new ArrayDeque<Loc>();
            trackingLocation(dest, routes[i], robotTrackers[i]);
            longestDest = Math.max(longestDest, robotTrackers[i].size());
        }
        
        return countingCrush(robotTrackers, longestDest);

    }
    
    // store one robot's move from the start to the end
    public void  trackingLocation (HashMap<Integer, Loc> dest, int [] route, ArrayDeque<Loc> tracking) {    
        for(int i = 1; i<route.length; i++){
            Loc start = dest.get(route[i-1]);
             Loc next = dest.get(route[i]);

             int r = start.r;
             int c = start.c;

             if(tracking.isEmpty()){
                tracking.add(new Loc(r,c));
             }

             while(r != next.r){
                r = (r<next.r? r+1 : r-1);
                tracking.add(new Loc(r,c));
             }

             while(c != next.c){
                 c = (c<next.c? c+1 : c-1);
                 tracking.add(new Loc(r,c));
            }
        }  
    }
    
    public int countingCrush (ArrayDeque<Loc> [] robotTrackers, int longestDist) {
        int res = 0; 
        
        for(int i = 0; i < longestDist; i ++){
            // store crush cnt of each location 
            HashMap<Loc, Integer> map = new HashMap<>();
            int cnt = 0;
            for(int j = 0; j < robotTrackers.length; j++){
                if(robotTrackers[j].isEmpty()) continue;
                Loc now = robotTrackers[j].poll();
                map.compute(now, (key, oldvalue) -> (oldvalue == null? 1 : map.get(key)+1));
            }
            for(Integer v : map.values()){
                if(v >= 2) cnt++;
            }
            res += cnt;
        }
        return res;
    }
    
}

class Loc {
    int r;
    int c;

    public Loc(int r, int c) {
        this.r = r;
        this.c = c;
    }

    public Loc(int r, int c, int nextPoint){
        this.r = r;
        this.c = c;
    }
    
    // to make Loc instance equal to another Loc instance which have same materials
    @Override
    public boolean equals (Object obj) {
        if(this == obj) return true; 
        if(obj == null || this.getClass() != obj.getClass()) return false; 
        Loc o = (Loc) obj;
        return (this.r == o.r && this.c == o.c);
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(r,c);
    }
}
```

이제 하나씩 알아보자.

### (0) Loc 클래스

```java
class Loc {
    int r;
    int c;

    public Loc(int r, int c) {
        this.r = r;
        this.c = c;
    }

    public Loc(int r, int c, int nextPoint){
        this.r = r;
        this.c = c;
    }
    
    // to make Loc instance equal to another Loc instance which have same materials
    @Override
    public boolean equals (Object obj) {
        if(this == obj) return true; 
        if(obj == null || this.getClass() != obj.getClass()) return false; 
        Loc o = (Loc) obj;
        return (this.r == o.r && this.c == o.c);
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(r,c);
    }
}
```

로봇이 존재하는 좌표 혹은 포인트의 좌표를 저장해둔 클래스이다. 배열로만 문제를 푸는 것보다 훨씬 가독성이 높아 용이하다. 아까도 말했듯, 나중에 `좌표 별로 존재하는 로봇의 개수`를 세기 위해 Loc은 HashMap의 Key가 되어야 한다. 따라서 equals와 hashCode를 재정의 해줘야 한다. 

HashMap이 두 Key의 동등성 여부를 체크할 경우 다음 2가지 순서에 따라 확인한다.

1. 들어온 Key의 이름이 B라면, B가 저장된 HashBucket으로 일단 들어간다.
   동등한 객체들은 같은 HashBucket에 저장된다. 따라서 의미적으로 동등하여 동등성을 부여할 객체끼리는 같은 HashCode를 가지고 같은 Bucket안에 들어가 있어야함.
2. 이제 같은 Bucket내에서 B와 equals() 했을 때 true가 반환되는 녀석이 있다면 둘은 `동등`한 Key임으로 해당 값의 value가 갱신된다.

`equals()`: 

```java
    @Override
    public boolean equals (Object obj) {
        if(this == obj) return true; 	// 비교대상인 인수와 현재 대상이 아예 같은 값이다. -> 바로 true 반환
        if(obj == null || this.getClass() != obj.getClass()) return false;	// 비교대상 값이 null이거나 현재 값과 클래스가 다르다면 false 반환
        Loc o = (Loc) obj;		// 이제 같은 클래스인 것까지는 확인 되었으니, 내용물(멤버 변수)가 같은지 확인
        return (this.r == o.r && this.c == o.c);
    }
```

`hashCode()`: 

```java
    @Override
    public int hashCode() {
        return Objects.hash(r,c); // 멤버 변수를 활용해 해쉬코드를 만들기에, 멤버 변수가 같은 녀석들은 같은 코드를 가질 수 밖에 없음. 이를 이용
    }
```

만약 이러한 과정을 거치지 않으면, hashCode()는 객체의 메모리 주소를 가지고 만들어짐으로, 모든 객체가 다른 hashCode를 가졌을테고, 그렇다면 동등성 비교 자체가 안된다. hashCode()를 재정의 하더라도, equals()가 재정의 되어 있지 않다면, 멤버 변수가 같아 의미적으로 같은 객체를 알아채지 못하고, 메모리 주소로 동등성을 체크함으로, 의미적으로 같은 Key를 넣더라도 value가 갱신되는 것이 아니라 새로운 Entry가 계속 생겨난다. 

### (1) solution method 

전체를 다루는 함수이다. 

```java
    public int solution(int[][] points, int[][] routes) {
        // 0. initialize
        // (robotTraker = store the way robots move), (map = store point locations),
        // (longestDest = longest move among robot's move)
        ArrayDeque<Loc> [] robotTrackers = new ArrayDeque [routes.length];
        HashMap<Integer,Loc> dest = new HashMap<>();
        int longestDest = 0;
        for(int i = 0; i < points.length; i++){
            dest.put((i+1), new Loc(points[i][0], points[i][1]));
        }
        
        for(int i = 0; i < routes.length; i++){
            robotTrackers[i] = new ArrayDeque<Loc>();
            trackingLocation(dest, routes[i], robotTrackers[i]);
            longestDest = Math.max(longestDest, robotTrackers[i].size());
        }
        
        return countingCrush(robotTrackers, longestDest);
    }
```

`ArrayDeque<Loc> [] robotTracker`: 위에서도 말했듯이 Loc은 좌표를 가지고 있는 객체이다. robotTracker[i]는 i번째 로봇이 움직인 경로를 Loc 형태로 저장하고 있다. ArrayDeque는 Queue의 일종으로 선입선출 구조이다. 따라서 맨 처음 이동한 위치부터 차례대로 ArrayDeque의 앞부분에 저장되어서, 나중에 시간 순으로 충돌여부를 따져볼 때 용이하다. 

`HashMap<Integer, Loc> dest`: dest는 포인트 번호 별로 좌표를 저장하고 있는 HashMap이다. 

`solution 함수의 원리`: 

1. 위에 설명한 두 자료구조 초기화 
2. `trackingLocation`: 함수 활용하여 로봇 별로 움직인 행적을 오래된 순으로 저장 
3. `countingCrush`: 2번에서 저장한 행적을 하나씩 빼보면서 충돌 횟수 세기

### (2) Tracking Location

```java
    // store one robot's move from the start to the end
    public void  trackingLocation (HashMap<Integer, Loc> dest, int [] route, ArrayDeque<Loc> tracking) {    
        for(int i = 1; i<route.length; i++){
            Loc start = dest.get(route[i-1]);
             Loc next = dest.get(route[i]);

             int r = start.r;
             int c = start.c;

             if(tracking.isEmpty()){
                tracking.add(new Loc(r,c));
             }

             while(r != next.r){
                r = (r<next.r? r+1 : r-1);
                tracking.add(new Loc(r,c));
             }

             while(c != next.c){
                 c = (c<next.c? c+1 : c-1);
                 tracking.add(new Loc(r,c));
            }
        }  
    }
```

특별히 어려운 원리는 없다. 시작 point에서 그 다음 point까지 행 우선 이동을 한다. 한칸 움직일 때마다 tracking이란 RobotTracker 배열 중 하나의 값에 움직임을 저장한다. 한 point에 도착하면 이제 그 포인트를 시작점으로 다음 포인트까지 움직임을 다시 기록한다. 

### (3) countingCrush()

```java
    public int countingCrush (ArrayDeque<Loc> [] robotTrackers, int longestDist) {
        int res = 0; 
        
        for(int i = 0; i < longestDist; i ++){
            // store crush cnt of each location 
            HashMap<Loc, Integer> map = new HashMap<>();
            int cnt = 0;
            for(int j = 0; j < robotTrackers.length; j++){
                if(robotTrackers[j].isEmpty()) continue;
                Loc now = robotTrackers[j].poll();
                map.compute(now, (key, oldvalue) -> (oldvalue == null? 1 : map.get(key)+1));
            }
            for(Integer v : map.values()){
                if(v >= 2) cnt++;
            }
            res += cnt;
        }
        return res;
    }
```

`longestDist`: 로봇마다 움직인 거리는 차이가 날 수 있다. 반복문에 움직임이 누락되는 것을 막기 위해 가장 길었던 움직임을 미리 기록해두고 이를 이용해 반복문을 진행한다. 

`HashMap<Loc, Integer> map`:  한번의 움직임 이후 좌표마다 로봇의 개수를 세기 위한 HashMap이다. 로봇이 현재 위치한 자리만 후보군으로 두고 거기서 충돌 여부를 계산하면 되므로, 내가 처음 선보인 3차원 배열 전체 탐색보다 훨씬 효율적임은 두 말하기엔 입이 아프다. 
매 초 움직임 마다 map이 비어져 있어야 함으로, 반복문 안 쪽에서 선언한 것을 잊지 않기를 바란다. 

# 4. 배운 것들 🎯

`HashMap의 동등성 보장` : 객체를 Key로 쓸 수 있다는 것도 처음 알았고, 이를 이용해 HashMap을 쓰려면, equals와 hashCode()를 재정의 해줘야 한다는 것을 배웠다. 시야가 넓어지는 기분이었고, 다음에 이를 활용해서 문제를 풀어야 겠다.