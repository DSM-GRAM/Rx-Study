# Subject란?

- obeservar나 observable처럼 행동하는 ReactiveX의 일부 구현체에서 사용 가능한 일종의 교각 혹은 프록시
- 옵저버이기 때문에 하나 이상의 Observable을 구독할 수 있음.
- 동시에 Observable이기 때문에 항목들을 하나하나 거쳐 재배출하고 관찰하며 새로운 항목들을 배출할 수 있다.



## AsyncSubject

~~~swift
let bag = DisposeBag()

var asyncSubject = AsyncSubject<String>()

asyncSubject.onNext("veryfirst value")

asyncSubject.subscribe{(event) in
    print(event)
}

asyncSubject.onNext("first value")
asyncSubject.onNext("second value")
asyncSubject.onCompleted()
// --- 출력 ---
// next(second value)
// completed
~~~

- Complete 될때까지 이벤트는 발생되지 않음.
- Complete가 되면 마지막 이벤트를 발생하고 종료된다.



## PublishSubject

~~~swift
let bag = DisposeBag()
var publishSubject = PublishSubject<String>()
publishSubject.onNext("very first value")
publishSubject.subscribe { (event) in
    print(event)
}
publishSubject.onNext("first value")
publishSubject.onNext("second value")
publishSubject.onError(TestError.test)
publishSubject.onNext("very last value")
publishSubject.onCompleted()
// --- 출력 ---
// next(first value)
// next(second value)
// error(test)
~~~

- PublishSubject는 subscribe 전의 이벤트는 emit하지 않음.
- Subscribe한 후의 이벤트만을 emit.
- 에러 이벤트가 발생하면 그 후의 이벤트 emit 하지 않음.



## BehaviorSubject

~~~swift
var behaviorSubject = BehaviorSubject(value: "initial value")
behaviorSubject.onNext("changed value")

behaviorSubject.subscribe{ (event) in
    print(event)
}

behaviorSubject.onNext("first value")
// --- 출력 ---
// next(changed value)
// next(first value)
// completed
~~~

- PublishSubject와 거의 유사.
- BehaviorSubject는 반드시 값으로 초기화를 해주어야 함.
- 따라서 Observer에게 subscribe하기 전 마지막 이벤트 혹은 초기 값을 emit함.



## ReplaySubject

~~~swift
let bag = DisposeBag()
var replaySubject = ReplaySubject<String>.create(bufferSize: 1)
replaySubject.onNext("before subscribe first value")
replaySubject.onNext("before subscribe second value")
replaySubject.subscribe { (event) in
  print(event)
}
replaySubject.onNext("first value")
replaySubject.onCompleted()
// --- 출력 ---
// next(before subscribe second value)
// next(first value)
// completed
~~~

- ReplaySubject는 미리 정해진 사이즈 만큼 가장 최근의 이벤트를 새로운 Subscriber에게 전달.
- 위의 예제에서는 버퍼 사이즈가 1이기 때문에 가장 최근의 이벤트인 "before subscribe second value"를 새로운 구독자에게 전달.