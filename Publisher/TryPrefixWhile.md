# Publishers.TryPrefixWhile

**제네릭 구조체** | 에러를 던지는 주어진 조건 클로저가 요소를 발행하는 것을 계속해야 하는지 나타내는 동안 요소를 다시 발행하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `predicate` : 요소 발행을 계속해야 하는지 결정하는 에러를 던지는 클로저

`tryPrefix` 오퍼레이터와 관련이 있다.

`Publishers.PrefixWhile`의 동작과 같으나 동작을 수행하다가 에러를 낼 수 있다는 차이가 있다.

## RxSwift

`takeWhile` 오퍼레이터를 사용하여 구현할 수 있다.

## ReactiveSwift

해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - Take](http://reactivex.io/documentation/operators/take.html)

[Publishers.PrefixWhile](./PrefixWhile.md)