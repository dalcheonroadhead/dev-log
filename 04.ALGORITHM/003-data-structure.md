[toc] 



# 1. Array

![image-20240622202928470](https://github.com/dalcheonroadhead/what-i-study/assets/102154788/8b4c62f5-4bdc-4350-8ed3-c3e44b22f2bc)


## (1) 정의 

번호와 번호에 대응되는 값들로 이루어진 자료구조를 의미한다. 
번호만 알고 있으면, 해당 번호에 해당하는 값을 O(1)에 조회가 가능하다. 

## (2) 특징

ⓐ Index에 해당하는 값에 바로 접근할 수 있다. (값 조회에 빠르다.)
ⓑ 배열은 선언할 때, 그 크기가 정해진다. 그 이후, 줄이거나, 늘릴 수 없다. 
ⓒ 따라서 중간에 값을 삽입하거나 삭제하기가 어렵다. 
     만약에 그러한 구현이 꼭 필요하다면, 빈 배열을 통해 변경사항이 반영된 배열을 새로 만들어야 한다.

## (3) 많이 쓰 함수 

ⓐ Arrays.asList(T..a)
: 배열을 ArrayList로 변환시켜준다. 

ⓑ Arrays.toString()
: 배열을 문자열 형태로 변경해준다. 문자열 형태: [a,b,c,d...] → 출력할 때 많이 씀 

ⓒ Arrays.sort(array, Comparator)
: Comparator의 정렬 기준에 따라 Array를 정렬해준다. 만약 2번째 인자가 없으면, 오름차순으로 정렬한다. 

```java
int [] array = {3,4,5,1,2,6};

// 1. 일반적인 오름 차순 내림차순
Arrays.sort(array); // {1,2,3,4,5,6}
Arrays.sort(array, Collections.reverseOrder()); // {6,5,4,3,2,1}

// 2. 짝수 우선 오름차순 정렬 후, 홀수 오름차순 정렬
Arrays.sort(array, new Comparator<Integer>() {
    
    public int compare(Integer o1, Integer o2){
        
        boolean isO1Even = o1%2 == 0;
        boolean isO2Even = o2%2 == 0;
        
        if (isO1Even && isO2Even) {            // 둘 다 짝수
            return Integer.compare(o1,o2);
        } else if (!isO1Even && isO2Even){      // 둘 다 홀수
			return Integer.compare(o1,o2);             
        } else if (isO1Even) {  // 앞의 것이 짝수이고, 뒤에 것이 홀수
            return -1;  // 자리 바꾸지 않기
        } else if (isO2Even) {  // 앞의 것이 홀수이고, 뒤에 것이 짝수
            return 1;  // 지리 바꾸기
        }
    }
})
   

```

```java
// 3. 2차원 배열에 관한 정렬
        int[][] array = {
            {3, 2, 1},
            {1, 2, 3},
            {2, 3, 1}
        };

        // 첫 번째 열을 기준으로 각 행을 정렬
        Arrays.sort(array, new Comparator<int[]>() {
            @Override
            public int compare(int[] a, int[] b) {
                return Integer.compare(a[0], b[0]);
            }
        });

        // 정렬된 배열 출력
        for (int[] row : array) {
            System.out.println(Arrays.toString(row));
        }
		
/*  출력
	{1,2,3}
	{2,3,1}
	{3,2,1}
*/
	
```

> Comparator의 compare 함수는 요소 2개만 가지고 비교하지만, 단순히 2개 비교에서 끝나는 것이 아니고, 모든 요소가 원하는 방향으로 정렬될 때까지 비교 및 위치 조정 과정을 반복한다.

# 2. LinkedList

![image-20240622203908325](https://github.com/dalcheonroadhead/what-i-study/assets/102154788/6f5d23c8-3e45-4f5b-9c79-3b75f9eea60b)


## (1) 정의

pointer와 value라는 구성요소를 가진 Node라는 것을 Pointer로 줄이어 묶은 자료구조를 뜻한다. 

- pointer : 다음 노드의 주소값을 저장하고 있다. 이를 이용해 다음 노드를 조회할 수 있음.
- value    : 실제 값을 저장하고 있는 곳 이다.

## (2) 특징 

ⓐ 특정 값을 조회하려면 head에서 시작해서 처음부터 원하는 값이 나올 때까지 조회해야 한다. (index가 없으므로)
ⓑ 크기가 따로 정해져 있지 않다. 필요에 따라 늘리거나 줄여줘도 된다. (가변적인 특징 때문에 값 업데이트가 잦을 때 유용)
ⓒ 데이터 삽입, 삭제 연산이 빠르다. (포인터 값만 조정해주면 된다.)



# 3. ArrayList

## (1) 정의

Node가 아닌 Array를 사용하여 구현한 List 자료구조 → 따라서 Array의 특성에 좀 더 가깝다. 

 ## (2) 특징

ⓐ ArrayList 클래스는 내부적으로 object[]를 이용하여 구현했다. 
ⓑ 따라서 조회에 빠르며, 중간 삽입 저장 혹은 배열 길이 늘리기에는 느리다. (배열의 속성을 그대로 가지고 있음)

배열의 길이 늘리기 느린 이유 
:배열 공간이 꽉 찰 때마다 배열을 copy하여 더 큰 배열에 집어넣는 식으로 길이 가변을 구현함

# 4. HashMap

## (1) 정의

데이터를 저장할 때, Key, Value 쌍으로 저장하는 자료구조 
Key를 락커룸 키, value를 락커룸 속에 저장된 물건이라 생각하자. 라커룸에서 물건을 꺼내려면, 해당 락커의 키가 있어야 한다. 마찬가지로 내가 저장한 value를 꺼내기 위해서는 그 value가 저장된 key를 알고 있어야 한다. 

![image-20240622220118607](https://github.com/dalcheonroadhead/what-i-study/assets/102154788/3da1f8a9-868c-4daf-ae63-c99504bd92f7)


## (2) 특징

- 값이 저장되는 원리 
  : key값을 해쉬함수에 인수로 넣어 나온 결과에 해당하는 주소값에 value를 저장한다. 따라서 올바른 key값이어야지만 값에 접근이 가능하다.
- key, value는 각각 다른 자료형으로 선정이 가능하다. 
- 값 삭제, 추가, 특히 조회의 속도가 빠르다. 
- key 값은 중복될 수 없다. (value를 꺼내기 위해서 유일무이 해야한다.) 반면 value는 key값만 다르다면, 중복 가능하다.

## (3) 많이 쓰는 함수

ⓐ void **putAll**(Map<? extends K, ? extends V> m) : Map m의 데이터를 전부 저장합니다.
ⓑ V **putIfAbsent**(K key, V value) : 기존 데이터에 key가 없으면  key와 value를 저장합니다. 
ⓒ boolean **containsKey(**Object key) : key와 일치하는 데이터가 있는지 여부를 반환합니다. (있으면 true)
ⓓ boolean **containsValue**(Object value) : value가 일치하는 데이터가 있는지 여부를 반환합니다. (있으면 true)
ⓔ Set<Map.Entry<K, V>> **entrySet( )** : 모든 key-value 맵핑 데이터를 가진 Set 데이터를 반환합니다. 
    (entrySet()은 말 그대로, map.entry의 집합(set)을 나타낸다. map.entry는 하나의 키와 값 쌍을 가지는 객체이다. 
     Map과 Entry의 관계는 DB 테이블과 Tuple의 관계로 생각하면 된다.) 

     ```java
     // 사용 예시 
     Map<String, String> hashMap = new HashMap<>();
      
     for (Map.Entry<String, String> entry : hashMap.entrySet()) {
         System.out.println("key : " + entry.getKey());
         System.out.println("value : " + entry.getValue());
     }
     ```

ⓕ  V **getOrDefault**(Object key, V defaultValue) : key와 맵핑된 value값을 반환하고 없으면 defaultValue값을 반환합니다.
ⓖ **compute(key, remapping Function)**: 2번째 람다식에 key, value를 인자로 파라미터로 전달하고 실행, 결과가 null 이 아니라면, 첫 번째 인자의 key로 해당 결과 값을 저장하고 반환, null 이면 NPE 출력 

```java
// 사용 예시 
Map<String, Integer> map = new HashMap<>();

map.compute("coding", (key, oldValue) -> oldValue == null ? 0 : oldValue + 1);
System.out.println(map.get("coding"));	
// 실행결과 0

// key의 값이 있는게 확실 한 경우에는 아래처럼도 가능
map.compute("coding", (key, oldValue) -> oldValue + 1);
System.out.println(map.get("coding"));	
// 실행결과 1
```

ⓗ **computeIfAbsent(key, remapping Function)**: key에 해당하는 값이 없으면, 2번째 인자인 람다식을 실행하고 그 결과를 key에 저장 및 리턴, 만약 있으면 그대로 그 value를 return 
```java
// 사용 예시
Map<String, String> map = new HashMap<>();

// 값이 없으면 람다식의 결과 값을 put하고 값 리턴
String value = map.computeIfAbsent("nojam", key -> "Coding" + key );
System.out.println(value);
// 실행결과 : Codingnojam

// 값이 존재하면 해당 key의 값 리턴
value = map.computeIfAbsent("nojam", key -> "Hi" + key );
System.out.println(value);
// 실행결과 : Codingnojam -> hi가 저장되지 않은 것을 볼 수 있다.
```

ⓘ **computeIfPresent(key, remapping Function)**: key에 해당하는 값이 있을 때만, 그 값으로 람다식 실행 및 그 결과를 return 및 key에 다시 저장, 없으면 null 출력 
 ```java
 // 사용 예시
 Map<String, String> map = new HashMap<>();
 map.put("Coding", "NoJam");
 
 // Coding이라는 키에 매핑된 value값 있으므로 람다식을 수행하고 새로운 값을 키에 매핑 후 리턴
 String str = map.computeIfPresent("Coding", (key, oldValue) -> key + oldValue + "Hello");
 System.out.println(str);	// CodingNoJamHello 출력
 
 // none이라는 키에 매핑된 value값이 없으므로 null 리턴
 String str_null = map.computeIfPresent("none", (key, oldValue) -> key + oldValue + "Hello");
 System.out.println(str_null); 	// null 출력
 ```

ⓙ foreach(): map의 key와 value 쌍을 하나씩 꺼내서 작업할 수 있게 해주는 함수
```java
// 사용 예시 
Map<String, String> map = new HashMap<>();
map.put("coding", "01");
map.put("Java", "NoJam");
map.put("Coding", "CodingNoJamHello");
map.put("nojam", "Codingnojam");

// 내부에서 key와 value를 쌍으로 하나씩 꺼내서 출력
map.forEach((k, v) -> System.out.println(k + " : " + v));
/* 출력 예시
 * coding : 01
 * Java : NoJam
 * Coding : CodingNoJamHello
 * nojam : Codingnojam 
 */
```





