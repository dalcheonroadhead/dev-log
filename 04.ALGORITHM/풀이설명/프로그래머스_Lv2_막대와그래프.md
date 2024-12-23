# 1. 문제 설명 📌

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/258711)

문제에서는 다음 3가지 단방향 그래프를 제시하고 있다. 
![image-20241024230707381](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/프로그래머스_Lv2_막대와그래프/image-20241024230707381.png)

**도넛형**은 순환형 그래프이고, 간선의 개수와 정점의 개수가 같다.
**막대형**은 비순환형 그래프이고, `정점의 개수` - `간선의 개수` = 1 이다. 
**팔자형**은 순환형 그래프이고, `간선의 개수` - `정점의 개수`= 1 이다. 

이러한 3가지 유형에 해당하는 그래프가 최소 2개 이상 주어진다. 이때 주어진 모든 그래프를 잇는 정점을 하나 그린다. 우리는 이번 문제 풀이에서 해당 정점을 `뿌리 정점`이라 부르겠다. (문제에서 마땅히 해당 정점을 지칭하는 용어가 없어서 임의로 명명하겠다.) 뿌리 정점을 부착한 예시는 다음과 같다.
![image-20241024231210352](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/프로그래머스_Lv2_막대와그래프/image-20241024231210352.png)

이때 `뿌리정점의 번호`, `도넛형 그래프의 수`, `막대 그래프의 수`, `팔자형 그래프의 수` 를 순서대로 기록한 1차원 배열을 출력하면 된다.

# 2. 접근 방식 🗃️

`KEY WORD`: `Simulation`

 필자는 해당 문제를 2시간 넘게 고민하다가 결국 답지를 봤다. 답지 첫 문장을 읽자, 아이디어가 이해가 갔다. 나는 단순히 그래프라고 해서 BFS, DFS 등 그래프 알고리즘을 활용해서 문제를 풀어야한다는 생각에 매몰되어 있었다. 그래서 그래프의 특징 파악에 소홀했던 것이 문제를 풀지 못한 이유인 것 같다. 

 자, 잡설은 각설하고 접근 방식은 다음과 같다.

- `뿌리 정점`이 연결됨으로 인해, 그래프 고유의 특징이 사라져버림으로, 뿌리 정점을 식별해 제거한다.
- 이제 모두 `온전히 분리된` 각 그래프를 확인하며 어떤 그래프 유형인지 확인하고 개수를 센다.

## (1) 뿌리 정점은 어떻게 식별할까? 

 뿌리 정점 자체가 타 그래프의 어느 정점과도 연결될 수 있어서, 그래프 간의 연결 관게로 식별하기는 불가능하다. 따라서 `진입 차수`와 `진출 차수`를 활용한다. ( `진입 차수`란 방향 그래프에서 해당 정점으로 들어오는 간선의 개수를 의미하고, `진출 차수`란 해당 정점에서 나가는 간선의 개수이다. )
 자세히 보면, `뿌리 정점`은 문제에서 주어진 그래프의 개수만큼의 진출 차수를 가지고 있고, 진입 차수는 없다! 당연하게도 모든 그래프를 붙잡아서 연결하고 있기에, 진입 차수가 있을 필요가 없다. 

따라서 뿌리 정점은 `진입 차수가 없고, 진출 차수가 2개 이상`인 정점이다. 
### 음? 일반 정점과 해당 특징은 안 겹치나요? 🤔💭

안 겹친다. 순환 그래프는 당연히 진출차수가 있으면 진입차수가 필요하다 따라서 도넛형과 팔자형 내부 정점들은 뿌리정점과 특징이 안 겹친다. 막대 그래프는 2가지를 유의해서 봐야하는데, 첫째로 막대 그래프의 시작부분이다. 분명 진입 차수는 없지만, 진출 차수가 1개로 고정이다. 따라서 특징이 안 겹친다. 또한 맨 마지막 정점은 진입 차수가 1개로 고정이고, 진출 차수는 없다. 따라서 문제 내에 `뿌리 정점`과 해당 특징이 겹치는 정점은 존재하지 않는다. 

## (2) 그래프들은 어떻게 구분할까? 

뿌리 정점을 들어냈음으로, 그래프들은 모두 독립적으로 존재한다. 여기서 문제에서 주어진 대로, `정점`과 `간선`의 관계를 활용해도 좋겠지만, 이렇게 풀려면, BFS로 모든 그래프를 조회해야 한다는 번거로움이 있다. 해당 번거로움을 줄이고 효율적으로 문제를 풀기 위해 이번에도 `진입 차수`와 `진출 차수`를 활용한다.

