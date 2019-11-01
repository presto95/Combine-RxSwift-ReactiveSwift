# Publishers.TryFirstWhere

**제네릭 구조체** | 에러를 던지는 조건 클로저를 만족하기 위한 스트림의 첫 번째 요소만을 발행하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `predicate` : 어떤 요소를 발행할지 결정하는 에러를 던지는 클로저

`tryFirst` 오퍼레이터와 관련이 있다.

`Publishers.FirstWhere`의 동작과 같으나 동작을 수행하다가 에러를 낼 수 있다는 차이가 있다.

## RxSwift

해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## ReactiveSwift

해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[Publishers.FirstWhere](./FirstWhere.md)