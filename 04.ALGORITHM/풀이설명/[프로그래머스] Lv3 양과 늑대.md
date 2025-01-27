# 1. 문제 설명📌

## (1) 링크🔗

https://school.programmers.co.kr/learn/courses/30/lessons/92343

## (2) 주목 포인트 🕵

- 1️⃣ `늑대` >= `양` 이면 모았던 양의 개수가 **0**이 된다! 
- 2️⃣ 현재 특정한 서브 트리를 방문 중이라 가정할 때, **해당 트리에서 최대 이익을 이미 냈다고 확신한다면, 조상 노드를 거슬러 올라가 다른 서브 트리를 파고 드는 것이 가능하다.**

# 2. 생각의 흐름: 코드가 나오기까지 🗃️

## (1) IDEA 도출💡

**`KEY WORD`: `BACK-TRACKING`, `DFS`**

해설에서 설명한 2️⃣번째 포인트 때문에, 이번 문제는 **`BACK-TRACKING`에 가깝게 변형된 `DFS`**를 사용해야 한다. 깊이 우선 탐색을 하는 성질은 같으나, **현 노드의 `삼촌 노드` 혹은 `조카 노드`도 다음 탐색 후보지에 넣어야 한다.**

 입출력 에제 #2를 예로 들어보겠다.

![image-20250122224801154](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[프로그래머스] Lv3 양과 늑대/image-20250122224801154.png)

현재 `5번 노드`를 방문 중이라 해보자. 이제 다음에 갈 수 있는 후보지를 생각해보면, 

![image-20250122224912896](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[프로그래머스] Lv3 양과 늑대/image-20250122224912896.png)

**`삼촌 노드`**인 1번 노드와 `형제 노드`인 6번 노드이다. 다음 DFS 재귀를 1번 노드로 타는 것은, *'양의 개수만 늘릴 수만 있다면, 조상 노드를 거슬러 올라가 다른 서브 트리를 방문할 수 있다.'*는 **문제의 조건을 구현하는 행위**이다. 

![image-20250122225209496](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[프로그래머스] Lv3 양과 늑대/image-20250122225209496.png)

따라서 DFS 재귀를 탈 때, **부모 노드가 가졌던 후보지를 자식노드에게 그대로 대물림 해줘야 한다!**

### 실수하기 좋은 포인트🚨

**`Call by Value`, `Call by address`**

**`ArrayList`**는  하나의 객체이다. 이러한 객체를 매소드의 매개변수로 넣으면, **주소값이 복사되어 들어가서, 계산 결과가 함수가 끝난 뒤에도 유지된다.** 따라서 부모 노드의 후보지를 그대로 받아 깊이 탐색에 사용한다면, 깊이 탐색을 한 번 할 때마다, 계산 결과가 후보지에 영향을 줄 것이다. 이는 필히 오차를 발생시킨다. 따라서 **모든 깊이에서 후보지 List를 새로 만든다!** 여기에 값을 넣어서 사용해서, **후보지 계산은 해당 재귀에서만 유효하도록 만들어야 한다.** 

## (2) SUDO CODE 📜

- 0️⃣ 인접리스트 형태로 트리 구현
- 1️⃣ 재귀 함수 만들기
  - a. 현재 방문한 노드에 양이 있는지, 늑대가 있는지 확인 및 그 개수 누적 
    - `양` <= `늑대` 이면 해당 재귀 바로 탈출
    - `양` > `늑대` 이면 누적된 양의 개수를 현재까지의 양의 최대값(ans)과 비교 후 ans 갱신 
  - b.  현 노드의 자식 노드를 방문 후보지 List에 넣는다.
  - c. 방문 후보지는 부모에게서 받은 것이므로, 현 노드도 아직 그 중에 포함되어 있다. 현 노드의 양, 늑대 계산은 끝났음으로 리스트에서 제거해준다.
  - d. 후보지를 하나씩 방문한다. (**이때, 방문 후보지를 같이 넣어줘야 하는데, 그대로 넣지말고 ArrayList를 새로 만들어서 거기 원래의 값을 채운 후 넣는다! (`call by address` 주의)**)
- 2️⃣재귀 함수로 구한 ans를 반환한다.

## (3) 시간복잡도 분석 🕓

노드 개수가 최대 `17`이다. 아무리 백트래킹을 한다지만, 1억번을 절대 넘길 수 없다. 따라서 시간 복잡도를 굳이 안 따져도 되는 문제 이다.

# 3. 구현 코드 🔎

```java
import java.util.*;

class Solution {
    static int ans = 0;
    static ArrayList<Integer> [] nodes = new ArrayList [17];
    static int [] INFO;
    public int solution(int[] info, int[][] edges) {
        INFO = info;
        for(int i = 0; i < 17; i++){
            nodes[i] = new ArrayList<>();
        }
        for(int i = 0; i < edges.length; i++){
            int parent = edges[i][0];
            int child = edges[i][1];
            nodes[parent].add(child);
        }
        ArrayList<Integer> non_visited = new ArrayList<Integer>();
        non_visited.add(0);
        recur(0, non_visited , 0, 0);
        return ans;
    }
    
    public void recur(int now, ArrayList<Integer> non_visited, int lamps, int wolfs){
        if(INFO[now] == 0) lamps++;
        else wolfs++;
        if(lamps <= wolfs) return;
        // (0) 양 최대값 저장
        ans = Math.max(lamps, ans);
        // (1) 자식 노드 미방문 정점에 넣기
        non_visited.addAll(nodes[now]);
        // (2) 미방문 정점에서 자기 자신을 빼기 
        non_visited.remove(Integer.valueOf(now));
        // (3) non_visited에 해당하는 정점 하나씩 방문
        for(int i = 0; i < non_visited.size(); i++){
            int next = non_visited.get(i);
            ArrayList<Integer> next_nodes = new ArrayList<>(non_visited);
            recur(next,next_nodes, lamps, wolfs);
        }
    }
}
```

# 4. 배운 것들 🎯

## (1) list.remove(i) vs list.remove(Integer.valueOf(i))

**ArrayList의 remove() 함수는** 2가지 형태로 오버로딩 되어 있다.

- 1️⃣ `list.remove(i)`: 원시 자료형 int를 넣으면, **이를 `index`로 인식해서 해당 index의 값을 지운다.** 
- 2️⃣`list.remove(Object o)`: 객체를 넣으면, 그와 같은 `value`를 삭제한다.

### 문제에서의 적용점✨ 

해당 문제의 `방문할 노드 후보지`는 `ArrayList<Integer>` 형태로 되어 있다. 여기서 i라는 노드를 지우고 싶다고, `list.remove(i)`를 하면, 원하는 값이 아닌 index가 i인 값이 엉뚱하게 지워질 것이다. 이를 방지하기 위해, **int 자료형을 Wrapper Class인 Integer의 객체로 변환하는 작업**을 가졌다. 이제 해당 노드 번호는 객체가 되었음으로, 위에 설명된 2️⃣의 remove가 자연스럽게 호출된다.

**`list.remove(Integer.valueOf(i))`**



>  이모지 모음: 🤔, ➜ ✨ 0️⃣1️⃣2️⃣3️⃣4️⃣5️⃣6️⃣7️⃣8️⃣9️⃣🔟

