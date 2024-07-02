

<목차>

[toc]



# 1. 버블 정렬

## (1) 정의 

인접한 원소끼리 비교 후에, 정렬되어 있지 않다면, 두 원소의 위치를 swap한다. 
이런 식으로 정렬하게 되면 배열 혹은 리스트의 오른쪽부터 값들이 정렬되어 쌓이게 된다. 
예시 

![image-20240702231728741](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/005-all-about-sorting/image-20240702231728741.png)

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

![image-20240702232021586](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/005-all-about-sorting/image-20240702232021586.png)

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

![image-20240702232449866](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/005-all-about-sorting/image-20240702232449866.png)

사진 출처: [블로그](https://m.blog.naver.com/PostView.nhn?blogId=justant&logNo=20204025251&proxyReferer=https:%2F%2Fwww.google.com%2F)

## (2) 시간 복잡도 

$$
O(n^2)
$$

