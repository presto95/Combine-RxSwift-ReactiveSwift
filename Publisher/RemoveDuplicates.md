# Publishers.RemoveDuplicates

**제네릭 구조체** | 이전 요소와 일치하지 않는 요소만 발행하는 Publisher

발행되려는 요소가 이전에 발행된 것과 일치한다면 해당 요소를 발행하지 않는다.

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `predicate` : 두 요소가 중복되었는지 평가하기 위한 클로저. 

중복된 값이 들어오는 경우 같은 값을 반복 발행하는 것을 방지하기 위해 사용한다.

`removeDuplicates` 오퍼레이터는 해당 Publisher를 반환한다. 

해당 오퍼레이터를 사용할 때 Output 타입이 `Equatable` 프로토콜을 구현하고 있다면 별도의 `predicate`를 구현할 필요가 없다.

```swift
// 1 : Publishers.RemoveDuplicates Publisher
Publishers.RemoveDuplicates(upstream: Publishers.Sequence<[Int], Never>(sequence: [1, 2, 2, 2])) { $0 == $1 }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine RemoveDuplicates Error")
    case .finished:
      print("Combine RemoveDuplicates Finish")
    }
  }, receiveValue: { value in
    print("Combine RemoveDuplicates : \(value)")
  })
  .store(in: &cancellables)

// 2 : removeDuplicates Operator
Publishers.Sequence<[Int], Never>(sequence: [1, 2, 2, 2])
  .removeDuplicates()
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine RemoveDuplicates Error")
    case .finished:
      print("Combine RemoveDuplicates Finish")
    }
  }, receiveValue: { value in
    print("Combine RemoveDuplicates : \(value)")
  })
  .store(in: &cancellables)

// Combine RemoveDuplicates : 1
// Combine RemoveDuplicates : 2
// Combine RemoveDuplicates Finish
```

상위에 흐르는 Publisher가 1, 2, 2, 2의 값을 차례대로 내는데, 2가 중복되어 내어질 때 무시된다. 그러므로 1, 2의 값을 차례대로 내고 종료하게 된다.

## RxSwift

Observable 필터링 오퍼레이터 `distinctUntilChanged`를 사용하여 구현할 수 있다.

Element 타입이 `Equatable` 프로토콜을 구현하고 있다면 별도의 중복 처리 로직을 구현할 필요가 없다.

```swift
Observable.from([1, 2, 2, 2])
  .distinctUntilChanged()
  .subscribe(onNext: { value in
    print("RxSwift RemoveDuplicates : \(value)")
  }, onError: { _ in
    print("RxSwift RemoveDuplicates Error")
  }, onCompleted: {
    print("RxSwift RemoveDuplicates Finish")
  })
  .disposed(by: disposeBag)

// RxSwift RemoveDuplicates : 1
// RxSwift RemoveDuplicates : 2
// RxSwift RemoveDuplicates Finish
```

## ReactiveSwift

`skipRepeats` 오퍼레이터를 사용하여 구현할 수 있다.

Element 타입이 `Equatable` 프로토콜을 구현하고 있다면 별도의 중복 처리 로직을 구현할 필요가 없다.

```swift
SignalProducer([1, 2, 2, 2])
  .skipRepeats()
  .start { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift RemoveDuplicates : \(value)")
    case .failed:
      print("ReactiveSwift RemoveDuplicates Error")
    case .completed:
      print("ReactiveSwift RemoveDuplicates Finish")
    default:
      break
    }
  }

// ReactiveSwift RemoveDuplicates : 1
// ReactiveSwift RemoveDuplicates : 2
// ReactiveSwift RemoveDuplicates Finish
```

## 참고

[ReactiveX - Operators - Distinct](http://reactivex.io/documentation/operators/distinct.html)