# Publishers.TryCompactMap

**제네릭 구조체** | 받은 각각의 요소에 대하여 에러를 던지는 클로저를 호출하여 nil이 아닌 결과를 다시 발행하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `transform` : 상위에 흐르는 Publisher로부터 값을 받고 옵셔널 값을 반환하는 에러를 던지는 클로저

시퀀스에서 nil 값을 없애는 등의 작업을 하고 싶으며 작업이 에러를 던질 수 있을 때 사용할 수 있다.

`tryCompactMap` 오퍼레이터는 해당 Publisher를 반환한다.

`Publishers.CompactMap`의 동작과 같으나 동작을 수행하다가 에러를 낼 수 있다는 차이가 있다.

## RxSwift

Observable 필터링 오퍼레이터 `compactMap`을 사용하여 구현할 수 있다.

## ReactiveSwift

ReactiveSwift는 `filterMap`의 동작을 수행할 때 에러를 던질 수 있는 방법을 제공하지 않는다.

## 참고

[Publishers.CompactMap](./CompactMap.md)