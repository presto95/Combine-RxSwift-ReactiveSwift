# Publishers.CompactMap

**제네릭 구조체** | 받은 각각의 요소에 대하여 클로저를 호출하여 nil이 아닌 결과를 다시 발행하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `transform` : 상위에 흐르는 Publisher로부터 값을 받고 옵셔널 값을 반환하는 클로저

Swift의 `compactMap` 함수와 같은 동작을 한다.

시퀀스에서 nil 값을 없애는 등의 작업을 하고 싶을 때 사용할 수 있다.

`compactMap` 오퍼레이터와 관련이 있다.

```swift
// Publishers.CompactMap Publisher
Publishers.CompactMap(upstream: Publishers.Sequence<[Int?], Never>(sequence: [1, nil, 2])) { $0 }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine CompactMap Error")
    case .finished:
      print("Combine CompactMap Finish")
    }
  }, receiveValue: { value in
    print("Combine CompactMap : \(value)")
  })
  .store(in: &cancellables)

// compactMap Operator
Publishers.Sequence<[Int?], Never>(sequence: [1, nil, 2])
  .compactMap { $0 }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine CompactMap Error")
    case .finished:
      print("Combine CompactMap Finish")
    }
  }, receiveValue: { value in
    print("Combine CompactMap : \(value)")
  })
  .store(in: &cancellables)

// Combine CompactMap : 1
// Combine CompactMap : 2
// Combine CompactMap Finish
```

## RxSwift

`compactMap` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
Observable.from([1, nil, 2])
  .compactMap { $0 }
  .subscribe(onNext: { value in
    print("RxSwift CompactMap : \(value)")
  }, onError: { _ in
    print("RxSwift CompactMap Error")
  }, onCompleted: {
    print("RxSwift CompactMap Finish")
  })
  .disposed(by: disposeBag)

// RxSwift CompactMap : 1
// RxSwift CompactMap : 2
// RxSwift CompactMap Finish
```

## ReactiveSwift

`filterMap` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
SignalProducer([1, nil, 2])
  .filterMap { $0 }
  .start { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift CompactMap : \(value)")
    case .failed:
      print("ReactiveSwift CompactMap")
    case .completed:
      print("ReactiveSwift CompactMap")
    default:
      break
    }
  }

// ReactiveSwift CompactMap : 1
// ReactiveSwift CompactMap : 2
// ReactiveSwift CompactMap Finish
```

