# Like 대신 Full Text Search

일반적으로 DB에서 특정 단어가 포함된 레코드를 찾을 때 LIKE를 사용한다.
하지만 LIKE는 '%단어%' 형태로, 인덱스를 사용하지 않아 데이터가 대략 10000건이 넘어가면 속도가 느려진다고 한다. 그래서 Full Text Search 방법에 대해 찾아보았다.

### MySQL의 B-Tree index (일반적인 Index)

한 컬럼 안에서 비슷한 형식의 데이터 중에서 원하는 데이터를 찾을 때 사용

### Full Text (Search) Index

한 컬럼 안에 많은 형태의 데이터가 담겨 있어서 효율적으로 데이터를 찾는 경우 사용

- 사용자가 쓰기 나름인 데이터를 토큰 단위로 쪼개서 검색에 용이하게 한다.

## 데이터 인덱싱 하는 방법

- 검색하게 될 키워드를 빠르게 검색할 수 있도록 두 종류의 parser를 이용하여 인덱스를 구축한다.

### Built-in parser 또는 Stop-word parser

- Stop-word 기법을 사용하는 build-in parser를 이용한다.
- Stop-word는 토큰을 나눌 때 해당하는 Stop-word가 나타나는 것을 기준으로 토큰을 나눈다.
- 예시

```
Input : "나는 매일이 지옥 같았습니다.”
Stop-word : “ ”(공백) => 공백은 디폴트 값입니다.
Token : 나는 | 매일이 | 지옥 | 같았습니다.
```

공백을 기준으로 4개의 토큰이 인덱싱 되었다.

### N-gram parser

- n-gram 기법을 사용하여 할당한 토큰의 크기 n만큼 데이터를 인덱스로 파싱해두었다가 사용하는 parser
- 예시

```
N-gram : n=2
// 나는 | 는  |  김 | 김치 | 치찌 | 찌개 | 개가 | 개  |  좋 | 좋습 | 습니 | 니다 | 다.
// 중에서 Token 2가 아닌 “는 “,” 김”, “개 “, “ 좋” 은 제거된다.

Token : 나는 | 김치 | 치찌 | 찌개 | 개가 | 좋습 | 습니 | 니다 | 다.
```

#### N-gram Full Text Index 생성 과정

