# 1. 개념

그래프가 주어졌을 때, `시작 노드`를 하나 정해서, 그 시작노드부터 `연결된 모든 노드로 가는 최단경로`를 `한 번에` 구하는 알고리즘이다.

# 2. 작동 원리

명절 연휴 귀성길에서 아버지와 삼촌의 통화를 들어본 적이 있는가? 
`아버지`: "여기서 엄마 집 가려면 고속도로 타고 바로 가는 게 낫지 않냐?"
`삼촌`: "ㄴㄴ □□ IC 가 새로 생겨서 거기 찍고 엄마 집으로 가는 게 나아"

원래 가던 길이 있었는데, 삼촌의 새로운 정보를 통해 □□IC를 거쳐서 가는 것이 최단 거리로 바뀌었다. 이건 `다익스트라`이다. 
이처럼 A➜B로 가는데 원래 가던 길보다 정점 C를 찍고 가는 게 빠르다면 그것으로 갱신해 나가며, 이렇게 모든 정점을 고려해 나가면 된다.

## (0) 사전 세팅

1. `시작 노드`를 정한다.
2. `최단 거리 배열`과 `방문 확인 배열`을 미리 만들어 놓는다.

#### a. 최단 거리 배열

`index`는 `그래프 내의 모든 노드의 번호`를 가리킨다. `value`는 `시작 노드에서 해당 노드로 가는 경로 비용`을 나타낸다. 다익스트라 알고리즘을 진행할수록 모든 배열의 value는 시작 노드에서 해당 노드로 가는 최단 비용으로 갱신된다. 
**초기화는?**
최단 거리 배열은 모두 `inf(∞)` 로 초기화 시킨다. 최소값으로 갱신해나갈 것임으로, 초기 값이 0이면 최단 경로를 갱신할 수 없기 때문이다. java로 문제를 풀 경우, 상황 별로 적절하게, `Integer.MAX_VALUE` 등을 이용한다. 

#### b. 방문 배열 

다익스트라 알고리즘은 지금까지 방문하지 않은 정점 중 최단 거리로 갈 수 있는 노드를 계속 방문하며 최단거리를 갱신하는식으로 작동한다. 이를 위해서 방문 노드와 미방문 노드를 구분해줄 배열이 필요 하다. 

## (1) 작동 

### a. 시작 노드에서 시작한다. (방문 처리 및 거리 배열의 시작 노드 값은 0으로)

### b. 현 노드에서 직접적으로 연결된 노드들의 간선을 이용해 최단 거리 배열을 갱신한다.

갱신 기준은 다음과 같다. 거리배열을 `dist`, 현재 방문 중인 노드가 `A`, A와 연결되고 거리 배열 갱신할 노드가 `B`라고 할 때, 
`dist[B]` = `Math.min(dist[B], dist[A] + (A -> B 간선 비용))`

### c. 최단 거리 배열 갱신을 끝낸 후, 아직 방문하지 않은 노드들 중에서 `시작노드에서부터` 최단거리로 갈 수 있는 노드를 방문한다. 

### d. `b`와 `c`를 도착노드 방문 시까지 반복한다.

# 3. 예시

![image-20241009151614095](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009151614095.png)

다음과 같은 그래프에서 다익스트라 알고리즘을 쓴다고 가정해보자. 시작 노드는 1이고, 우리가 값을 얻고 싶은 목표 노드는 6이다. 

![image-20241009151830596](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009151830596.png)

![image-20241009151848850](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009151848850.png)

시작 노드에 대해서 거리 방문 처리 및 거리 배열의 값을 0으로 세팅한다. (예시에서는 노란색으로 색칠을 방문으로 간주한다.)

![image-20241009152026768](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009152026768.png)

![image-20241009152203098](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009152203098.png)

다음과 같이 1번의 인접 노드인 2,3이 값을 최신화 한다. 방문하지 않은 노드 중에 가장 거리 배열 값이 작은 (시작 노드에서 갈 수 있는 최단 경로인) 값은 무엇인가? 답은 노드 `3`이다. 

![image-20241009153406316](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009153406316.png)

그럼 이제 3의 입장에서 거리배열을 갱신해보자. 방문 노드 제외하고 3에서 갈 수 있는 노드들은 2와 4번 노드이다. 
2번의 경우를 생각해보면, 1 ➜2 와 1➜3➜2 중 최단 거리가 더 짧은 비용을 택하게 될 것이다.

![image-20241009152809557](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009152809557.png)

2번 노드는 원래의 3이란 값이 더 최소값이라서 현행유지 할 것이다. 다음으로 1,3을 제외한 노드 중 시작 노드에서 가장 최단으로 갈 수 있는 노드는 2번임으로 2번을 선택한다.

