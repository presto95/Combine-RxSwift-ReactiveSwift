# Publishers.TryMap

**제네릭 구조체** | 제공된 에러를 던지는 클로저를 사용하여 상위에 흐르는 Publisher로부터 모든 요소를 변환하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `transform` : 상위에 흐르는 Publisher로부터 요소들을 변환하기 위한 에러를 던지는 클로저

`tryMap` 오퍼레이터와 관련이 있다.

`Publishers.Map`의 동작과 같으나 동작을 수행하다가 에러를 낼 수 있다는 차이가 있다.

## RxSwift

`map` 오퍼레이터를 사용하여 구현할 수 있다. 

## ReactiveSwift

`attemptMap` 오퍼레이터를 사용하여 구현할 수 있다.

## 참고

[ReactiveX - Operators - Map](http://reactivex.io/documentation/operators/map.html)

[Publishers.Map](./Map.md)