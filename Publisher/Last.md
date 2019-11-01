# Publishers.Last

**제네릭 구조체** | 스트림이 종료한 후 스트림의 마지막 요소만을 발행하는 Publisher

이니셜라이저는 한 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher

`last` 오퍼레이터와 관련이 있다.

```swift
// Publishers.Last Publisher
Publishers
  .Last(upstream: Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3, 4, 5]))
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Last Error")
    case .finished:
      print("Combine Last Finish")
    }
  }, receiveValue: { value in
    print("Combine Last : \(value)")
  })
  .store(in: &cancellables)

// last Operator
Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3, 4, 5])
  .last()
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Last Error")
    case .finished:
      print("Combine Last Finish")
    }
  }, receiveValue: { value in
    print("Combine Last : \(value)")
  })
  .store(in: &cancellables)

// Combine Last : 5
// Combine Last Finish
```

상위 Publisher는 1, 2, 3, 4, 5의 값을 차례대로 낸다.

`last`에 의해 마지막 요소만 발행되며, 결과적으로 5의 값을 내고 종료한다.

## RxSwift

`takeLast` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
Observable.from([1, 2, 3, 4, 5])
  .takeLast(1)
  .subscribe(onNext: { value in
    print("RxSwift Last : \(value)")
  }, onError: { _ in
    print("RxSwift Last Error")
  }, onCompleted: {
    print("RxSwift Last Finish")
  })
  .disposed(by: disposeBag)

// RxSwift Last : 5
// RxSwift Last Finish
```

## ReactiveSwift

`last` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
SignalProducer([1, 2, 3])
  .last()?.signal
  .observe { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift Last : \(value)")
    case .failed:
      print("ReactiveSwift Last Error")
    case .completed:
      print("ReactiveSwift Last Finish")
    default:
      break
    }
  }

// ReactiveSwift Last : 1
// ReactiveSwift Last Finish
```

## 참고

[ReactiveX - Operators - Last](http://reactivex.io/documentation/operators/last.html)