- `막대형`: 막대형이 다른 두 그래프와 대비되는 차이점은 막대의 끝 정점이다. 나머지 2개는 순환형 그래프라 정점마다 무조건 진입 차수와 진출 차수가 1개씩은 존재해야 하지만, 막대형의 맨 마지막 부분은 진출 차수가 존재하지 않는다. 따라서 **정점 조회 시, 진출 차수가 없는 정점의 수**를 세면 그것이 막대형 그래프의 수이다. 
- `팔자형`: 팔자형은 **8자의 허리가 되는 부분이 무조건 진입 차수 2개와 진출 차수 2개**를 가진다. 이 특징을 가진 정점의 개수를 세면 그것이 팔자형 그래프의 수이다.
- `도넛형`: 도넛형은 **모든 정점이 무조건 하나의 진입 차수와 진출 차수**를 가진다. 근데 이 특징을 가진 정점은 막대형 그래프의 몸통과 팔자형 그래프에도 수없이 존재한다. 따라서 도넛형 그래프의 개수는 `전체` - `나머지 두 그래프 개수의 합`으로 센다.

# 3. 코드 소개 🔎

먼저 전체 코드를 보여준 뒤, 하나씩 분석해보겠다. 

## (1) 전체 코드

```java
import java.io.*;
import java.util.*;

class Solution {
    
    static ArrayList<Edge> [] lists;
    public int[] solution(int[][] edges) {
        
        // 0. initialize
        int [] answer = new int[4];
        lists = new ArrayList [findMaxVertex(edges)+1];
        for(int i = 0; i < edges.length; i++){
            int start   = edges[i][0];
            int end     = edges[i][1];
            
            if(lists[start] == null) {lists[start] = new ArrayList<>();}
            lists[start].add(new Edge(end, true));
            if(lists[end] == null) {lists[end] = new ArrayList<>();}
            lists[end].add(new Edge(start, false));
        }
        
        // 연결 정점을 찾아서 인접 리스트에서 지우기
        for(int i = 1; i < lists.length; i++){
            if(lists[i] == null) continue;
            int out = 0; int in = 0; 
            for(int j = 0; j < lists[i].size(); j++){
                Edge next = lists[i].get(j);
                if(next.isAdvance) out++;
                else in++;
            }
            if(in == 0 && out >=2) {
                answer[0] = i;
                break;
            }
        }

        
        // 연결 정점의 원소에 가서 진출한 정점을 알아냄. 
        // 해당 정점 원소에서 진입차수로 들어온, 연결 정점 제거
        answer[1] = lists[answer[0]].size();
        for(int i = 0; i < lists[answer[0]].size(); i++){
            Edge next = lists[answer[0]].get(i);
            for(int j = 0; j < lists[next.dest].size(); j++){
                if(lists[next.dest].get(j).dest == answer[0]) {
                    lists[next.dest].remove(lists[next.dest].get(j));
                    break;
                }
            }
        }
        lists[answer[0]].clear();
        
        // 막대(진입 1, 진출 x) 랑 8자 (진입 2, 진출 2) 찾기
        for(int i = 1; i < lists.length; i++){
            if(i == answer[0] || lists[i] == null) continue;
            
            int in = 0; int out = 0;
            for(int j = 0; j < lists[i].size(); j++){
                Edge next = lists[i].get(j);
                if(next.isAdvance) out++;
                else in++;
            }
            // System.out.printf("현재 정점 번호: %d / 진입차수: %d, 진출 차수: %d\n", i, in, out);
            if( out == 0) answer[2]++;
            if(in == 2 && out == 2) answer[3]++;
        }
        
        answer[1] -= (answer[2]+answer[3]);
        
        return answer;
    }
    
    public int findMaxVertex (int [][] edges){
        int ans = 0;
        for(int i = 0; i < edges.length; i++){
            ans = Math.max(ans, edges[i][0]);
            ans = Math.max(ans, edges[i][1]);
        }
        
        return ans;
    }
}

class Edge {
    int dest;
    boolean isAdvance;
    
    public Edge(int dest, boolean isAdvance){
        this.dest = dest;
        this.isAdvance = isAdvance;
    }
}
```

## 1. Class Edge

```java
class Edge {
    int dest;
    boolean isAdvance;
    
    public Edge(int dest, boolean isAdvance){
        this.dest = dest;
        this.isAdvance = isAdvance;
    }
}
```

