# Publishers.ReceiveOn

**제네릭 구조체** | 특정 스케줄러에서 아래에 흐르는 Subscriber에게 요소를 전달하는 Publisher

이 코드를 작성한 후의 체인은 명시한 스케줄러에서 동작하게 된다.

Combine은 `Scheduler` 프로토콜을 정의하며, 기존의 `OperationQueue`, `DispatchQueue` 그리고 `RunLoop`가 이 프로토콜을 채택한다.

이니셜라이저는 세 개의 인자를 요구한다.

- `upstream` : 상위에 흐르는 Publisher. `Publisher` 프로토콜을 채택하는 객체를 전달해야 한다.
- `scheduler` : 앞으로 체인이 동작할 스케줄러. `Scheduler` 프로토콜을 채택하는 객체를 전달해야 한다.
- `options` : 스케줄러의 옵션. 
  - `RunLoop`의 경우 스케줄러 옵션을 지원하지 않는다.
  - `DispatchQueue`의 경우 `DispatchQoS`, `DispatchWorkItemFlags`, `DispatchGroup` 등을 정의하여 스케줄러 옵션을 지정할 수 있다.

`receive` 오퍼레이터 중 스케줄러를 인자로 받는 것은 해당 Publisher를 반환한다.

```swift
// 1 : Publishers.ReceiveOn Publisher
Publishers.ReceiveOn(upstream: Just(Void()), scheduler: DispatchQueue.main, options: nil)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine ReceiveOn Error")
    case .finished:
      print("Combine ReceiveOn Finish")
    }
  }, receiveValue: {
    print("Combine ReceiveOn")
  })
  .store(in: &cancellables)

// 2 : receive Operator
Just(Void())
  .receive(on: DispatchQueue.main)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine ReceiveOn Error")
    case .finished:
      print("Combine ReceiveOn Finish")
    }
  }, receiveValue: {
    print("Combine ReceiveOn")
  })
  .store(in: &cancellables)

// Combine ReceiveOn
// Combine ReceiveOn Finish
```

`DispatchQueue.main`에서 동작하도록 스케줄러를 명시해 주었으므로 해당 코드가 명시된 이후의 동작은 메인 스레드에서 이루어지게 된다.

## RxSwift

Observable 유틸리티 오퍼레이터 `observeOn`을 사용하여 구현할 수 있다.

RxSwift는 자체적으로 스케줄러를 구현하였으며, `MainScheduler`를 사용하면 이후의 동작을 메인쓰레드에서 이루어지게 할 수 있다.

```swift
Observable.just(Void())
  .observeOn(MainScheduler.instance)
  .subscribe(onNext: {
    print("RxSwift ReceiveOn")
  }, onError: { _ in
    print("RxSwift ReceiveOn Error")
  }, onCompleted: {
    print("RxSwift ReceiveOn Finish")
  })
  .disposed(by: disposeBag)
```

## ReactiveSwift

SignalProducer의 `observe` 메소드를 사용하여 구현할 수 있다.

ReactiveSwift는 자체적으로 스케줄러를 구현하였으며, `UIScheduler`를 사용하면 이후의 동작을 메인 쓰레드에서 이루어지게 할 수 있다.

```swift
SignalProducer(value: Void())
  .observe(on: UIScheduler())
  .start { event in
    switch event {
    case .value:
      print("ReactiveSwift ReceiveOn")
    case .failed:
      print("ReactiveSwift ReceiveOn Error")
    case .completed:
      print("ReactiveSwift ReceiveOn Finish")
    default:
      break
    }
  }
```

## 참고

[ReactiveX - Operators - ObserveOn](http://reactivex.io/documentation/operators/observeon.html)