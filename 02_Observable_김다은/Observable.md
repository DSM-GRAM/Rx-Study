# Observable

### Observable이란?

> 리액티브 프로그래밍은 외부환경이 애플리케이션에 데이터를 밀어넣는 방식인 푸시 시나리오로 동작하는 애플리케이션을 작성하는 것이다.
>
> 즉, 필요한 데이터를 획득하기 위해 애플리케이션이 외부 환경에 요청하여 데이터를 획득하는 것이 아니라, 애플리케이션은 외부 환경을 관찰하고 있다가 외부 환경에서 데이터 스트림을 방출하면 그것에 반응하여 데이터를 획득하는 것이다.

이때

 **외부 환경에서 애플리케이션 내부로 연속적으로 흐르는 데이터(데이터 스트림)를 생성하고 방출하는 객체를** 

**<u>옵저버블(Observable)</u>**이라 하고, 옵저버블이 방출한(emit) **노티피케이션**(Notification : 옵저버블이 방출할 수 있는 푸시 기반 이벤트 또는 값)을 획득하여 사용하는 객체를 **옵저버**(Observer)라 한다. 

다시 말해 데이터 소비자인 옵저버는 데이터 생산자인 옵저버블을 **구독**(subscription)한다. 이 구독에 의해 옵저버는 옵저버블에 연결되어 옵저버블의 상태를 관찰한다.

그리고 옵저버블이 방출한 노티피케이션은 옵저버에게 자동으로 전파된다.

------

### Observer 생성

<u>비동기 모델에서의 메서드 호출 흐름</u>

1. 비동기 메서드 호출로 결과를 리턴받고 필요한 동작을 처리하는 메서드(옵저버의 일부)를 정의
2. Observerble로 비동기 호출 정의
3. 구독을 통해 옵저버를 Observable 객체에 연결 (동시에 Observable의 동작을 초기화)
4. 필요한 코드를 계속 구현, 메서드 호출로 결과가 리턴될 때마다, 옵저버의 메서드는 리턴 값 또는 Observable이 배출하는 항목들을 사용해서 연산 시작

흐름을 코드로 구현

```
// 옵저버의 onNext 핸들러를 정의한다, 하지만 실행하지는 않는다
// (이 예제에서는, 단순히 옵저버에 onNext 핸들러만 구현한다)
def myOnNext = { it -> /* 필요한 연산을 처리한다 */ };
// Observable을 정의하지만, 역시 실행하지는 않는다
def myObservable = someObservable(itsParameters);
// 옵저버가 Observable을 구독한다. 그리고 Observable을 실행한다
myObservable.subscribe(myOnNext);
// 필요한 코드를 구현한다
```

------

### Subscribe 메서드

Subscribe 메스드를 통해 옵저버와 Observable을 연결한다.

- ##### onNext

  - Observable은 새로운 항목들을 배출할 때마다 이 메서드를 호출한다. 
  - 이 메서드는 Observable이 배출하는 항목을 파라미터로 전달받는다.

- ##### onError

  - Observable은 기대하는 데이터가 생성되지 않았거나 다른 이류로 오류가 발생할 경우 오류를 알리기 위해 이 메서드를 호출한다.
  - 이 메서드가 호출되면 onNext나 onCompleted는 더 이상 호출되지 않는다.
  - 오류정보를 저장하고 있는 객체를 파라미터로 전달 받는다.

- ##### onCompleted

  - 오류가 발생하지 않았다면 Observable은 마지막 onNext를 호출한 후 이 메서드를 호출한다.

Observable 계약(*Observable* Contract)에 명시된 조건에 따르면

> onNext는 0번 이상 호출 될 수 있으며 그 후에는 onCompleted 또는 onError 둘 중 하나를 마지막으로 호출한다.
>
> **단, 이 둘 모두를 호출하지는 않는다.**
>
> onNext 호출 :  항목의 "배출" 
>
> onCompleted 혹은 onError 호출 : "알림"

Subscribe 호출 예제(의사코드)

```
def myOnNext     = { item -> /* 필요한 연산을 처리한다 */ };
def myError      = { throwable -> /* 실패한 호출에 대응한다 */ };
def myComplete   = { /* 최종 응답 후 정리 작업을 한다 */ };
def myObservable = someMethod(itsParameters);
myObservable.subscribe(myOnNext, myError, myComplete);
// 필요한 코드를 계속 구현한다
```

------

### 구독해지

현재 구독 중인 Observable 중, 옵저버가 더 이상 구독을 원하지 않는 경우에는 **Subscriber**라는 특별한 옵저버 인터페이스가 제공하는 **<u>unsubscribe</u>**라는 메서드를 호출해서 구독을 해지할 수 있다.

