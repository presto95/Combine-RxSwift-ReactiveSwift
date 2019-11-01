# Publishers.TryReduce

**제네릭 구조체** | 전달받은 모든 요소에 에러를 던지는 클로저를 적용하고, 상위에 흐르는 Publisher가 종료할 때 축적된 값을 내는 Publisher

이니셜라이저는 세 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `initial` : 클로저의 첫 번째 호출에 제공되는 초기 값
- `nextPartialResult` : 새로운 값을 내기 위해 이전까지 축적된 값과 상위 Publisher의 다음 요소를 취하는 에러를 던지는 클로저

상위 Publisher에 흐르는 모든 값을 모아 어떠한 하나의 값을 내고 싶으며 해당 작업이 에러를 낼 수 있을 때 사용할 수 있다.

`tryReduce` 오퍼레이터와 관련이 있다.

`Publishers.Reduce`의 동작과 같으나 동작을 수행하다가 에러를 낼 수 있다는 차이가 있다.

## RxSwift

`reduce` 오퍼레이터를 사용하여 구현할 수 있다.

## ReactiveSwift

해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - Reduce](http://reactivex.io/documentation/operators/reduce.html)

[Publishers.Reduce](./Reduce.md)