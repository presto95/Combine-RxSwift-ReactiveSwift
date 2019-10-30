# Publishers.CombineLatest4

**제네릭 구조체** | 네 개의 Publisher로부터 최신의 요소를 전달받고 조합하는 Publisher

이니셜라이저는 조합할 네 개의 Publisher를 받는다.

인자에 들어가는 모든 Publisher의 에러 타입은 같아야 한다.

네 개의 Publisher에서 최신의 요소를 발행했을 때 이들을 조합하는 동작을 원할 때 사용할 수 있다.

다른 세 개의 Publisher를 인자로 받는 `combineLatest(_:_:_:)` 오퍼레이터는 해당 Publisher를 반환한다. 이 때 조합 결과는 튜플의 형태로 발행된다.

다른 세 개의 Publisher를 인자로 받고 transform 클로저를 명시한 `combineLatest(_:_:_:_:)` 오퍼레이터는 `Publishers.Map` Publisher를 반환한다.

`Publishers.CombineLatest`와 취할 수 있는 Publisher의 개수만 다를 뿐 동작은 같다.

## RxSwift

Observable 결합 오퍼레이터 `combineLatest`를 사용하여 구현할 수 있다.

## ReactiveSwift

ReactiveSwift는 세 개 이상의 스트림에 대한 `combineLatest` 동작을 구햔하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - CombineLatest](http://reactivex.io/documentation/operators/combinelatest.html)

[Publishers.CombineLatest](./CombineLatest.md)