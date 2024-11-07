# 1. 그리디 알고리즘은 무엇인가요? 💡

`Greedy Algorithm`은 매 선택의 순간마다 당시에 고를 수 있는 최선의 선택지를 골라가는 것이 전체에서 봤을 때 최선의 선택이라고 가정하는 알고리즘이다.
예를 들어 다음과 같은 문제가 있다고 해보자.
![image-20241107233812280](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/012-Greedy Algorithm/image-20241107233812280.png)

![image-20241107233930605](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/012-Greedy Algorithm/image-20241107233930605.png)
현재 A의 시점에서 고를 수 있는 선택지 중 C가 최단거리이다. 그러므로 C를 선택한다. 

![image-20241107234111261](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/012-Greedy Algorithm/image-20241107234111261.png)
C 시점에서 고를 수 있는 선택지 중 최단 거리로 갈 수 있는 노드는 G이다. 따라서 G를 선택한다. 두번의 선택 후 무조건 F를 비용 0으로 갈 수 있다고 할 때, 전체 노드는 다음과 같다.
![image-20241107234511273](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/012-Greedy Algorithm/image-20241107234511273.png)

매 순간 갈 수 있는 최선의 선택지를 골랐더니 전체에서 봤을 때도 최선의 선택이었다. 이와 같이 `매 순간의 최선` = `전체의 최선` 이 성립할 때, 이러한 논리를 `Greedy 알고리즘`이라 한다. 

# 2. 왜 사용하죠? 🤷‍♂️

음... 저러한 가정이 성립하는 문제라면 굳이 구현으로 완전탐색을 하는 것은 시간 낭비이기 때문이다. 하지만 개념 설명에서 봤다시피, `매 순간의 최선` = `전체의 최선`이 성립할때만 이용이 가능하다. 따라서 해당 가정이 성립하는지 문제를 풀며 유심히 봐야 한다.

# 3. 어떻게 구현하죠? 🔍

다음 3가지 가정을 따른다.

1. 재귀이든 Loop이든 선택의 순간이 오면 해당 시점에서 고를 수 있는 최선의 선택지를 고른다.
2. 해당 선택지가 문제의 조건을 위반하지 않는지 확인한다.
3. 전체 문제의 답이 나왔는지 확인한다. 답이 아직 나오지 않았다면 1번을 반복한다.

