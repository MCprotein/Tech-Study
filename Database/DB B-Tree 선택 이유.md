# DB B-Tree 선택 이유

수많은 자료구조중에 DB의 인덱스가 B-Tree 자료구조를 선택한 이유

## Tree 자료구조
![image](https://user-images.githubusercontent.com/89785501/185393084-943f15a4-6242-481c-9109-0bac968756e1.png)

- 시간복잡도 평균 O(logN)
![image](https://user-images.githubusercontent.com/89785501/185393097-39f19396-4095-46d3-a0fe-0da38cb7830c.png)

- 최악의 경우에 O(N) -> 이것을 방지하기 위해 Balanced Tree 사용

## 탐색시간이 제일 빠른 Hash Table을 DB 인덱스로 사용할 수 없는 이유

- 단 하나의 데이터를 탐색하는 시간만 O(1) 이다.
- 모든 값이 정렬되어 있지 않으므로, 해시 테이블에서는 특정 기준보다 크거나 작은 값을 찾을 수 없다. 찾아도 O(1)을 보장할 수 없고 매우 비효율적이다.
- 그래서 기준 값 보다 크거나 작은 요소들을 항상 탐색할 수 있어야 하는 DB 인덱스 용도로 해시테이블은 어울리지 않는 자료구조이다.

## Balanced Tree

- 트리의 노드가 한 방향으로 쏠리지 않도록 노드 삽입 및 삭제 시 특정 규칙에 맞게 재 정렬되어 왼쪽과 오른쪽 자식 양쪽 수의 밸런스를 유지하는 트리이다. 항상 양쪽 자식의 밸런스를 유지하므로, 무조건 O(logN)의 시간 복잡도를 가지게 된다. 다만 재정렬되는 작업으로 인해 노드 삽입 및 삭제 시 일반적인 트리보다 성능이 떨어진다. 그러므로 밸런스 트리는 삽입/삭제의 성능을 희생하고 탐색에 대한 성능을 높였다고 볼 수 있다.
- RedBlack-Tree, B-Tree

### RedBlack-Tree
![image](https://user-images.githubusercontent.com/89785501/185393147-69fdb045-0944-4286-94dc-0553a34c06b1.png)

- 노드가 하나의 값만 가지고, 좌우 자식 노드의 개수 밸런스를 맞춘다.

### B-Tree
![image](https://user-images.githubusercontent.com/89785501/185393152-6c14a45a-4ce7-4e84-8f3d-924f1c0ca26d.png)

- 노드 하나에 여러 데이터가 저장될 수 있다. 각 노드 내 데이터들은 항상 정렬된 상태이며, 데이터와 데이터 사이의 범위를 이용하여 자식 노드를 가진다. 그러므로 자식 노드 개수는 n+1이다.

위 두개의 트리는 항상 좌, 우 자식노드 개수의 밸런스를 유지하므로 최악의 경우에도 무조건 탐색 시간이 O(logN)이다.

### RedBlack-Tree가 DB 인덱스로 선택받지 못한 이유

위 두 트리의 가장 큰 차이는 하나의 노드가 가지는 데이터의 개수 이다.
Redblack-Tree는 무조건 하나의 노드에 하나의 데이터 요소만을, B-Tree는 하나의 노드에 여러개의 데이터 요소를 저장한다.

#### B-Tree 배열 데이터 접근 방식

B-Tree는 같은 노드 공간의 데이터끼리 참조 포인터 값으로 접근할 필요가 없다.
실제 메모리 디스크에서 바로 다음 인덱스에 접근하는 것이다.
예를들어 200 값을 찾기 위해 100, 155, 226이 저장되어 있는 Root 노드를 탐색한다. 200이 없기 때문에 155와 226사이의 자식노드에서 200을 찾으면 된다.

#### Redblack-Tree 데이터 접근 방식

각 노드마다 무조건 하나의 데이터만 가지게 되므로 모든 데이터에 접근할 때 무조건 참조 포인터로 접근한다.
참조 포인터로 데이터에 접근하기 위해 접근하려는 주소를 연산을 통해 직접 알아내야 한다. B-Tree도 자식 노드에 접근할 때는 참조 포인터로 접근하지만, 하나의 노드가 가지는 데이터 개수가 많아질수록 포인터 개수는 줄고, 트리 내에서 다루는 데이터가 많아질수록 이러한 차이는 커질 것이다.

<br>
같은 O(logN)이지만, 포인터 접근 수의 차이로 인해 B-Tree가 RedBlack-Tree보다 탐색 시간이 더 빠를 수밖에 없다.
B-Tree에서 노드 내 데이터 개수는 상수이기 때문에 노드 탐색 시간은 시간 복잡도 계산에서 제외된다.

### 배열이 DB 인덱스로 선택받지 못한 이유
![image](https://user-images.githubusercontent.com/89785501/185393203-f2958b90-1d65-40bd-9f1c-6c7d82ee117d.png)

B-Tree보다 빠른 것은 탐색뿐이다.
배열 내에서 데이터 저장, 삭제는 훨씬 비효율적이다.
데이터의 중간에 데이터를 삽입하면, 그 데이터 뒤의 데이터들은 한칸씩 뒤로 물러나게 된다. 이 과정은 평균 시간복잡도가 O(N)이다. 삭제할때도 마찬가지이다. 심지어 수정을 할 때에는 정렬을 해야되서 O(N\*logN)이다.

## B-Tree가 가장 적합하다.

1. 항상 정렬된 상태로 특정 값보다 크고 작은 부등호 연산에 문제가 없다.
2. 참조 포인터가 적어 방대한 데이터 양에도 빠른 메모리 접근이 가능하다.
3. 데이터 탐색뿐 아니라, 저장, 수정, 삭제에도 항상 O(logN)의 시간 복잡도를 가진다.

## LinkedList가 DB 인덱스로 선택받지 못한 이유
![image](https://user-images.githubusercontent.com/89785501/185393227-4f169a4c-65fd-4351-9e22-cd9712f777bb.png)

- 탐색을 무조건 제일 앞부분인 HEAD에서부터 시작해야 하기 때문에 비효율적이다.
- 즉, Index Number로 해당 요소에 바로 접근할 수 없다.

#### 출처

- https://helloinyong.tistory.com/296
