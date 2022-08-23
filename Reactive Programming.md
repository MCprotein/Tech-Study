# Reactive Programming

## 비동기 작업이 필요한 이유

사용자 경험을 향상시키기 위해 필요하다.<br>
메인스레드가 멈추거나 느려지지 않도록 하여 사용자들에게 부드러운 사용자 경험과 좋은 성능을 제공해야 한다.<br>
메인 스레드를 자유롭게 유지하려면, 무겁고 시간이 오래 걸리는 작업은 백그라운드에서 해야한다.<br>
또한, 그러한 작업은 서버에서 수행되어야 한다. 그래서 네트워크 운영을 위한 비동기 작업이 필요하다.

### 비동기작업을 처리하는 라이브러리에서 필요한 평가 매트릭스 4가지

1. 명시적 실행 (Explicit execution)
   새로운 스레드에서 작업을 시작하면, 그것을 컨트롤할 수 있어야 한다. <br>
   백그라운드 작업을 수행하려면, 정보를 모으고 준비해야 한다. 준비가 되면 바로 백그라운드 작업을 시작할 수 있다.

2. 쉬운 스레드 관리 (Easy thread management)
   비동기 작업에서는 스레드 관리가 핵심이다. 백그라운드 작업 도중이나 작업이 끝난 후, 메인 스레드에서 UI를 업데이트해야 할 때가 있다. 우리의 작업을 하나의 스레드(백그라운드 스레드)에서 다른 스레드(메인 스레드)로 넘겨야 한다. 그래서 스레드를 쉽게 전환하고, 필요한 경우 다른 스레드로 작업을 넘길 수 있어야 한다.

3. 쉬운 구성력(Easily composable)
   이상적으로, 비동기 작업을 생성하고 백그라운드 작업을 시작하면 다른 스레드(특히 UI스레드)에 의존하지 않고 작업이 끝날때까지 다른 스레드와는 독립적으로 유지하는 것이 좋다. 하지만 실제로는 UI를 업데이트하고 데이터베이스를 변경하는 등 많은 것들을 스레드 상호 의존적으로 만들어야 한다. 그래서 비동기 라이브러리는 쉽게 구성할 수 있어야 하고 오류가 발생할 여지가 적어야 한다.

4. 부작용 최소화(Minimum the side effects)
   멀티 스레드가 수행되는 동안, 스레드 간 영향을 끼치는 부작용을 최소화해야 한다. 그것은 새로운 사람이 당신의 코드를 쉽게 읽고 이해할 수 있게 해주고, 오류를 쉽게 추적할 수 있게 한다.

## 리액티브 프로그래밍이란?

<b>리액티브 프로그래밍은 데이터흐름과 변화 전파에 중첩을 둔 프로그래밍 패러다임이다. 이것은 프로그래밍 언어로 정적 또는 동적인 데이터 흐름을 쉽게 표현할 수 있어야 하며, 데이터 흐름을 통해 하부 실행 모델이 자동으로 변화를 전파할 수 있는 것을 의미한다.</b> - 위키피디아

Rx프로그래밍에서 데이터 흐름은 하나의 컴포넌트로부터 전파되며, Rx 라이브러리에서 제공하는 하부 구조가 '데이터 변화 수신자'로 등록된 다른 컴포넌트에게 변화를 전파한다. Rx는 세가지 키포인트로 구성된다.
`RX = OBSERVABLE + OBSERVER + SCHEDULERS`

### Observable

Observable은 데이터 스트림일 뿐이다. 하나의 스레드에서 다른 스레드로 전달 할 데이터를 압축한다. 주기적으로 또는 설정에 따라 생애주기동안 한 번만 데이터를 방출한다. Observer가 특정 이벤트를 기반으로 특정 데이터를 방출하도록 돕는 다양한 연산자가 있다. 즉, 데이터를 처리하고 다른 구성요소에 전달하는 공급자라고 보면 된다.

### Observer

Observer는 Observable에 의해 방출된 데이터 스트림을 소비한다. subscribeOn() 메서드를 사용해서 observable을 구독하고, observable이 방출하는 데이터를 수신할 수 있다. observable이 데이터를 방출할 때 마다 등록된 모든 observer는 onNext() 콜백으로 데이터를 수신한다. 여기에서 JSON 응답 파싱이나 UI 업데이트와 같은 다양한 작업을 수행할 수 있다. observable에서 에러가 발생하면, observer는 onError()에서 에러를 수신한다.

### Scheduler

Rx는 비동기 프로그래밍을 위한 것이고, 우리는 스레드 관리가 필요하다. Scheduler는 Observable과 Observer에게 그들이 실행되어야 할 스레드를 알려주는 Rx의 구성요소이다. observeOn() 메서드로 observer와 observable에게 관찰해야 할 스레드를 알려줄 수 있다. Schedulers.newThread()와 같이 RxJava에서 제공된 메인 기본 스레드는 새로운 백그라운드를 생성한다. Schedulers.io()는 IO 스레드에서 코드를 실행할 것이다.

#### 예제코드

```java
// Observable 생성
Observable<String> database = Observable      //Observable. This will emit the data
                .just(new String[]{"1", "2", "3", "4"});    //Operator

// Observer 생성
 Observer<String> observer = new Observer<String>() {
           @Override
            public void onCompleted() {
                //...
            }

            @Override
            public void onError(Throwable e) {
                //...
            }

            @Override
            public void onNext(String s) {
                //...
            }
        };

// 스케줄 정의
database.subscribeOn(Schedulers.newThread())          //Observable runs on new background thread.
        .observeOn(AndroidSchedulers.mainThread())    //Observer will run on main UI thread.
        .subscribe(observer);                         //Subscribe the observer
```

1. 데이터를 방출하는 observable 생성
   여기서 database가 데이터를 방출하는 observable이고, strings를 방출한다. just()는 기본으로 제공된 데이터를 하나씩 방출하는 메서드이다.

2. 데이터를 소비하는 observer 생성
   위 코드에서 observer는 database observable이 발행한 데이터와 에러를 내부에서 처리한다.

3. 동시성 관리
   마지막 단계에서 동시성을 관리하는 스케줄을 정의한다.
   `subscribeOn(Schedulers.newThread()`은 database observable에게 백그라운드 스레드에서 실행하라고 하는 것이다. <br>
   `observeOn(AndroidSchedulers.mainThread())`은 observer에게 메인 스레드에서 실행하라고 말한다. 이것이 리액티브 프로그래밍의 기본 코드이다.

#### 출처

- https://dev-daddy.tistory.com/25