![image-20241009153539110](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009153539110.png)

![image-20241009153656386](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009153656386.png)

방문하지 않는 노드들에 대한 갱신을 해보면, 4번 노드는 이전의 경로보다 현재 2번 노드를 통한 경로가 최단 경로라 갱신되는 것을 볼 수 있다. (2에서 3번 노드를 갱신하려고 해도, 최단 경로가 안되므로 갱신할 필요가 없다. - 이미 방문한 노드들은 방문 후 절대 값이 갱신 되지 않는다! 그 이유는 다음 장에서 설명하겠다.)

![image-20241009154050112](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009154050112.png)

![image-20241009154204851](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009154204851.png)

이제 그림으로만 진행하겠다.

![image-20241009154354715](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009154354715.png)

![image-20241009154447040](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009154447040.png)

![image-20241009154616011](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009154616011.png)

![image-20241009154632090](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009154632090.png)

도착 정점 방문을 마치는 순간 다익스트라 알고리즘은 끝난다. 1➜6 최단 경로 비용은 8이다. 

# 4. 특징

### (1) 방문한 노드의 값은 그 뒤로 갱신하려 해도 절대 갱신되지 않는다.

현 노드가 A일 때, 인접 노드를 B,C라고 하자. 이때 A에서 갈 수 있는 최단 경로의 노드가 B라서 B를 방문했다. 그렇다면 
`A ➜ B 의 비용` <= `A -> C 의 비용` 이다. 이미 방문한 노드인 B에 대한 거리 배열 값이 바뀌려면, `A ➜ C ➜ B`의 값이 `A ➜ B` 의 값보다 작아야 하는데, 가능한가? 
이미 `A ➜ C`의 비용이 `A ➜ B`보다 크거나 같은 상황인데, 거기에 양수 값이 추가된다면 절대 불가능하다.

![image-20241009155128048](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/다익스트라알고리즘에대하여/image-20241009155128048.png)

1 ➜ 2 (비용 1)
1 ➜ 3 ➜ 2 (비용 3+K)

만약 해당 그래프에서 이미 방문한 2번 노드에 대한 최단 경로 값이 바뀌려면, `3+k < 1` 즉 `k < -2` 여야 한다. k가 음수 값이 아닌 이상 방문한 노드에 대한 최단 경로 갱신은 이루어지지 않는다. 

### (2) 1번의 특징 때문에, 다익스트라 알고리즘은 가중치가 양의 정수인 경우만 사용할 수 있다. 

만약 음의 정수가 허용되면, 이미 방문한 노드에 대한 최단 경로 갱신이 다시 일어날 가능성이 있고, 이는 그 전에 해왔더 계산과 비교 과정을 무의미하게 만들어, 처음부터 다시 최단 경로를 계산해야 한다. 이는 무한 루프에 빠지게 한다.

# 4. 구현

## (1) 순차 탐색

저번 게시글에서 설명한 `작동원리` 그대로 구현한 방식이다.
방문한 정점과 그렇지 않은 정점 구분을 위해서, 방문배열을 사용한다. 
현재 정점에서 갈 수 있는 간선을 통해 dist[] 배열을 최신화하고, 방문 배열을 통해, 현재까지 방문하지 않은 정점 중 시작 정점에서 최단 거리로 갈 수 있는 정점을 택해야 한다. 
여기서 최악의 경우,  정점의 수가 N개라면, 매번 N번의 정점을 탐색해 다음 방문해야할 정점을 찾아야 하고, 이 행위를 모든 정점을 방문할 때까지 반복해야 하므로 최악의 시간복잡도는 `O(N^2)`이 든다. 

```java
import java.io.*;
import java.util.*;

public class 순차탐색_다익스트라 {
    static int [] dist = new int [N]; 	// N이 변수가 아니라 상수라 치자 
    static boolean [] visited = new boolean [N];
    static ArrayList<Node> [] lists;
    
     public static void main(String[] args) throws IOException {
         // 배열의 원소가 시작노드, 배열 안의 나열된 값들은 <도착노드, 가중치> 묶음
         lists = new ArrayList [N+1]; 
        
         for(int i = 1; i < N+1; i++){
             lists[i] = new ArrayList<>();
         }
         
         // 값 입력 받아서 인접 리스트 채우기 
         // ...
         Dijkstra(1);
    }
    
    public int 최단거리노드찾기() {
        int min_dist = Integer.MAX_VALUE;
        int min_idx = -1;
        
        for(int i = 0; i < N; i++){
            if(visited[i]) continue; 
            if(dist[i] < min_dist){
                min_dist = dist[i];
                min_idx = i;
            }
        }
        return min_idx;
    }
    
    // 시작노드를 인수로 받아서, 해당 노드로부터 모든 노드의 최단 거리를 dist에 입력
    public void Dijkstra(int start) {
        // 1. dist 배열 초기화 
        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[start] = 0;
		visited[start] = true;
        // 2. 본 다익스트라 시작 -> 모든 노드 방문할 때까지만 반복하면 됨. 
        for(int i = 0; i < N-1; i++){
            int now_node = 최단거리노드찾기();
            visited[now_node] = true;
            
            for(int j = 0; j < lists[now_node].size(); i++){
                // 만약 현재까지 j로 가는 최단거리보다 now_node를 거쳐서 j로 가는 최단거리가 더 짧을 경우 갱신
                if(dist[j] > dist[now_node] + lists[now_node].get(j).w){
                    dist[j] = dist[now_node] + lists[now_node].get(j).w;
                }
            }
        }
    }
        
}

class Node {
    int v; // 도착 노드
    int w; // 도착 노드까지 가는 간선의 가중치 
    
    public Node(int v, int w) {
        this.v = v;
        this.w = w;
    }   
}
```

