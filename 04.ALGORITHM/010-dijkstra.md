# 1. 개념

그래프가 주어졌을 때, `시작 노드`를 하나 정해서, 그 시작노드부터 `연결된 모든 노드로 가는 최단경로`를 `한 번에` 구하는 알고리즘이다.

### ⭐BFS와의 차이점

기존에는 그래프 내의 최단 경로를 찾기 위해서, `BFS`를 자주 활용했다. 하지만, BFS는 `가중치가 없는 그래프`에서만 최단경로를 구할 수 있다. 반면 `다익스트라 알고리즘`은 `가중치가 있는 그래프`에서도 최단 경로를 구할 수 있다.

# 2. 작동 원리

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

### b. 현 노드에서 직접적으로 연결된 노드들의 간선을 이용해 거리 배열을 갱신한다.

갱신 기준은 다음과 같다. 거리배열을 `dist`, 현재 방문 중인 노드가 `A`, A와 연결되고 거리 배열 갱신할 노드가 `B`라고 할 때, 
`dist[B]` = `Math.min(dist[B], dist[A] + (A -> B 간선 비용))`

### c. 거리 배열 갱신을 끝낸 후, 아직 방문하지 않은 노드들 중에서 `시작노드에서부터` 최단거리로 갈 수 있는 노드를 방문한다. 

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



