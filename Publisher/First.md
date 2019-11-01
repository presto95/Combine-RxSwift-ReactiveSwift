# Publishers.First

**제네릭 구조체** | 스트림의 첫 번째 요소를 발행하고 종료하는 Publisher

이니셜라이저는 한 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher

`first` 오퍼레이터와 관련이 있다.

```swift
// Publishers.First Publisher
Publishers
  .First(upstream: Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3, 4, 5]))
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine First Error")
    case .finished:
      print("Combine First Finish")
    }
  }, receiveValue: { value in
    print("Combine First : \(value)")
  })
  .store(in: &cancellables)

// first Operator
Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3, 4, 5])
  .first()
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine First Error")
    case .finished:
      print("Combine First Finish")
    }
  }, receiveValue: { value in
    print("Combine First : \(value)")
  })
  .store(in: &cancellables)

// Combine First : 1
// Combine First Finish
```

상위 Publisher는 1, 2, 3, 4, 5의 값을 차례대로 낸다.

`first`에 의해 첫 번째 요소만 발행되며, 결과적으로 1의 값을 내고 종료한다.

## RxSwift

`first` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
Observable.from([1, 2, 3, 4, 5])
  .first()
  .subscribe(onSuccess: { value in
    print("RxSwift First : \(value)")
  }, onError: { _ in
    print("RxSwift First Error")
  })
  .disposed(by: disposeBag)

// RxSwift First : Optional(1)
```

## ReactiveSwift

`first` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
SignalProducer([1, 2, 3])
  .first()?.signal
  .observe { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift First : \(value)")
    case .failed:
      print("ReactiveSwift First Error")
    case .completed:
      print("ReactiveSwift First Finish")
    default:
      break
    }
  }

// ReactiveSwift First : 1
// ReactiveSwift First Finish
```

## 참고

[ReactiveX - Operators - First](http://reactivex.io/documentation/operators/first.html)