## (2) 우선순위 큐 활용

첫번째 다익스트라 구현 예시를 보면, 방문 배열을 통해 다음으로 방문할 정점을 계산해야해서 최악의 시간 복잡도가 `O(N^2)`의 시간이 들었다. 이를 조금 더 효율적으로 바꾸기 위해서 방문 배열 대신에 우선순위 큐를 활용한다.
 우선순위 큐는 `시작 정점으로부터 거리가 짧은 순으로 정렬` 되어있다. 따라서 첫 번째 방법처럼 모든 정점을 순회하며 확인할 필요 없이 우선순위 큐에서 poll()한 값(현재 방문 가능한 정점 중 최단 거리 정점)을 꺼내서 쓰면 된다. 여기서 주의할 점이 있다. **꺼낸 정점 객체가 이미 방문한 정점 혹은 갱신하기 전의 최단 거리를 가진 정점인지 확인**하는 작업이 필요하다. 

 해당 풀이에서는 현재 방문 중인 정점에서 갈 수 있는 간선을 통해 최신화된 dist[] 값이 정점 객체의 가중치로 사용된다. (위의 풀이에서 정점 객체의 가중치가 오직 현 정점과 방문 정점간의 간선 비용을 나타내던 것과 다르다.) 따라서 Queue에서 꺼내는 값이 Queue 내에서 제일 최단 거리 값이더라도, **이미 방문이 끝난 정점**이거나 **최단 거리 최신화가 안된 객체**일 가능성이 있다.이를 구분하기 위해 다음 조건문을 쓴다.
`if(dist[curNode.idx] < curNode.cost) continue;`
큐에서 꺼낸 정점 객체가 주장하는 최단거리가 현재까지 최신화된 해당 정점으로 가는 최단 거리보다 크다면 더 이상 쓸모 없는 값이므로 탐색을 하지 않고 넘어간다. -> 이미 방문을 했거나, 최신화 이전 값임으로 탐색을 해봤자 무의미하게 시간만 쓰는 격이기 때문이다. 

```java
// 대략적인 구현 방법 

/*
	0. 인접 리스트, 최단 거리 배열 최신화 
	
	1. 정점 객체의 시작노드로부터의 거리가 짧은 순으로 정렬되는 우선순위 큐 생성
	1-1 해당 우선순위 큐에 시작 정점을 넣고 '우선순위 큐'가 빌 때까지 반복문 시작
	
	2. Node curNode = queue.poll() 하여 현재 방문하려는 정점 꺼내기
	2-1. if(dist[curNode.idx] < curNode.cost) continue; 를 통해 유효성 확인
	2-2. 현재 방문 중인 정점이 유효하다면 이를 이용해 최단 거리 배열 갱신
	2-3. 갱신된 정점만 다시 queue에 넣기. 
*/
```

### 시간복잡도는? 🤔💭

우선순위 큐 활용 다익스트라는 우선순위 큐 활용 부분만 뺀다면 1단 반복문과 조건문으로만 이루어져 있기 때문에, 우선순위 큐 활용이 시간이 제일 많이 든다. 따라서 우선순위 큐 활용부분만 생각해주면 된다. N개의 값을 우선순위큐에 삽입한다면 `O(logN)`의 시간이 든다. 추출 시에도 마찬가지로 `O(logN)`의 시간이 든다.
 정점의 수를 V, 간선의 수를 E라 할 때, 삽입은 얼마나 이루어질까? 우선순위 큐 삽입은 최단 거리 배열이 갱신될 때만 이루어진다. 최악의 경우, 현재 방문한 정점에서 갈 수 있는 정점을 고려할 때마다 갱신될 것이므로 간선의 수만큼 삽입 될 것이다. 따라서 `O(ElogE)`의 시간 복잡도가 필요하다. 밀집 그래프까지 가정한다면 E <= V^2 개까지 가능하므로 O(ElogV^2), O(2*ElogV) 즉 `O(ElogV)`로도 표현이 가능하다.  
 추출은 당연히 Queue가 빌때까지 계속 되어야 한다. Queue에는 최소 정점의 개수, 최대로 많아도 간선의 최대값인 정점의 개수의 제곱을 넘지 않을 것이다.  따라서 시간복잡도는 `O(VlogV)`이다. 둘을 합치면 최종적으로 

