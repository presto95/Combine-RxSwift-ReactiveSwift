# Publishers.First

**제네릭 구조체** | 스트림의 첫 번째 요소를 발행하고 종료하는 Publisher

이니셜라이저는 상위에 흐르는 Publisher를 인자로 받는다.

`first` 오퍼레이터는 다음의 Publisher를 반환한다.

- 발행할 값이 없을 수 있으므로 `Optional.Publisher` Publisher를 반환한다.
- 해당 Publisher를 반환한다.

```swift
// 1 : Publishers.First Publisher
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

// 2 : first Operator
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

Observable 필터링 오퍼레이터 `first`를 사용하여 구현할 수 있다.

`first` 오퍼레이터는 Single 타입을 반환하므로 Single을 다루는 것처럼 코드를 작성해야 한다.

값이 없을 수도 있으므로 값 이벤트에 전달되는 값은 옵셔널 타입이다.

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

`first` 오퍼레이터는 `Result?` 타입을 반환한다. 첫 번째 값이 있거나 에러가 발생한 경우 Result에 담아 반환하고, 첫 번째 값이 없는 경우 nil을 반환한다.

Result에서 producer 프로퍼티를 통해 오퍼레이터 체인을 계속 이어갈 수 있다.

```swift
SignalProducer([1, 2, 3])
  .first()?.producer
  .start { event in
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