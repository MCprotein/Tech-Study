# 샤딩

- 사전적 의미 : 조각 또는 파편
- 한 테이블의 row들을 서로 다른 테이블, 즉 파티션(물리적)으로 분리하는 것이다.
- Horizontal Partitioning했다고 볼 수 있다.

![image](https://user-images.githubusercontent.com/89785501/185943714-6a0e94a8-b851-4845-9b66-2b2dfe4d15cf.png)

- 샤딩은 보통 하나의 테이블에 넣기에 데이터 양이 방대하거나 관리하기에 어려워질때 적용한다.
- 기존에 하나의 테이블로 구성될 스키마를 다수의 복제본으로 구성하고, 각각의 샤드에 어떤 데이터가 저장될지를 샤드키를 기준으로 분리한다.
- 주로 application level에서 실행되며 어떤 샤드로 읽기와 쓰기를 전송할지 정의하는 코드를 가지게 된다.
- 샤딩 기능이 내장된 데이터베이스에서는 database level에서 실행될 때도 있다.

## 장단점

### 장점

- Scale-Out이 가능
- 스캔 범위를 줄여서 쿼리 반응 속도를 빠르게 한다.
- 장애가 샤드 단위로 발생한다.

### 단점

- 프로그래밍 복잡도 증가
- 데이터가 한 쪽 샤드로 몰릴 경우 (Hotspot), 샤딩이 무의미해짐
- 잘못 사용할경우 리스크가 크다.
- 한 번 샤딩 사용시 샤딩을 사용하기 전 구조로 돌아가기 힘들다.

## 대체 방법

- db서버의 Scale-up
- Read의 부하가 클 경우 Cache 사용 및 Database Replication
- 테이블의 일부 컬럼만 주로 사용할 경우 Vertical Partitioning

## 여러가지 샤딩 방식

데이터를 분산시키는 방법, 읽는 방법은 샤드키를 어떻게 정하는지에 따라 달라진다.
최대한 데이터를 균일하게 분산하기 위한 방법이 몇가지 있다.

### Hash Sharding

데이터의 id를 hashing하여 샤딩하는 방식이다. 즉, 샤드키를 id%num하여 정한다. <br>
다만 한 번 나눌 개수를 정해놓으면 수정하지 못한다는 단점이 있고, 단순한 hashing방식이기 때문에 데이터가 저장되는 공간적 효율에 대한 고려는 할 수 없다.

![image](https://user-images.githubusercontent.com/89785501/185943759-ccfaddd7-ba3a-4232-b0c2-783114452559.png)

### Dynamic Sharding

일종의 Locator Service를 통하여 샤드키를 정한다. <br>
확장에 유연하지만 데이터를 재배치할 때 Locator Service도 변환해야되서 의존성이 있다는 단점이 있다.
![image](https://user-images.githubusercontent.com/89785501/185943783-f6901b67-cc0f-41e8-bfb5-bf73fd8b64be.png)

### Entity Group

동일한 파티션에 관련 엔티티를 저장하여 단일 파티션 안에서 추가 기능을 제공하는 방식이다. <br>
즉, 관련된 데이터에 대해 하나의 샤드를 이용하는 방식이다. <br>
하나의 물리적인 샤드를 사용하기 때문에, 쿼리 진행시 효과적이며 응집도가 높아진다는 장점이 있다. <br>
하지만 다른 샤드와 관련된 쿼리를 사용하게 된다면, 샤드를 사용하지 않았을 때보다 성능이 더 떨어질 수 있다는 단점이 있다.
![image](https://user-images.githubusercontent.com/89785501/185943822-325fb77f-9614-4c10-b732-8c85dda9c046.png)

#### 출처

- https://minkwon4.tistory.com/317