**`O((V+E)logV)`**의 시간복잡도를 필요로 한다.

### a. 예시

1.

![image-20241009151830596](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009151830596.png)

![image-20241009151848850](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009151848850.png)

![image-20241014235746919](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241014235746919.png)

2.

![image-20241009152026768](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009152026768.png)

![image-20241009152203098](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009152203098.png)

![image-20241015000111965](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241015000111965.png)

3.

![image-20241009153028305](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009153028305.png)

![image-20241009152809557](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009152809557.png)

![image-20241015000324639](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241015000324639.png)

4.

![image-20241009153539110](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009153539110.png)

![image-20241009153656386](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009153656386.png)

![image-20241018224212444](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241018224212444.png)

5.![image-20241009154050112](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009154050112.png)

![image-20241009154204851](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009154204851.png)

![image-20241018224414632](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241018224414632.png)

이제 다음이 중요한데, 이미 방문한 정점`4`가 우선순위 큐의 front에 와있다. 하지만 잘보면 정점4까지의 최단거리라 저장해놓은 것이 `5`로 이미 최신화된 최단 거리 배열 속 dist[4]의 값보다 크다. 그래서 queue.poll() 하자마자 버린다. 

![image-20241018224638853](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241018224638853.png)

6.![image-20241009154354715](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009154354715.png)

![image-20241009154447040](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009154447040.png)

![image-20241018224845886](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241018224845886.png)

7.

![image-20241009154616011](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009154616011.png)

![image-20241009154632090](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241009154632090.png)

![image-20241018224941894](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241018224941894.png)

이후 남은 정점 속 dist 값도 최신화된 dist 배열(최단 거리 배열) 속 dist[6]보다 값이 크므로 그냥 PASS 한다.

![image-20241018225103900](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/010-dijkstra/image-20241018225103900.png)

#### b. 구현

```java
import java.io.*;
import java.util.*;

public class 순차탐색_다익스트라 {
    static int N = 9;
    static int [] dist = new int [N];
    static ArrayList<Vertex> [] lists;
    
     public static void main(String[] args) throws IOException {
         // 0. initialize
         BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
         
         lists = new ArrayList [N+1]; 
         Arrays.fill(dist, Integer.MAX_VALUE);
         for(int i = 1; i < N+1; i++){
             lists[i] = new ArrayList<>();
         }
         
         int edge_cnt = Integer.parseInt(br.readLin());
         for(int i = 0; i < edge_cnt; i++){
             StringTokenizer st = new StringTokenizer(br.readLine());
             int start 	= Integer.parseInt(st.nextToken());
             int end 	= Integer.parseInt(st.nextToken());
             int cost = Integer.parseInt(st.nextToken());
             lists[start].add(new Vertex(end, cost));
         }
         
         // 1부터 모든 정점까지의 최단거리를 찾겠다.
         Dijkstra(1);
    }

    
    // 시작노드를 인수로 받아서, 해당 노드로부터 모든 노드의 최단 거리를 dist에 입력
    public void Dijkstra(int start) {
        PriorityQueue<Vertex> pq = new PriorityQueue<>((o1,o2) -> o1.fast_cost - o2.fast_cost);
        pq.add(new Vertex(start, 0));
        
        while(!pq.isEmpty()){
            Vertex now = pq.poll();
            if(now.cost > dist[now.idx]) continue;
            for(int i = 0; i < list[now.idx].size(); i++){
                int dest_idx = list[now.idx].get(i).idx;
                int compare_dist = list[now.idx].get(i).weight + dist[now.idx];
                
                if(compare_dist < dist[dest_idx]){
                    dist[dest_idx] = compare_dist;
                    pq.add(new Vertex(dest_idx, compare_dist));
                }
            }
        }
    }
    
}

class Vertex {
    int idx;		// 현 정점의 번호
    int cost; 	// 시작 정점에서 현 정점까지의 최단 거리 (큐에 집어넣을 당시의 최단거리라서 최신화된 dist 배열과 차이가 날 수 있음)
    public Vertex (int idx, int fast_cost){
        this.idx = idx;
        this.fast_cost = fast_cost;
    }
}
```