원래 인접리스트는 해당 정점에서 진출 하는 간선과 도착 정점만 표현하면 되지만, 문제를 위해 변형했다. 나는 `진입 간선과 진출 간선을 구분하여 저장하는 Class`로서 Edge를 구현했다. 
 먼저 dest는 정점 번호이다. `isAdvance == true`라면 현 정점 ➜ dest로 진출하는 간선이다. `isAdvance == false`라면, dest ➜ 현 정점으로 진입하는 간선이다. 따라서 초기화 할 때 다음과 같이 된다.

## 2. 초기화

```java
    // 0. initialize
    int [] answer = new int[4];
    lists = new ArrayList [findMaxVertex(edges)+1];
    for(int i = 0; i < edges.length; i++){
        int start   = edges[i][0];
        int end     = edges[i][1];
        
        if(lists[start] == null) {lists[start] = new ArrayList<>();}
        lists[start].add(new Edge(end, true));
        if(lists[end] == null) {lists[end] = new ArrayList<>();}
        lists[end].add(new Edge(start, false));
    }
```

edges에서는 진출간선만 제공하므로 진입 간선도 따로 표현한다. 

## 3. `뿌리 정점` 지우기

```java
        // 연결 정점을 찾아서 인접 리스트에서 지우기
        for(int i = 1; i < lists.length; i++){
            if(lists[i] == null) continue;
            int out = 0; int in = 0; 
            for(int j = 0; j < lists[i].size(); j++){
                Edge next = lists[i].get(j);
                if(next.isAdvance) out++;
                else in++;
            }
            if(in == 0 && out >=2) {
                answer[0] = i;
                break;
            }
        }

        
        // 연결 정점의 원소에 가서 진출한 정점을 알아냄. 
        // 해당 정점 원소에서 진입차수로 들어온, 연결 정점 제거
        answer[1] = lists[answer[0]].size();
        for(int i = 0; i < lists[answer[0]].size(); i++){
            Edge next = lists[answer[0]].get(i);
            for(int j = 0; j < lists[next.dest].size(); j++){
                if(lists[next.dest].get(j).dest == answer[0]) {
                    lists[next.dest].remove(lists[next.dest].get(j));
                    break;
                }
            }
        }
        lists[answer[0]].clear();
```

접근 방식에서 말한대로, 진출 차수는 2개 이상인데 진입 차수가 없는 경우의 정점을 먼저 찾는다.  그 후, 인접 리스트에서 해당 정점의 진출 간선을 모두 지운다. 흔적을 모두 지우려면, 2가지를 신경써야 한다. 

- 뿌리 정점 자체 없애기
- 뿌리 정점에서의 진입 간선을 저장하고 있는 인접리스트 배열의 원소 속에서 해당 정점 지우기 

## 4. 그래프 유형별 개수 구하기

```java
        // 막대(진입 1, 진출 x) 랑 8자 (진입 2, 진출 2) 찾기
        for(int i = 1; i < lists.length; i++){
            if(i == answer[0] || lists[i] == null) continue;
            
            int in = 0; int out = 0;
            for(int j = 0; j < lists[i].size(); j++){
                Edge next = lists[i].get(j);
                if(next.isAdvance) out++;
                else in++;
            }
            // System.out.printf("현재 정점 번호: %d / 진입차수: %d, 진출 차수: %d\n", i, in, out);
            if( out == 0) answer[2]++;
            if(in == 2 && out == 2) answer[3]++;
        }
        
        answer[1] -= (answer[2]+answer[3]);
```

전체 그래프의 개수는 당연히 뿌리 정점에서의 진출 차수와 같다. 그를 이용한다.

# 4. 배운 것들 🎯

##  35번 테스트 케이스가 자꾸 런타임 에러가 났던 이유📝 

우리가 항상 간과하고 지나가서 못 본 경우가 많지만, 보통 그래프 문제에서는 정점 번호를 1~N까지 라고 명확히 주어준다. 하지만 해당 문제에서는 그러지 않았다. 따라서 정점 번호가 1~N까지 연속되지 않고, 1,3,5,10 등 뛰엄뛰엄 있을 수 있다. 그런데, 이를 상정하지 않고, 문제에서 주어준 적 없는 정점번호까지 계산하려고 하면 애초부터 주어진 데이터가 없어 초기화 하지 않았으므로 NullPointer Exception이 뜰 수 밖에 없다.