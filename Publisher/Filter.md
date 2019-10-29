# Publishers.Filter

**제네릭 구조체** | 제공된 클로저와 일치하는 모든 요소를 다시 발행하는 Publisher

Swift의 `filter` 함수와 같은 동작을 한다.

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : `Publisher` 프로토콜을 따르는 Publisher를 넘겨준다.
- `isIncluded` : 어떤 요소를 다시 발행할지를 나타내는 클로저를 넘겨준다. 클로저의 실행 결과는 Bool 타입이며, true이면 해당 요소를 다시 발행한다.

조건에 맞는 요소만 남기고 싶을 때 사용할 수 있다.

`filter` 오퍼레이터는 해당 Publisher를 반환한다.

```swift
// 1 : Publishers.Filter Publisher
Publishers.Filter(upstream: Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3, 4])) { $0.isMultiple(of: 2) }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Filter Error")
    case .finished:
      print("Combine Filter Finish")
    }
  }, receiveValue: { value in
    print("Combine Filter : \(value)")
  })
  .store(in: &cancellables)

// 2 : filter Operator
Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3, 4])
  .filter { $0.isMultiple(of: 2) }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Filter Error")
    case .finished:
      print("Combine Filter Finish")
    }
  }, receiveValue: { value in
    print("Combine Filter : \(value)")
  })
  .store(in: &cancellables)

// Combine Filter : 2
// Combine Filter : 4
// Combine Filter Finish
```

시퀀스에서 2의 배수인 것만 다시 발행하기 위해 `isMultiple(of: 2)`를 사용하였다.

결과적으로 1, 2, 3, 4의 값에서 2, 4의 값만 다시 발행된다.

## RxSwift

Observable 필터링 오퍼레이터 `filter`를 사용하여 구현할 수 있다.

```swift
Observable.from([1, 2, 3, 4])
  .filter { $0.isMultiple(of: 2) }
  .subscribe(onNext: { value in
    print("RxSwift Filter : \(value)")
  }, onError: { _ in
    print("RxSwift Filter Error")
  }, onCompleted: {
    print("RxSwift Filter Finish")
  })
  .disposed(by: disposeBag)

// RxSwift Filter : 2
// RxSwift Filter : 4
// RxSwift Filter Finish
```

## ReactiveSwift

`filter` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
SignalProducer([1, 2, 3, 4])
  .filter { $0.isMultiple(of: 2) }
  .start { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift Filter : \(value)")
    case .failed:
      print("ReactiveSwift Filter")
    case .completed:
      print("ReactiveSwift Filter")
    default:
      break
    }
  }

// ReactiveSwift Filter : 2
// ReactiveSwift Filter : 4
// ReactiveSwift Filter Finish
```

## 참고

[ReactiveX - Operators - Filter](http://reactivex.io/documentation/operators/filter.html)