# Singleton, IoC, DI

## 싱글톤

클래스의 인스턴스를 한개만 생성하여 사용하는 것

### 장점

- 인스턴스 생성을 한 번만 하면 되서 메모리 낭비 줄일 수 있다.
- 인스턴스의 정보가 변경되더라도 한 개의 인스턴스를 사용하기 때문에 동기화의 문제가 없다.
- 인스턴스의 상태가 모두 동일하기 때문에 어디서 사용하더라도 같은 결과를 확인할 수 있다.

### 단점

객체간 결합이 강해진다.

## 도서관 예시

1. 도서관에서 책을 빌려서 볼 수 있기 때문에 내가 구매해야하는 비용을 줄일 수 있다.
2. 새로운 책이 입고되었을 경우 어떤 책인지 확인할 수 있다.
3. 이미 다른사람이 책을 보고 있을 경우 난 그 책을 볼 수 없다.
   책과 사람은 서로 의존하고 있는 상태이다. -> 강한 결합

- 해결 방법

1. 책을 사지 않아야 한다.
2. 다른 사람이 책을 보고 있어도 나 또한 볼 수 있어야 한다.
3. 신간이 들어오는 것을 언제나 확인할 수 있어야 한다.<br>
   이것을 해결하기 위해 의존성 주입(DI, Dependency Injection)이라는 용어를 사용한다.

### 도서관에서 의존성 주입(DI)

책과 사람의 의존성을 느슨하게 만들어야 한다. 이것을 느슨한 결합이라고 한다.

- 해결방법
  책을 E-Book의 형태로 만든다.<br>
  그리고 도서관 앱을 통해 E-Book을 대여한다.<br>
  여기서 제어의 역전(IoC, Inversion Of Control)의 개념이 나온다.<br>
  Before: 사람이 직접 책을 다룬다.<br>
  After: 도서관 앱이 책을 관리한다.<br>
  개발에서는 개발자가 권한을 가지고 있었는데, 프레임워크가 그 권한을 행사하는 것을 뜻한다.<br>
  예) Nest에서 Provider, Spring에서 Bean container<br>

앱이 책을 관리하기 때문에, 앱에 책의 정보를 입력해야 한다. 이것을 의존성 주입(DI)이라고 한다. <br>
객체지향 프로그래밍 OOP에서 중요하게 여기는 SOLID원칙을 따르기 위한 방법이 의존성 주입(DI)을 통한 싱글톤 패턴이다.

## 결론

- 싱글톤을 사용하는 이유
  - 인스턴스를 재사용하기 위해
- IoC가 필요한 이유
  - 강한 결합을 약한 결합으로 바꾸기 위해
  - 개발자 대신 프레임워크가 인스턴스를 관리하기 위해
- DI가 필요한 이유
  - 프레임워크가 인스턴스를 관리하기 위해서는 기존의 인스턴스에 대한 정보를 주입해줘야 한다.

#### 출처

- https://velog.io/@yukina1418/OOP%EC%97%90%EC%84%9C-IoC%EC%99%80-DI%EA%B0%80-%EC%A4%91%EC%9A%94%ED%95%9C-%EC%9D%B4%EC%9C%A0%EC%97%90-%EB%8C%80%ED%95%98%EC%97%AC-%EC%9E%91%EC%84%B1%EC%A4%91
