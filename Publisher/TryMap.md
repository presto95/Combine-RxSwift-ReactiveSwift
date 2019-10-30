# Publishers.TryMap

**제네릭 구조체** | 제공된 에러를 던지는 클로저를 사용하여 상위에 흐르는 Publisher로부터 모든 요소를 변환하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : `Publisher` 프로토콜을 따르는 Publisher를 넘겨준다.
- `transform` : 상위에 흐르는 Publisher로부터 요소들을 변환하기 위한 에러를 던지는 클로저를 넘겨준다.
  - 클로저 문법에서 반환형을 명시해줄 필요가 있다. 그렇지 않으면 Void를 반환하는 것으로 취급된다.

에러를 던지는 작업을 수행하기 위해 사용한다.

`tryMap` 오퍼레이터는 해당 Publisher를 반환한다.

`Publishers.Map`의 동작과 같으나 동작을 수행하다가 에러를 낼 수 있다는 차이가 있다.

## RxSwift

Observable 변환 오퍼레이터 `map`을 사용하여 구현할 수 있다. 

## ReactiveSwift

`attemptMap` 오퍼레이터를 사용하여 구현할 수 있다.

## 참고

[ReactiveX - Operators - Map](http://reactivex.io/documentation/operators/map.html)

[Publishers.Map](./Map.md)