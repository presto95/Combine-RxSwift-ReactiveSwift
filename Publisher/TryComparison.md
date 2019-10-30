# Publishers.TryComparison

**제네릭 구조체** | 다른 Publisher에서 각각의 새로운 아이템에 대하여 이전에 발행된 아이템과 비교했을 때 오름차순일 때만 아이템을 다시 발행하며, 이 로직이 에러를 낸다면 실패하는 Publisher

`tryMax(_:)` 및 `tryMin(_:)` 오퍼레이터는 해당 Publisher를 반환한다.

`Publishers.Comparison`의 동작과 같으나 동작을 수행하다가 에러를 낼 수 있다는 차이가 있다.

## RxSwift

RxSwift는 해당 기능을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## ReactiveSwift

ReactiveSwift는 해당 기능을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - Max](http://reactivex.io/documentation/operators/max.html)

[ReactiveX - Operators - Min](http://reactivex.io/documentation/operators/min.html)

[Publishers.Comparison](./Comparison.md)