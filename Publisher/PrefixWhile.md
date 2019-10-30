# Publishers.PrefixWhile

**제네릭 구조체** | 주어진 조건 클로저가 요소를 발행하는 것을 계속해야 하는지 나타내는 동안 요소를 다시 발행하는 Publisher

주어진 조건을 통과하는 동안 해당 요소를 다시 발행하며, 주어진 조건을 통과하지 못하면 종료한다.

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `predicate` : 요소 발행을 계속해야 하는지 결정하는 클로저

`prefix(while:)` 오퍼레이터는 다음의 Publisher를 반환한다.

- `Publishers.Sequence` Publisher를 반환한다.
- 해당 Publisher를 반환한다.

```swift
// 1 : Publishers.PrefixWhile Publisher
Publishers
  .PrefixWhile(upstream: Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3])) { $0 < 3 }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine PrefixWhile Error")
    case .finished:
      print("Combine PrefixWhile Finish")
    }
  }, receiveValue: { value in
    print("Combine PrefixWhile : \(value)")
  })
  .store(in: &cancellables)

// 2 : prefix(while:) Operator
Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3])
  .prefix { $0 < 3 }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine PrefixWhile Error")
    case .finished:
      print("Combine PrefixWhile Finish")
    }
  }, receiveValue: { value in
    print("Combine PrefixWhile : \(value)")
  })
  .store(in: &cancellables)

// Combine PrefixWhile : 1
// Combine PrefixWhile : 2
// Combine PrefixWhile Finish
```

상위 Publisher는 1, 2, 3의 값을 차례대로 내고, 3 미만인 동안 값을 내도록 하는 조건을 설정하였다.

따라서 1, 2의 값은 조건을 통과하지만 3의 값은 조건을 통과하지 못한다.

결과적으로 1, 2의 값을 내고 종료한다.

## RxSwift

Observable 필터링 오퍼레이터 `takeWhile`을 사용하여 구현할 수 있다.

```swift
Observable.from([1, 2, 3])
  .takeWhile { $0 < 3 }
  .subscribe(onNext: { value in
    print("RxSwift PrefixWhile : \(value)")
  }, onError: { _ in
    print("RxSwift PrefixWhile Error")
  }, onCompleted: {
    print("RxSwift PrefixWhile Finish")
  })
  .disposed(by: disposeBag)

// RxSwift PrefixWhile : 1
// RxSwift PrefixWhile : 2
// RxSwift PrefixWhile Finish
```

## ReactiveSwift

`take(while:)` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
SignalProducer([1, 2, 3])
  .take { $0 < 3 }
  .start { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift PrefixWhile : \(value)")
    case .failed:
      print("ReactiveSwift PrefixWhile Error")
    case .completed:
      print("ReactiveSwift PrefixWhile Finish")
    default:
      break
    }
  }

// ReactiveSwift PrefixWhile : 1
// ReactiveSwift PrefixWhile : 2
// ReactiveSwift PrefixWhile Finish
```

## 참고

[ReactiveX - Operators - Take](http://reactivex.io/documentation/operators/take.html)