1. 인덱스 대상 문서를 n글자로 구성된 서브 그룹으로 나눈다.
![image](https://user-images.githubusercontent.com/89785501/185383057-0cfcee12-c4d1-43f8-857e-37a9f315313c.png)
- 서브그룹 생성 방식은 해당 문서 데이터의 왼쪽을 시작으로 4글자 토큰을 만들고, 토큰의 마지막 글자부터 다시 4글자 토큰을 만들어서 데이터의 끝까지 생성한다.

2. 1단계에서 추출된 서브그룹중에 중복되는 서브그룹별로 토큰을 분류하고, 백엔드 인덱스를 만든다.
![image](https://user-images.githubusercontent.com/89785501/185383078-4b6bf162-9a73-45e3-9628-e5ec763f12fc.png)

- 중복되지 않는 4글자로 구성된 서브그룹 리스트를 만든다.
- 백엔드 인덱스에 문서위치를 생성한다. 문서위치 = (문서#, 문서열#)

3. 백엔드 인덱스에서 중복되지 않은 서브그룹에서 다시 한 번 n-gram용 서브그룹을 만든다.
![image](https://user-images.githubusercontent.com/89785501/185383122-fe9bc69d-9e9f-412c-a39f-25488ae38ef2.png)

- 각 서브그룹을 다시 한 번 2-gram 방식으로 2글자 토큰으로 나눈다.
- n-gram 토크나이징 방식은 왼쪽부터 한 글자씩 오른쪽으로 이동하면서 연속된 n글자로 토큰을 구성한다.
- 예시

```
n=2    =>    가나,나다,다라,라마
n=3    =>    가나다,나다라,다라마
n=4    =>    가나다라,나다라마
```

4. 3단계에서 생성된 2-gram 토큰을 분류하고 중복되지 않은 2-gram 서브 그룹을 생성한다.
![image](https://user-images.githubusercontent.com/89785501/185383140-b264e998-36d5-4c79-813b-85ee5166a9bd.png)

5. 4단계에서 토크나이징한 서브그룹 정보를 이용해서 프론트엔드 인덱스를 생성한다.
![image](https://user-images.githubusercontent.com/89785501/185383155-683e2ba4-bbee-44ea-9699-970a417e54a9.png)

- 프론트엔드 인덱스에서 서브 그룹과 그룹 위치로 구성한다. (서브그룹번호, 서브그룹열번호)

#### N-gram Full Text Index 검색 과정

1. 인덱스의 검색 과정은 생성 과정과 반대로 입력된 검색어를 n-gram의 n바이트 단위로 동일하게 자른다.

- "마바사"를 검색할 때 "마바", "바사"로 자른다.

2. 동일하게 자른 검색용 n-gram 토큰을 이용해서 프론트엔드 인덱스를 검색한다.
![image](https://user-images.githubusercontent.com/89785501/185383197-65623271-7bda-4e52-9530-12e58a4a4b7f.png)

- 프론트엔드 인덱스에서 마바(1,1), 바사(1,2) 정보를 검색했다면, 두 토큰 모두 서브그룹1과 연관

3. 검색된 결과 정보를 백엔드 인덱스의 검색 대상 후보 리스트로 두고, 백엔드 인덱스를 통해 최종 확인을 거쳐 일치하는 결과를 가져온다.
![image](https://user-images.githubusercontent.com/89785501/185383206-828b74ab-18aa-4dcc-8c70-aa6517ed15d4.png)

- 프론트엔드 인덱스에서 찾아낸 서브그룹1("라마바사")로 문서 위치를 찾는다.
- 최종적으로 "마바사"의 위치는 서브그룹 정보1인 "라마바사"의 정보 (0,1), (1,2)에서 행정보인 문서 0과 1을 찾는다.

### Stop-word parser와 N-gram parser의 비교

1. 검색 속도

- 검색어 바이트 수가 많아질수록, 쿼리 실행 시간이 stop-word parser > N-gram parser이다. 즉, N-gram 검색이 빠르다.

2. 인덱스 용량

- 저장 데이터 바이트 수가 많아질수록, 인덱스 스토리지 사용량은 Stop-word parser < N-gram parser이다. 즉, N-gram이 더 많은 용량을 사용한다.

### Full-Text Search 검색 쿼리

- `SELECT * FROM "테이블명" WHERE MATCH("검색할컬럼명"[, ...]) AGAINST('"검색할키워드식" "검색모드");`

### 검색 모드의 정류

1. 자연어 검색

- 검색 문자열을 단어 단위로 분리한 후 해당 단어 중 하나라도 포함되는 행을 찾는다.
- `SELECT * FROM “테이블명" WHERE MATCH (“검색컬럼명") AGAINST (‘맛집' IN NATURAL LANGUAGE MODE);`

2. boolean mode search

- 검색 문자열을 단어 단위로 분리한 후, 해당 단어가 포함되는 행을 찾는 규칙을 추가적으로 적용하여 해당 규칙에 매칭되는 행을 찾는다.
- 검색의 정확도에 따라 결과가 정렬되지 않는다.
- 구문 검색이 가능하다.
- 필수(+), 예외(-), 부분(\*), 구문(" ") 연산자를 사용할 수 있다.
- `SELECT * FROM “테이블명" WHERE MATCH (“검색컬럼명") AGAINST (‘+대구*+닭*+맛집*' IN BOOLEAN MODE);`

3. 쿼리 확장 검색(query extension search)

- 2단계에 거쳐 검색을 수행한다. 1단계에서는 자연어 검색을 수행한 후, 첫 번째 검색의 결과에 매칭된 행을 기반으로 검색 문자열을 재구성하여 두 번짹 검색을 수행한다. 이는 1단계 검색에서 사용한 단어와 연관성이 있는 단어가 1단계 검색에 매칭된 결과에 나타난다는 가정을 전제로 한다.

#### 출처

- https://interconnection.tistory.com/95