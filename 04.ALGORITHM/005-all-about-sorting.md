

<목차>

[toc]



# 1. 버블 정렬

## (1) 정의 

인접한 원소끼리 비교 후에, 정렬되어 있지 않다면, 두 원소의 위치를 swap한다. 
이런 식으로 정렬하게 되면 배열 혹은 리스트의 오른쪽부터 값들이 정렬되어 쌓이게 된다. 
예시 

![image-20240702231728741](https://github.com/dalcheonroadhead/what-i-study/assets/102154788/3ea8a74c-90d7-4010-b0de-18fcd8a1f56a)

사진 출처: [블로그](https://blog.naver.com/justant/20204028286)

## (2) 시간 복잡도 

$$
O(n^2)
$$



# 2. 선택 정렬

## (1) 정의

정렬이 되어있지 않은 범위 내에서의 최소값 혹은 최대값을 맨 오른쪽 혹은 맨 왼쪽으로 몰아넣는다. 
이후 정렬되지 않은 부분 내의 최소값 혹은 최대값을 다시 찾아 아까 몰아넣은 곳으로 또 몰아넣는다.

 밑의 사진은 오름차순 정렬을 선택 정렬로 행한 것이고, 최소값을 오른쪽으로 몰아넣었다.


![image-20240702232021586](https://github.com/dalcheonroadhead/what-i-study/assets/102154788/fed36772-76db-40f6-adb3-67238fa4bf85)

사진 출처: [블로그](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=jsky10503&logNo=221249976761)

## (2) 시간 복잡도 

$$
O(n^2)
$$

# 3. 삽입 정렬

## (1) 정의

- 정렬된 범위 내에 현재 값을 삽입할 적절한 위치를 찾는다. (오름 차순, 내림차순일 경우, 범위 내 원소와 현재 값 크기 비교)
- 삽입할 위치에 현재 값을 삽입한다. (정렬된 영역이 +1 증가한다.)
- 정렬되지 않은 영역의 값을 가지고 위의 과정을 반복한다.

![image-20240702232501005](https://github.com/dalcheonroadhead/what-i-study/assets/102154788/565ca445-9fcf-45c7-aa2d-53db755ffadf)


사진 출처: [블로그](https://m.blog.naver.com/PostView.nhn?blogId=justant&logNo=20204025251&proxyReferer=https:%2F%2Fwww.google.com%2F)

## (2) 시간 복잡도 

$$
O(n^2)
$$

# 4. Quick 정렬

## (1) 정의 

오름차순을 Quick 정렬을 이용해 정렬시킨다고 해보자.

- 기준 값인 pivot 값을 설정해서, 그 값도 큰 데이터들은 오른쪽으로, 그 값보다 작은 데이터들은 왼쪽으로 몬다. 
  (pivot 제외 맨 왼쪽의 index를 가르키는 포인터를 i, 맨 오른쪽을 가르키는 포인터를 j라고 해보자.)

  - i가 가르키는 값이 pivot 값보다 작으면 i++
  - j가 가르키는 값이 pivot 값보다 크면 j-- 
  - i가 가르키는 값이 pivot보다 크다면 i는 해당 자리에 멈춰있을 것이다. j도 마찬가지로 j가 가르키는 값이 pivot보다 작으면 멈춰있을 것이다. 둘 다 위의 이유로 멈춰있다면 서로 값을 바꾸고, i++, j-- 한다.

  

- pivot을 2개의 집단 사이의 index에 저장한다.

- 각 집단별로 재귀를 이용해 또 quick 정렬을 반복한다. (더 이상 쪼개지지 않을 때까지)

## (2) 시간 복잡도

$$
O(n^2)
$$

## (3) 예시

![image-20240705230217430](https://github.com/dalcheonroadhead/what-i-study/assets/102154788/caaef305-500b-46e7-9edf-f21385f0891f)

# 5. 병합 정렬

## (1) 정의 

병합 정렬은 `분할 정복` + `투 포인터의 개념`을 이용하여 배열 혹은 리스트를 정렬하는 방법이다. 이론은 다음과 같다.

- 배열 혹은 리스트를 더 이상 쪼갤 수 없는 단위까지 쪼갠다. (분할)
- 작은 단위들을 정렬한다. 
- 작은 단위들을 병합하여 재정렬한다. 이것을 배열 혹은 리스트 전체가 정렬될 때까지 반복한다. (정복 by 투 포인터)

![image-20240706183925930](https://github.com/dalcheonroadhead/what-i-study/assets/102154788/09c9bc8c-8cc2-4c66-a084-7d7cba0ff94c)


## (2) 구현 방법

![image-20240706184004969](https://github.com/dalcheonroadhead/what-i-study/assets/102154788/b7c855e4-c520-4805-8ea8-7a44690440aa)


배열의 두 부분을 병합할 때를 생각해보자. 이때 포인터가 총 3개 필요하다.

- 오름차순 정렬을 가정했을 때, s와 m을 비교해서 더 작은 값을 k로 보낸다. 그 후 k++, k에 값을 집어넣은 포인터 ++ 을 한다. 
- 해당 반복은 s나 m이 자신의 범위 맨 끝까지 갈 때까지 반복한다. 
- 이렇게 되면 한쪽 배열의 값은 전부 다 소진했는데, 나머지는 아닐 것이다. 
  전체 배열의 빈 부분은 해당 나머지들을 바로바로 채워주면 된다. (부분 배열들은 모두 정렬된 상태이기 때문이다.)

## (3) 시간 복잡도

$$
O(nlogn)
$$



# 6. 기수 정렬

## (1) 정의 

주어진 입력값의 자릿수를 비교하여 값을 정렬하는 방법. 

### a. 장점

정렬들 중에서 가장 성능이 빠르다. O(n)

### b. 단점

- 성능이 빠른 만큼, 많은 량의 메모리를 차지한다. Queue가 10개 (음수 값이 있을 때는 10개 더 필요) 혹은 Bucket 배열을 따로 써야한다. 또한 10개의 Queue에 정렬된 수를 넣었다 빼내는 과정에서 추가적인 메모리 소요가 크다. 
- 양의 정수와 음의 정수로 데이터의 종류가 양분화 되었다면, 양의 정수는 양의 정수끼리 따로 모아서 처리, 음의 정수는 음의 정수끼리 따로 모아서 처리해야 한다. (안 그러면 정렬이 안된다.)

## (2) 구현 방법

### 세팅 

Queue 10개를 준비한다. 혹은 그에 상응하는 bucket 배열을 준비한다. (bucket 배열 사용법은 수 정렬하기 3 정리에 보면 있다.)
각 Queue는 0 ~ 9 사이의 숫자를 대변한다.

다음은 입력으로 주어진 배열이다.

![image-20240707152254280](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/005-all-about-sorting/image-20240707152254280.png)

- 먼저 1의 자릿수를 기준으로 자신이 해당하는 번호의 queue에 집어넣는다.
  ![image-20240707152236029](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/005-all-about-sorting/image-20240707152236029.png)

- 0번째 queue부터 9번째 queue까지 차례대로 값을 원래의 배열에 저장시킨다.
  ![image-20240707152359830](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/005-all-about-sorting/image-20240707152359830.png)
- 이번에는 10의 자릿수를 토대로 다시 집어넣는다. (순서는 배열의 0번부터 차례대로!)
  ![image-20240707152447464](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/005-all-about-sorting/image-20240707152447464.png)

- 다시 넣은 값들을 0번 queue부터 순서대로 빼서 원본 배열에 집어넣는다. 

  ![image-20240707152521969](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/005-all-about-sorting/image-20240707152521969.png)

- 이번에는 마지막인 100의 자릿수를 토대로 다시 queue에 집어넣는다. 
  ![image-20240707152556376](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/005-all-about-sorting/image-20240707152556376.png)

- 마지막으로 원본 배열에 넣는다. 해당 값들이 정렬된 것을 볼 수 있다. 

  ![image-20240707152621037](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/005-all-about-sorting/image-20240707152621037.png)

사진 출처: [javapoint.com](https://www.javatpoint.com/radix-sort)

