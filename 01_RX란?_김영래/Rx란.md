# Rx란?

## 리액티브 프로그래밍(Reactive programming)
* Reactive: '반작용을 나타내는'

* 데이터 흐름과 전달에 관한 프로그래밍 패러다임입니다.

* 리액티브 프로그래밍은 데이터 흐름을 먼저 정의하고 데이터가 변경되었을 때 연관되는 함수나 수식이 업데이트되는 방식입니다.
* 예시로는 각 셀에 값을 넣거나 혹은 다른 셀을 조합해서 내가 원하는 값을 계산하는 엑셀을 들 수 있습니다

> 회사에서 올해 1월부터 12월까지 매출액의 합을 구한다. 매월 매출액은 리액티브 프로그래밍의 데이터 소스에 해당한다. 연간 매출액은 다음처럼 매월 매출액의 합으로 산출한다.

> 연말 매출액 = 1월 매출 + 2월 매출 + 3월 매출 + ... + 12월 매출

> 최종 매출액을 결산해보니 2월 매출이 900억에서 850억으로 줄고 8월 매출이 1200억에서 1300억으로 늘었다.

> 이럴 때 리액티브 프로그래밍은 변경된 매출액을 다시 가져와서 총합을 구하는 방식이 아니라 매월 매출액으로 지정해놓은 데이터 소스에서 변경된 값을 전달한다. 그리고 전달된 2월과 8월의 개별 값이 미리 지정해둔 수식을 통해 계산되어 연말 매출액을 갱신한다.

* 명령형 프로그래밍 방식은 변경이 발생했다는 통지를 받아서 연말 매출액을 새로 계산하는 당겨오는 (pull) 방식이다.

* 리액티브 프로그래밍은 데이터 소스가 변경된 데이터를 밀어주는(push) 방식이다. 일종의 옵서버 패턴이라고 할 수 있다.

* 네트워크 프로그래밍할 때 사용되는 콜백이나 UI 프로그래밍할 때 버튼 이벤트를 처리하는 클릭 리스너도 개념상으로는 리액티브 프로그래밍에 해당한다

* 이 전통적인 개념에 몇 가지 요소를 추가해야 RxJava 기반의 리액티브 프로그래밍이라고 할 수 있다.

## 리액티브 프로그래밍 개념 잡기

    컴퓨터 프로그램의 세 가지 종류

    첫 번째는 변환 프로그램으로 주어진 입력값을 바탕으로 결과를 계산하는 프로그램이다. 일반적인 예는 컴파일러와 수치 계산 프로그램이 있다.

    두 번째는 상호작용 프로그램으로 프로그램이 주도하는 속도로 사용자 혹은 다른 프로그램과 상호작용을 한다. 사용자의 관점으로 볼때 시분할 시스템은 상호작용 프로그램이다.

    리액티브 프로그램은 주변의 환경과 끊임없는 상호작용을 하는데 프로그램이 주도하는 것이 아니라 환경이 변하면 이벤트를 받아 동작한다. 

    상호작용 프로그램은 자신의 속도에 맞춰 일하고 대부분 통신을 담당한다.

    반면 리액티브 프로그램은 외부 요구에 반응에 맞춰 일하고 대부분 정확한 인터럽트 처리를 담당한다.  

* 보통 애플리케이션을 만드는 프로그래머가 작성하는 프로그래머가 작성하는 프로그램은 변환 프로그램이거나 상호작용 프로그램이다.

* 인터럽트 같은 개념은 시스템 프로그래머가 담당한다. 혹은 클라이언트의 요청을 처리하는 서버 프로그래밍은 리액티브 프로그래밍에 가깝다.  

## RxJava 시작하기

* Hello RxJava 2를 출력하는 코드를 보고, 하나하나 살펴보자

~~~java
import io.reactivex.Observable;

public class RxStart {
    public void emit() {
        Observable.just("Hello", "RxJava 2!!")
        .subscribe(System.out::println);
    }

    public static void main(String args[]) {
        RxStart demo = new RxStart();
        demo.emit();
    }
}
~~~

> Hello     
RxJava 2!!

### io.reactivex
* RxJava 2의 기본 패키지 이름은 io.reactivex이다.

### Observable 클래스
* Observable 클래스는 데이터의 변화가 발생하는 데이터 소스이다.
* 예시로 들었던 연간 매출액 예에서는 개별적인 월간 매출액 데이터에 해당한다.
* 리액티브 프로그래밍은 Observable에서 시작한다.

### just() 함수
* Observable 클래스의 just() 함수는 가장 단순한 Observable 선언 방식이다.
* 위의 코드에서는 데이터 소스에서 'Hello' 와 'RxJava 2!!' 를 발행했다.
* Integer와 같은 래퍼 타입부터 Order 같은 사용자 정의 클래스의 객체도 인자로 넣을 수 있다.

### subscribe() 함수
* subscribe() 함수는 Observable을 구독한다. 
* Observable은 subscribe() 함수를 호출해야 비로소 변화한 데이터를 구독자에게 발행한다(just() 함수만 호출하면 데이터를 발행하지 않는다).
* 반드시 데이터를 수신할 구독자가 subscribe() 함수를 호출해야 Observable에서 데이터가 발행된다.

### System.out::println
* 수신한 데이터를 System.out::println을 통해 호출했다.

### emit() 메서드
> '발행하다' 로 표현
* Rxjava 개발 문서에서는 Observable이 subscribe() 함수를 호출한 구독자에게 데이터를 발행하는 것을 표현하는 용어로 사용한다.