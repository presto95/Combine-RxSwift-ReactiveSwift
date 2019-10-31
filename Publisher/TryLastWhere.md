# Publishers.TryLastWhere

**제네릭 구조체** | 스트림이 종료하면, 에러를 던지는 특정 조건 클로저를 만족하기 위한 스트림의 마지막 요소만을 발행하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `predicate` : 어떤 요소를 발행할지 결정하는 클로저. 에러를 던질 수 있다.

`tryLast(where:)` 오퍼레이터는 해당 Publisher를 반환한다.

`Publishers.LastWhere`의 동작과 같으나 동작을 수행하다가 에러를 낼 수 있다는 차이가 있다.

## RxSwift

RxSwift는 해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## ReactiveSwift

ReactiveSwift는 해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[Publishers.LastWhere](./LastWhere.md)