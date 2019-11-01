# Publishers.TryFilter

**제네릭 구조체** | 제공된 에러를 던지는 클로저와 일치하는 모든 요소를 다시 발행하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `isIncluded` : 어떤 요소를 다시 발행할지를 나타내는 에러를 던지는 클로저. 클로저의 실행 결과는 Bool 타입이며, true이면 해당 요소를 다시 발행한다.

조건에 맞는 요소만 남기는 작업을 하고 싶으며 작업이 에러를 던질 수 있을 때 사용할 수 있다.

`tryFilter` 오퍼레이터와 관련이 있다.

`Publishers.Filter`의 동작과 같으나 동작을 수행하다가 에러를 낼 수 있다는 차이가 있다.

## RxSwift

`filter` 오퍼레이터를 사용하여 구현할 수 있다.

## ReactiveSwift

해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - Filter](http://reactivex.io/documentation/operators/filter.html)

[Publishers.Filter](./Filter.md)