> 만약 더 이상 관심있는 다른 옵저버가 존재하지 않는다면 Observable들은 새로운 항목들을 배출하지 않는다.

unsubscibe는 연산자 체인을 통해 옵저버가 구독하고 있었던 Observable들이 더 이상 항목을 배출하지 못하도록 체인 안에 연결된 링크들을 끊어 버린다.

------

### Hot, Cold Observable

Observable은 연속된 항목을 언제 배출할까?

> Observable에 따라 다르게 배출한다.

- Hot Observable :  생성되자마자 항목들을 배출하기도 함
  - 이 Observable을 구독하는 옵저버들은 어떤 경우에는 항목들이 배출되는 중간부터 Observable을 구독할 수 있다.
- Cold Observable : 옵저버가 구독할 때까지 항목을 배출하지 않음
  - 이 Observable을 구독하는 옵저버는 Observable이 배출하는 항목 전체를 구독할 수 있도록 보장 받는다.

------

### Observable 계약

- Observable 계약 : Observable을 공식적으로 정의하려는 시도
- 알림
  - Observable은 알림들을 통해 옵저버와 커뮤니케이션 한다.
    - OnNext , OnCompleted, OnError, OnSubscribe(선택사항)
  - 옵저버는 아래의 알림들을 통해 Observable과 커뮤니케이션 한다.
    - Subscribe,  Unsubscribe,  Request(선택사항)
  - 알림원칙에 관한 계약
    - Observable은 0개 이상의 OnNext 알림을 만들고 각각의 알림은 배출된 하나의 항목을 나타내며 OnCompleted나 OnError 알림 중 하나를 배출한다.
    - 단, OnCompleted와 OnError 알림을 둘다 만들지는 않고, OnCompleted나 OnError 알림을 배출한 후에는 다른 알림을 배출하지 않는다.
    - Observable은 아무 알림도 배출하지 않거나, OnCompleted 또는 OnError 알림만 배출할 수 있으며, OnNext 알림만 배출할 수 있다.
    - Observable은 반드시 순차적으로 옵저버에게 알림을 보낸다. 그리고 이 알림은 다른 스레드를 옹해 전달 되기도 한다. 하지만, 공식적으로 알림 간에는 전후관계가 존재한다.
  - Observable 종료
    - 만약 Observable이 OnCompleted나 OnError 알림을 보내지 않는다면 옵저버는 Observable이 여전히 실행 중이라고 판단하고 알림을 보내기도 한다.
    - Observable이 OnCompleted나 OnError 알림을 발행하면 Observable은 자원을 반환하거나 실행을 종료시키고, 이때 옵저버는 더 이상 Observable과의 커뮤니케이션을 시도하면 안된다.
    - OnError 알림은 반드시 발생한 오류에 대한 정보를 포함하고 있어야 한다.
    - Observable은 종료 전에 Observable을 구독 중인 모든 구독자에게 OnCompleted나 OnError 알림 중 하나를 꼭 보내야 한다.
  - 구독과 해지
    - Observable은 옵저버로부터 Subscribe 알림을 받는 순간, 즉시 옵저버에게 알림을 보낼 수 있다.
    - 옵저버가 Observable에게 Unsubscribe 알림을 보내면, Observable은 더 이상 옵저버에게 알림을 보내지 않을 것이다.
    - 하지만, 옵저버가 Unsubscribe 알림을 보낸 후라 해도 구독 해지는 보장되지 않을 수 있다.
    - Observable이 OnError나 OnCompleted를 옵저버에게 보내면 구독은 종료된다.
    - 이 경우, 옵저버는 더 이상 구독을 해지하기 위해 Observable이 Unsubscribe 알림을 보낼 필요가 없다.
  - 다중 Observer
    - 이미 첫 번째 옵저버에게 항목을 배출 중인 Observable을 두 번째 옵저버가 구독하려 했을 때, 이후 부터의 동작은 전적으로 Observable이 결정한다.
      - 이때 Observable이 선택할 수 있는 옵션
        - 두 번째 옵저버가 구독을 시작하는 시점부터 발행 중인 항목을 동일하게 두 번째 옵저버에게도 배출
        - 아예 처음부터 두 번째 옵저버에게 항목을 다시 배출
        - 아예 다른 순서대로 항목을 두 번째 옵저버에게 배출
      - 일반적으로 같은 Observable을 두 개의 옵저버가 구독하는 경우, 동일한 순서대로 옵저버가 항목들을 구독 받도록 보장 할 수는 없다.