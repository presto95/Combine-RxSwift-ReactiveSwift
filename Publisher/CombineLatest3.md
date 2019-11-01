# Publishers.CombineLatest3

**제네릭 구조체** | 세 개의 Publisher로부터 최신의 요소를 전달받고 조합하는 Publisher

이니셜라이저는 조합할 세 개의 Publisher를 받는다.

조합할 모든 Publisher의 에러 타입은 같아야 한다.

세 개의 Publisher에서 최신의 요소를 발행했을 때 이들을 조합하는 동작을 원할 때 사용할 수 있다.

`combineLatest` 오퍼레이터와 관련이 있다.

`Publishers.CombineLatest`와 취할 수 있는 Publisher의 개수만 다를 뿐 동작은 같다.

## RxSwift

`combineLatest` 오퍼레이터를 사용하여 구현할 수 있다.

## ReactiveSwift

세 개 이상의 스트림에 대한 `combineLatest` 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - CombineLatest](http://reactivex.io/documentation/operators/combinelatest.html)

[Publishers.CombineLatest](./CombineLatest.md)