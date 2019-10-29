# Publishers.Sequence

**제네릭 구조체** | 요소의 주어진 시퀀스를 발행하는 Publisher

이니셜라이저의 `sequence` 인자로 `Sequence` 프로토콜을 채택하는 값을 넘겨주어야 한다.

Publisher가 시퀀스에 있는 요소들을 모두 사용하였을 때 다음 요청에서 Publisher가 종료한다.

시퀀스에 있는 요소들을 처음부터 하나하나 내려주며, 모든 요소를 다 사용하면 종료한다.

```swift
Publishers.Sequence<[Int], Never>(sequence: [1, 2])
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Sequence Error")
    case .finished:
      print("Combine Sequence Finish")
    }
  }, receiveValue: {
    print("Combine Sequence : \($0)")
  })
  .store(in: &cancellables)

// Combine Sequence : 1
// Combine Sequence : 2
// Combine Sequence Finish
```

## RxSwift

Observable 생성 오퍼레이터 `from`을 사용하여 구현할 수 있다.

```swift
Observable.from([1, 2])
  .subscribe(onNext: {
    print("RxSwift Sequence : \($0)")
  }, onError: { _ in
    print("RxSwift Sequence Error")
  }, onCompleted: {
    print("RxSwift Sequence Finish")
  })
  .disposed(by: disposeBag)

// RxSwift Sequence : 1
// RxSwift Sequence : 2
// RxSwift Sequence Finish
```

## ReactiveSwift

`SignalProduer`의 이니셜라이저 중 인자로 `Sequence` 프로토콜을 채택하는 값을 넘겨주는 `init(_:)`을 사용하여 구현할 수 있다.

```swift
SignalProducer([1, 2])
  .start { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift Sequence : \(value)")
    case .failed:
      print("ReactiveSwift Sequence Error")
    case .completed:
      print("ReactiveSwift Sequence Finish")
    default:
      break
    }
  }

// ReactiveSwift Sequence : 1
// ReactiveSwift Sequence : 2
// ReactiveSwift Sequence Finish
```

## 참고

[ReactiveX - Operators - From](http://reactivex.io/documentation/operators/from.html)