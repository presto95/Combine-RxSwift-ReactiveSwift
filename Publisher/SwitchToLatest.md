# Publishers.SwitchToLatest

**제네릭 구조체** | 중첩된 Publisher들을 평평하게 펴주는 Publisher

이니셜라이저는 Publisher를 요소로 전달받는 Publisher, 다시 말해서 중첩된 Publisher를 받는다.

예를 들어 `Publisher<Publisher<Data, NSError>, Never>` 타입의 Publisher에 해당 Publisher를 적용하면 `Publisher<Data, NSError>` 타입의 Publisher를 얻을 수 있다.

`switchToLatest()` 오퍼레이터는 해당 Publisher를 반환한다.

```swift
// 1 : Publishers.SwitchToLatest Publisher
Publishers.SwitchToLatest(upstream: Publishers.Sequence<[Just<Int>], Never>(sequence: [Just(1), Just(2), Just(3)]))
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine SwitchToLatest Error")
    case .finished:
      print("Combine SwitchToLatest Finish")
    }
  }, receiveValue: { value in
    print("Combine SwitchToLatest : \(value)")
  })
  .store(in: &cancellables)

// 2 : switchToLatest() Operator
Publishers.Sequence<[Just<Int>], Never>(sequence: [Just(1), Just(2), Just(3)])
  .switchToLatest()
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine SwitchToLatest Error")
    case .finished:
      print("Combine SwitchToLatest Finish")
    }
  }, receiveValue: { value in
    print("Combine SwitchToLatest : \(value)")
  })
  .store(in: &cancellables)

// Combine SwitchToLatest : 1
// Combine SwitchToLatest : 2
// Combine SwitchToLatest : 3
// Combine SwitchToLatest Finish
```

상위 Publisher는 `Just` Publisher를 발행하는 `Publishers.Sequence` Publisher이다. 이것에 `switchToLatest`를 적용하여 상위 Publisher를 평평하게 해주었다.

결과적으로 1, 2, 3의 값을 차례대로 내고 종료한다.

## RxSwift

Observable 변환 오퍼레이터 `flatMap`의 클로저에서 인자를 그대로 반환하여 해당 동작을 구현할 수 있다.

```swift
Observable.from([Observable.just(1), Observable.just(2), Observable.just(3)])
  .flatMap { $0 }
  .subscribe(onNext: { value in
    print("RxSwift SwitchToLatest : \(value)")
  }, onError: { _ in
    print("RxSwift SwitchToLatest Error")
  }, onCompleted: {
    print("RxSwift SwitchToLatest Finish")
  })
  .disposed(by: disposeBag)

// RxSwift SwitchToLatest : 1
// RxSwift SwitchToLatest : 2
// RxSwift SwitchToLatest : 3
// RxSwift SwitchToLatest Finish
```

## ReactiveSwift

`flatMap` 오퍼레이터의 클로저에서 인자를 그대로 반환하여 해당 동작을 구현할 수 있다.

```swift
SignalProducer([SignalProducer(value: 1), SignalProducer(value: 2), SignalProducer(value: 3)])
  .flatMap(.concat) { $0 }
  .start { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift SwitchToLatest : \(value)")
    case .failed:
      print("ReactiveSwift SwitchToLatest Error")
    case .completed:
      print("ReactiveSwift SwitchToLatest Finish")
    default:
      break
    }
  }

// ReactiveSwift SwitchToLatest : 1
// ReactiveSwift SwitchToLatest : 2
// ReactiveSwift SwitchToLatest : 3
// ReactiveSwift SwitchToLatest Finish
```

## 참고

[ReactiveX - Operators - FlatMap](http://reactivex.io/documentation/operators/flatmap.html)