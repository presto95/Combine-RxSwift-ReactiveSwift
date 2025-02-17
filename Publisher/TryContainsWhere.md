# Publishers.TryContainsWhere

**제네릭 구조체** | 에러를 던지는 조건 클로저를 만족하는 요소를 전달받느냐에 따라 불리언 값을 방출하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `predicate` : 요소가 조건과 맞는지 판단하기 위한 에러를 던지는 클로저

`tryContains` 오퍼레이터와 관련이 있다.

`Publishers.ContainsWhere`의 동작과 같으나 동작을 수행하다가 에러를 낼 수 있다는 차이가 있다.

## RxSwift

해당 기능을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## ReactiveSwift

해당 기능을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - Contains](http://reactivex.io/documentation/operators/contains.html)

[Publishers.ContainsWhere](./ContainsWhere.md)