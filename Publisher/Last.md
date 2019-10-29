# Publishers.Last

**제네릭 구조체** | 스트림이 종료한 후 스트림의 마지막 요소만을 발행하는 Publisher

이니셜라이저는 상위에 흐르는 Publisher를 인자로 받는다.

`last` 오퍼레이터는 다음의 Publisher를 반환한다.

- 발행할 값이 없을 수 있으므로 `Optional.Publisher` Publisher를 반환한다.
- 해당 Publisher를 반환한다.

```swift
// 1 : Publishers.Last Publisher
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

// 2 : first Operator
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

Observable 필터링 오퍼레이터 `takeLast`을 사용하여 구현할 수 있다.

`last` 대신 `takeLast` 오퍼레이터가 구현되어 있다.

`takeLast`의 인자에 1을 넘겨주어 마지막 하나의 값을 취한다고 명시하면 해당 동작을 구현할 수 있다.

값이 없을 수도 있으므로 값 이벤트에 전달되는 값은 옵셔널 타입이다.

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

`last` 오퍼레이터는 `Result?` 타입을 반환한다. 첫 번째 값이 있거나 에러가 발생한 경우 Result에 담아 반환하고, 첫 번째 값이 없는 경우 nil을 반환한다.

Result에서 producer 프로퍼티를 통해 오퍼레이터 체인을 계속 이어갈 수 있다.

```swift
SignalProducer([1, 2, 3])
  .last()?.producer
  .start { event in
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