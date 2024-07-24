 

# 1. Binary-Search란 무엇인가요? 

**정렬된 배열 혹은 List** 에서 `탐색 구간을 절반씩 줄여가며` 특정 값을 찾는 탐색법이다. 다음과 같이 진행 된다.

(0) 기본적인 용어는 다음과 같다. 
`target`: 찾고자 하는 값, `start` : 왼쪽 포인터,(index=0에서 시작), `end`: 오른쪽 포인터(배열 끝에서 시작), `mid`:  두 값의 중간에 위치한 값 

(1) `mid`를 구하고 `target` 값과 대소관계를 비교한다.
(2) `mid` > `target` 이면,  `mid` 값이 `target`값으로 향하도록, `end` = `mid`-1 로 탐색 영역을 절반 줄인다. 
(3) `mid` < `target` 이면, `start` = `mid` +1 이 되도록 하여, 탐색 영역을 절반 줄인다.
(4) `mid` == `target` 이면 값을 구했으므로, 이분 탐색을 탈출한다.

위의 과정이 일반적인 이분 탐색의 과정이다. 물론, `upperBound`나 `LowerBound`는 구하고자 하는 값의 목표가 다르므로, 이것에서 조금 변형된다.

다음은 [geeksforgeeks](https://www.geeksforgeeks.org/binary-search/)에서 보여주는 이분 탐색으로 23을 찾는 과정이다.  

![image-20240724162511705](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/008-binary-search/image-20240724162511705.png)

# 2. 시간 복잡도: `O(logN)`

이분 탐색은 탐색 영역을 매 번 절반씩 줄여가기 때문에, 속도가 O(logN)으로 빠르다. 만약 정렬된 16개의 데이터에서 특정 데이터를 찾는다면, 4번의 조회안에 값을 찾을 수 있다. 하지만 전제조건이 있다. 
우리가 값을 찾으려는 배열 혹은 List가 `정렬`되어 있어야 한다는 것이다.



# 3. (심화) UpperBound와 LowerBound는 무엇인가요? 

UpperBound와 LowerBound는 mid값이 target 값과 일치할 때, 바로 탈출하지 않고, 계속 이분 탐색을 하여, 특정 `경계값`을 찾는 알고리즘이다. 해당 알고리즘은 주로 `target 값이 List에 중복되어 많을 때` 사용하기 좋다.

*경계값*: 특정 값이 시작하거나, 끝나는 위치

## (1) `UpperBound`

`UpperBound`는 `target값보다 큰 값의 첫 시작점`을 반환하는 방법이다. 

 구현 방법은 다음과 같다.

1. `mid` <= `target` 일 때, `start`=`mid`+1 로 올린다.
2. `mid` > `target` 일 때, `end` = `mid` -1로 내린다. 

이렇게 하면, 결국, mid가 target과 일치할 때도, start 포인터를 오른쪽으로 이동시켜서,  `start` 값이 target 값보다 큰 값이 시작되는 첫 위치를 가리키게 된다. 

그림은 [해당 블로그](https://yoongrammer.tistory.com/105)를 참고해라

## (2) `LowerBound`

`LowerBound`는 `target값이 시작하는 위치`를 반환하는 방법이다. 

구현 방법은 다음과 같다. 

1. `mid` < `target` 일 때, `start` = mid+1 로 올린다.
2. `mid` >= `target` 일 때, `end` = mid-1로 줄인다. 

이렇게 하면, mid가 target값과 일치해도 계속 내려가서, 결국 `start`가 target값이 value로 나오는 배열의 첫 시작점에 위치하게 된다. 

그림은  [이 블로그](https://yoongrammer.tistory.com/105)를 봐라



