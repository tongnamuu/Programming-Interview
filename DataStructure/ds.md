<h1> Data Structure </h1>

<h2>Array</h2>
배열은 값 또는 변수들의 집합으로 구성된 구조로 하나 이상의 인덱스  혹은 키로 식별한다. 배열은 크기를 지정하고 해당 크기만큼의 연속적인 메모리 공간을 할당받아 작업을 수행한다. 이 때 생성한 배열의 크기는 변경할 수 없다. 이 때 배열 내의 자료는 인덱스를 통해 O(1) 에 접근 가능하다. 추상 자료형(ADT)의 구현은 대부분 배열 혹은 연결 리스트를 기반으로 한다.

<h2>LinkedList</h2>
연결리스트는 데이터 요소의 선형 집합으로 데이터의 순서가 메모리에 물리적인 순서대로 저장되지는 않는다. 동적으로 새로운 노드를 삽입하거나 삭제하는 것이 가능하다. 다만 특정 노드를 찾는데는 배열과 다르게 O(n)이 걸린다.

<h3>자바의 ArrayList vs LinkedList</h3>

||ArrayList|LinkedList|
|------|---|---|
|Implementation|Dynamic Array|Double Linked List|
|Implemented Interfaces|Serializable, Cloneable, Iterable<E>, Collection<E>, List<E>, RandomAccess|Serializable, Cloneable, Iterable<E>, Collection<E>, Deque<E>, List<E>, Queue<E>|
|Delete|삭제할 노드에 접근 O(1), 삭제한 노드 이후의 데이터를 밀어줘야하므로 O(n)|삭제할 노드에 접근O(n), 삭제 연산은 O(1)|


ArrayList 는 List만 implement하지만 LinkedList는 Deque, Queue를 implementation하여 Deque, Queue의 연산을 모두 지원한다.
삭제시에도 ArrayList는 O(1)에 접근, 삭제 후 시프트하기 때문에 O(n)
LinkedList는 O(n)에 접근, 삭제연산은 O(1)이다.

<h2>Stack</h2>
스택은 Last In First Out (후입선출) 로 처리되는 선형 자료구조이다. 스택의 구현은 배열, 혹은 링크드 리스트를 이용해서 할 수 있다.


<h2>Queue</h2>
큐는 First In First Out (선입선출) 로 처리되는 선형 자료구조이다.
큐의 구현은 배열, 혹은 링크드 리스트를 이용해서 할 수 있다.


<h2>Hash Table</h2>
해시함수 : 임의의 크기를 가진 값을 고정 크기의 값에 대응시키는 함수
입력값이 같으면 언제나 출력값도 같아야 함(결정론적)


해시 알고리즘
- 암호학적 해시함수
- 비암호학적 해시함수

해시함수의 응용
- 체크섬, CRC(순환중복 검사)

일반적으로 좋은 해시 함수의 특징
- 해시 함수 값 충돌 최소화
- 쉽고 빠른 연산
- 해시 테이블 전체에 해시 값이 균일하게 분포
- 사용할 키의 모든 정보를 이용하여 해싱
- 해시 테이블 사용 효율이 좋아야 함
  
<h3>로드팩터</h3>

- 해시테이블에 저장된 데이터 개수를 버킷의 개수로 나눈 것
- 로드팩터 비율에 따라 해시함수를 재작성하거나 해시 테이블 크기를 조정


<h3>기타 알아둘 것</h3>

- _해시함수 대신 딥러닝을 이용한 해시 맵_
[*The Case for Learned Index Structures*](https://arxiv.org/abs/1712.01208)
- 균일성 측정 : Chi-squared test



<h2>Graph</h2>
객체의 일부 쌍들이 연관되어 있는 객체 집합 구조를 말한다.

<h3>그래프의 표현</h3>

- 인접리스트 : 연결되지 않은 정점은 저장하지 않으므로 메모리가 효율적
- 인접행렬: 어떤 정점 두개가 연결되어있는지 O(1)에 판단가능

<h3> 그래프의 순회</h3>
- bfs, dfs

제약충족문제(Constraint Satisfaction Problems): 수많은 제약조건을 충족하는 상태를 찾아내는 문제(스도쿠 등)

최단 경로 문제
- 가중치가 음수가 아닌 수로 모두 같다면 bfs를 사용할 수 있다.
- 음의 가중치가 없다면 다익스트라를 사용한다.
- 음의 가중치가 있다면 벨만포드 혹은 spfa를 사용한다.
- 모든 쌍의 최단경로를 찾는 플로이드 와샬이 있다.


<h2>Tree</h2>
트리는 계층형 구조를 표현하는 추상 자료형으로 루트, 부모-자식 관계의 서브트리로 구성되며 서로 연결된 노드의 집합이다. 트리는 순환구조를 갖지 않는 그래프로 결국 그래프의 일종이다. 

Full Binary Tree : 모든 노드가 0개 또는 2개의 자식 노드를 갖는다.
Complete Binary Tree : 마지막 레벨을 제외하고 모든 레벨이 완전히 채워져 있다. 마지막 레벨은 왼쪽부터 채워져 있다.
Perfect Binary Tree : 모든 노드가 2개의 자식 노드를 가지며 모든 리프노드가 동일한 깊이, 레벨을 갖는다.

Binary Search Tree : 어떤노드를 기준으로 노드의 값 보다 작은 값은 왼쪽 자식, 큰 값은 오른쪽 자식으로 보내서 평균적으로 특정한 값을 찾는데 O(log n) 이 걸리지만 트리가 편향되어 있다면 최악으로는 O(n)이 걸리게 된다.
