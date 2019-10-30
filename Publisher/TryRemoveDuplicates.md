# Publishers.TryRemoveDuplicates

**제네릭 구조체** | 이전 요소와 일치하지 않는 요소만 발행하는데, 제공된 에러를 던지는 클로저를 사용하여 일치 여부를 평가하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `predicate` : 두 요소가 중복되었는지 평가하기 위한 에러를 던지는 클로저. 

중복된 값이 들어오는 경우 같은 값을 반복 발행하는 것을 방지하며 해당 작업이 에러를 던질 수 있을 때 사용한다.

`tryRemoveDuplicates` 오퍼레이터는 해당 Publisher를 반환한다.

`Publishers.RemoveDuplicates`의 동작과 같으나 동작을 수행하다가 에러를 낼 수 있다는 차이가 있다.

## RxSwift

Observable 필터링 오퍼레이터 `distinctUntilChanged`를 사용하여 구현할 수 있다.

## ReactiveSwift

ReactiveSwift는 `skipRepeats`의 동작을 수행할 때 에러를 던질 수 있는 방법을 제공하지 않는다.

## 참고

[ReactiveX - Operators - Distinct](http://reactivex.io/documentation/operators/distinct.html)

[Publishers.RemoveDuplicates](./RemoveDuplicates.md)