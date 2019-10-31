# Publishers.TryAllSatisfy

**제네릭 구조체** | 전달받은 모든 요소가 주어진 에러를 던지는 조건을 통과하는지를 나타내는 하나의 불리언 값을 발행하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `predicate` : 각각의 전달받은 요소를 평가하는 클로저. 에러를 던질 수 있다.

주어진 조건이 에러를 던질 수 있으며, 값들이 주어진 조건을 모두 만족하는지 알기 위하 사용할 수 있다.

`tryAllSatisfy(_:)` 오퍼레이터는 해당 Publisher를 반환한다.

`Publishers.TryAllSatisfy`의 동작과 같으나 동작을 수행하다가 에러를 낼 수 있다는 차이가 있다.

## RxSwift

RxSwift는 해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## ReactiveSwift

ReactiveSwift는 해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[Publishers.AllSatisfy](./AllSatisfy.md)