# 0. 그래프 탐색의 기본인 DFS와 BFS

`그래프 탐색이란 무엇인가?`

그래프 탐색이란, 정점과 간선으로 이루어진 그래프에서 특정 정점을 선택하고, 해당 정점에서 인접한 정점을 방문하는 것을 말한다. 이러한 정점 방문 방법에는 크게 2가지가 있는데, 이것이 앞으로 살펴볼 **`DFS`**와 **`BFS`**이다.

![image-20250107142406012](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/020_DFS&BFS/image-20250107142406012.png)

우리는 위의 그래프를 예시로 사용하며 하나씩 이해해 보겠다.

# 1. DFS

**DFS는** Depth First Search의 약자로 **깊이 우선 탐색**을 뜻한다. 말 그대로 방문하기로 정한 인접 정점의 최대 깊이까지 탐색을 마친 후, 다음 인접 정점을 확인하는 것이다. 

![image-20250107142530914](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/020_DFS&BFS/image-20250107142530914.png)

알고리즘 논리는 다음과 같다.

- (1) 현재 정점과 인접한 정점을 방문한다.
- (2) 방문한 정점에서 아직 방문하지 않은 정점이 있다면 **방문하지 않은 정점을 모두 방문할 때까지** (1)번을 반복하고 다시 출발 정점으로 돌아간다.
- (3) 방문한 정점의 인접 정점을 모두 방문한 상태라면 출발 정점으로 바로 돌아간다.

## (1) 코드

위의 설명을 코드로 나타내면 다음과 같다. 코드로 나타내는 방법에는 (<u>a. STACK 이용</u>)과 (<u>b. 재귀 이용</u>) 두 가지 방법이 있는데, 사실 재귀도 Call Stack이라는 Stack 자료구조에 의해 구현됨으로, 둘의 원리는 똑같다. 여기서는 재귀를 이용해서 구현해보겠다.

### a. SUDO CODE

```json
함수 dfs("인접 리스트(lists)", "시작 정점 번호(S)", "방문배열(isVisited)")

1. 시작 정점 방문 체크

2. for(현 깊이의 시작정점의 인접 정점을 반복문으로 하나씩 조회) 
	if(isVisited[조회할 인접 정점] = false(미방문 상태))
			2-1 해당 정점 방문 체크
			2-2 재귀 dfs("인접리스트(lists)", "방문할 정점", "방문 배열");

```

더 이상 미방문 정점이 없으면 재귀가 자동 종료되므로 기저 조건은 없다.

### b. java 코드

다음은 위 sudo code를 java로 실체화한 모습이다.

```java
    public static void dfs(ArrayList<Integer> [] lists, int now, boolean [] isVisited, StringBuilder ans) {
        ans.append(now).append(" ");
        isVisited[now] = true;

        for (int i = 0; i < lists[now].size(); i++) {
            int next = lists[now].get(i);
            if(!isVisited[next]) {
                dfs(lists, next, isVisited, ans);
            }
        }
    }
```

해당 코드는 [DFS와 BFS](https://www.acmicpc.net/problem/1260)의 풀이코드 중 일부 이기도 하다. 한 번 직접 구현해보실 분들은 문제를 풀어보시길 바란다.

## (2) 시간 복잡도

$$
O(V+E)
$$

V는 정점의 개수, E는 간선의 개수

## (3) 소결

DFS는 매우 간단한 알고리즘이지만, 모든 그래프 문제 풀이의 근간이 되는 중요한 알고리즘이다. 많은 그래프 문제가 해당 알고리즘을 응용해야만 풀 수 있게 되어 있기 때문이다. 

백트래킹도 사실 DFS라고 볼 수 있다.



# 2. BFS

**BFS**는 Breadth First Search의 약자로 **넓이 우선 탐색**이라 불린다. 해당 알고리즘은 시작 정점에서 **거리가 가까운 정점 부터 방문**하는 알고리즘이다.

![image-20250107150931530](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/020_DFS&BFS/image-20250107150931530.png)

그럼 어떻게 **가까운 거리의 정점부터 탐색**할 수 있을까? 

이를 실현하기 위해서는 **Queue**라는 자료 구조를 사용해야 한다. Queue는 선입 선출 자료구조로써 맨 처음 넣었던 값이 제일 먼저 나온다. 이를 활용해 다음과 같이 진행하면 된다.

- (1) 시작 정점의 인접 정점 모두 Queue에 넣기
- (2) Queue에서 먼저 나온 값 처리 및 해당 정점의 인접 정점 모두 Queue에 넣기 

이러면, 항상 시작 정점에서 가까운 정점부터 처리될 것이다. 방문 하고 싶은 정점도 Queue에 넣어서 순서를 기다려야 하니, 인내심이 있는 알고리즘이라 보면 좋겠다.

## (1) 코드

### a. SUDO CODE

```json
bfs("인접 리스트[lists]", "시작 정점", "방문 배열") 
	"Queue" (// 방문 하고픈 정점 대기 리스트)
    
    1.    Queue에서 정점 하나 꺼내기
    2.    정점에 대한 처리
    3.    해당 정점에서 갈 수 있는 인접 정점 모두 Queue에 넣기 
    4.    1~3을 Queue가 빌 때까지 반복
```

## b. java 코드

```java
 public static void bfs (ArrayList<Integer> [] lists, int S, StringBuilder ans){
        boolean [] isVisited = new boolean[lists.length];
        ArrayDeque<Integer> aq1 = new ArrayDeque<>();
        aq1.add(S);
        isVisited[S] = true;

        while (!aq1.isEmpty()){
            int qSize = aq1.size();
            for (int i = 0; i < qSize; i++) {
                int now = aq1.poll();
                ans.append(now).append(" ");

                for (int j = 0; j < lists[now].size(); j++) {
                    int next = lists[now].get(j);
                    if(!isVisited[next]){
                        isVisited[next] = true;
                        aq1.add(next);
                    }
                }
            }
        }
 }
```

해당 코드 역시 [DFS와 BFS](https://www.acmicpc.net/problem/1260)의 풀이코드 중 일부이다.

## (2) 시간 복잡도

$$
O(V+E)
$$

V는 정점의 개수, E는 간선의 개수

## (3) 소결

BFS를 보면, 시작 정점에서 가장 가까운 정점부터 차례대로 방문한다. 따라서 간선에 **`가중치`**가 없는 경우, BFS를 활용해 특정 정점까지의 최단 경로를 찾을 수 있다.