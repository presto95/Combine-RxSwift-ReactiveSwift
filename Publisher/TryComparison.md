# Publishers.TryComparison

**제네릭 구조체** | 다른 Publisher에서 각각의 새로운 아이템에 대하여 이전에 발행된 아이템과 비교했을 때 오름차순일 때만 아이템을 다시 발행하며, 이 로직이 에러를 낸다면 실패하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `areInIncreasingOrder` : 두 개의 요소를 전달받고 이들이 오름차순이면 true를 반환하는 에러를 던지는 클로저
  - 첫 번째 인자는 현재 발행되는 요소, 두 번째 인자는 이전에 발행된 요소를 나타낸다.
  - 클로저의 매 실행마다 이 두 개의 값을 비교하여 조건에 맞는 요소를 반환한다.
  - 조건에 따라 오름차순 및 내림차순 설정을 할 수 있다.

`tryMax` 및 `tryMin` 오퍼레이터와 관련이 있다.

`Publishers.Comparison`의 동작과 같으나 동작을 수행하다가 에러를 낼 수 있다는 차이가 있다.

## RxSwift

해당 기능을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## ReactiveSwift

해당 기능을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - Max](http://reactivex.io/documentation/operators/max.html)

[ReactiveX - Operators - Min](http://reactivex.io/documentation/operators/min.html)

[Publishers.Comparison](./Comparison.md)