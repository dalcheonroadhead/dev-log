[toc]

# 1. 2차원 배열 회전

## (1) 시계 방향 90도 회전

![image-20240614002123109](https://github.com/dalcheonroadhead/what-i-study/assets/102154788/9ce6b02d-85dd-424e-a82d-d8ee47cd1294)


Before(R,C) 는 원래 행렬에서의 원소의 위치를 말하는 것이고, After()는 바뀐 행렬에서 동일한 원소의 위치를 말하는 것이다. 

예를 들어,  원소 '9'는 (1,3) 이다. 따라서 바뀐 행렬에서는 공식대로하면 (3,3) 이다. 실제로도 그렇다. 

## (2) 반시계 방향 90도 회전

![image-20240614002146894](https://github.com/dalcheonroadhead/what-i-study/assets/102154788/f3a60458-1d51-4068-85c3-d36af0fe01fd)

Before(), After()는 위에 설명한 그대로이다. 

## (3) 배열 시계 방향 혹은 반 시계 방향으로 원소를 한 칸씩 이동 

![KakaoTalk_20240614_024319206](https://github.com/dalcheonroadhead/what-i-study/assets/102154788/57468324-e7dc-4eee-9cee-192372e6ad09)

idx, idy를 이용하여, 한 칸씩 이동. 

1. 최초의 값을 temp라는 변수에 저장한다.
2. idx,idy를 이용하여, 배열의 끝부분에서는 뱡향 전환을 하며, 동서남북으로 원소를 한 칸씩 옮긴다.
3. 마지막 원소는 최초의 값에 인접한 원소이다. 여기에는 temp의 값을 집어넣는다. 



# 2. 구간 합 구하기 

![image-20240623215432256](https://github.com/dalcheonroadhead/what-i-study/assets/102154788/96a5a0fd-fcf8-4412-a05b-5907d08b3cb9)


만약 여기서 

![image-20240623215526155](https://github.com/dalcheonroadhead/what-i-study/assets/102154788/c049578f-0196-4d67-b3af-2e529902d20b)


이걸 구한다면 원래는 for문 루프를 돌아야겠지만, 누적합 배열을 이용하면, 35 -10 한 번만 하면 된다. 이러면 시간복잡도가 O(1)이 든다. 구간합 문제의 기본은 이것이고, 이를 응용해서 문제를 풀면 된다.

# 3. 투 포인터 & 슬라이딩 윈도우 

## (1) 투 포인터 

배열이나 리스트 내에 2개의 포인터를 두고, 그것으로 특정 값 혹은 범위를 지칭해가면서 문제를 푸는 방법

-  이런 식으로 오른쪽 포인터가 움직이면, 왼쪽 포인터가 따라가면서 문제를 해결하는 유형이 있고
![image-20240704200521553](https://github.com/dalcheonroadhead/what-i-study/assets/102154788/f223e495-8490-487c-9138-53e93912a263)


  사진 출처: [블로그](https://butter-shower.tistory.com/226)

- 아래 그림처럼 포인터 들을 양 끝에 두어서 서로 가까워지도록 하여 문제를 해결하는 경우도 있다. 
![image-20240704200716306](https://github.com/dalcheonroadhead/what-i-study/assets/102154788/4697371b-6e9f-4101-8cb1-32e23046f6a4)


  출처: [블로그](https://velog.io/@dmsgur7112/Algorithm-%ED%88%AC-%ED%8F%AC%EC%9D%B8%ED%84%B0-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98)

## (2) 슬라이딩 윈도우

문틀에서 창문을 미는 것처럼, 오른쪽 포인터가 움직인 만큼 왼쪽 포인터도 움직여 포인터 내의 범위가 일관되도록하여 문제를 푸는 기술이다. 
![image-20240704200907480](https://github.com/dalcheonroadhead/what-i-study/assets/102154788/4ee7079a-d8a4-4622-a979-c72bdf16c379)



출처: [블로그](https://learning-e.tistory.com/36)
