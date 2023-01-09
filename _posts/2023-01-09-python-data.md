---
layout: single
title: python (list, dictionary)의 효율적인 활용
categories: python
tags: [python]
toc: true
---

# python (list, dictionary)의 효율적인 활용

## 시간 복잡도 (Big-O notation)
<pre>
컴퓨터과학에서 알고리즘의 **시간복잡도**는 입력을 나타내는 문자열 길이의 함수로서 작동하는 알고리즘을 취해 시간을 정량화 하는 것이다. 알고리즘의 시간복잡도는 주로 빅-오 표기법을 사용하여 나타내며, 이 빅-오 표기법은 계수와 낮은 차수의 항을 제외시키는 방법이다. 이런 방식으로 표현할 때, (예를 들면, 입력크기를 무한대로 입력하여) 시간복잡도를 점근적으로 묘사한다고 말한다.

예시로서, 만약 크기 n의 모든 입력에 대해 알고리즘에 필요한 시간이 최대(어떤 n zero 보다 크지 않은 모든 n에 대하여) 5n세제곱 + 3n의 식을 가진다면, 이 알고리즘의 점근적 시간 복잡도는 O(n세제곱)이라고 할 수 있다.
</pre>

## immutable / mutable

* immutable : 한번 만들어지면 변하지 않음.
  * ex) Tuple, String, Byte, Frozen Set
  * 한번 만들어지면 내부 데이터가 바뀌자 않음

* mutable: 변할 수 있음.
  * ex) List, Dictionary, Set
  * 만들어진 후 값을 변화시킬 수 있음
```python
# 만들어진 후 값을 변화시킬 수 있음
l = [1, 2, 3, 4]
l.append(5) # expect [1, 2, 3, 4, 5]
```
### tuple
* mutable ?
  ```python
  a = (1, 2)
  b = (3, 4)
  a = a + b # tupleconcat(PyTupleObject *a, pythonObject *bb)
  print(a) # (1, 2, 3, 4)
  # 이것이 mutable 이라고 생각하는 사람이 간혹 있다. like me ...
  # 하지만 절대적으로 immutable 이다.
  ```
* tuple + tuple 은 python 내부적으로 tupleconcat 함수를 통해서 처리 한다.
* tuple concat은 두 tuple중 하나가 비었으면 비지 않은 tuple을 반환함.
    ```python
    In [1]: a= (1, 2)

    In [2]: b=()

    In [3]: c = a+b

    # 특이점
    In [4]: id(a)
    Out[4]: 139950111425088

    # 특이점
    In [5]: id(c)
    Out[5]: 139950111425088
    ```
### List
* immutable ?
    ```python
    >>> [1, 2] + [3, 4]
    [1, 2, 3, 4]
    # 의도한 경우일 수 있으나,
    # mutable이지만, immutable로 사용한 예.
    ```
* List들간의 + 연산은 tuple과 다르게 내부적으로 비어있는지 확인하지 않고 두개의 리스트를 더해서 새로운 List를 메모리에 할당한다.
    ```python
    In [12]: a = [1,2]

    In [13]: b = []

    In [14]: c = a+ b

    In [15]: id(a)
    Out[15]: 139950029136128

    In [16]: id(b)
    Out[16]: 139950029105472
    ```
* List extend
    ```python
    # 이런게 써야 mutable 하다.
    In [17]: a = [1,2]

    In [18]: id(a)
    Out[18]: 139950029096448

    In [19]: a.extend([3,4])

    In [20]: id(a)
    Out[20]: 139950029096448
    ```
    * List extend operation
      * O (N + M) [N은 원래 갯수, M은 extend에 추가된 갯수]
      * O(M) [만약 resize를 안할경우]
* List - pop
    ```python
    l = [1, 2, 3, 4, 5]
    print(l.pop(3)) # 4
    
    print(l) # [1, 2, 3, 5]
    # pop 연산을 하면 3번째 데이터를 뺀 다음 그 뒤에 있는 데이터를 앞으로 한칸 밀어야 한다.
    # pop 하나 했을 뿐인데 상당한 operation이 일어난다.
    # O(N)이라는 시간 소요

    ```
    1. list_pop(impl) # list pop 호출
    2. list_ass_slice() # list를 자르는거 호출
    3. memmove() # 메모리 이동 ... 엄청난 move ...

* List - append
    ```python
    In [21]: a
    Out[21]: [1, 2, 3, 4]

    In [22]: a = [1,2,3]

    In [23]: a.append(4)

    In [24]: a
    Out[24]: [1, 2, 3, 4]
    # 과연 O(1)일까?
    ```
* List는 0, 4, 8, 16 , 25, 35, 46, 58, 72, 88 사이즈 일떄마다 list_resize() 한다.
  * 이것을 list -growth pattern 이라고 한다.]
  * 하지만 resize가 계속 일어나면 늘어나는 시간이 점점 더 길어진다.
    > 매번 새로 할당하면 오래 걸림, 공간을 더 낭비하고 시간에서 이득이 생김.
    > 왜 ? 미리 공간을 할당 하니깐.
    > list에 1개의 데이터만 있어도 4개의 공간을 할당함.
* **resize를 방지하면 속도가 개선됨** <-- 중요하다.
    * 미리 할당 : [None] * N
    * append 대신 a[n] = value 를 사용.

### Dictionary
* Hash table
  * Hash function에 Key를 넣어주면 특정 Bucket을 지정해준다.
  * 대부분의 Operation이 O(1) !!! 상수값으로 정리
![](https://www.vladimircicovic.com/content/images/20200502181417-hash_function.jpg)

  * But ... Bucket Size가 문제 <- 이것도 List와 비슷하게 resize 이슈
    * python은 버켓 사이즈를 8로 해놓고 시작한다.
    * 2/3이 차면 Resize (현재 사이즈의 2배씩 사이즈 업)
        >python 내부의 c로 짜여진 코드를 보면  **newsize <<= 1** 이라는 비트 연산을 한다. 이것은 곱하기 2라는 뜻

    * resize하면 벌어지는 일
        1. 새로운 메모리 할당 & 복사
        2. Hash Value를 보고 Bucket과 연결 (Hash Function을 태워야 한다.)
    
    * resize를 최소화 하고 싶다?
      * 미리 크게 할당하고 시작한다.

### 나의 생각
> List와 Dictionary는 python에서 자주 쓰이는 자료구조이다.
> 있으니깐 막 쓰기보다 그 내부 동작이 어떻게 일어나는지, 알고 re_size 되는 시점도 아는것이 상당히 중요하다.
> 이를 알기 위해 **OS의 기본지식**은 필수이다.
> python에서는 우리가 알게 모르게 상당히 많은 부분을 뒤에서 도와준다.
> 사실 메모리 할당 같은 부분은 python 개발하면서 나도 자세하게 몰랐다.
> 그런데 개발 자체를 효율적으로 하려고 하면 언제 어느시점에 메모리에 올라가고 어떤게 메모리에 올라가는지, 또 언제 메모리에서 해제 되는지 ( python도 GC가 있다. reference_count 를 통해 0이 되면 메모리 해제 ... 하지만 GIT 이슈도 있지 ㅠ )