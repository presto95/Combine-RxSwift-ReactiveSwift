# Publishers.SubscribeOn

**제네릭 구조체** | 특정 스케줄러에서 상위에 흐르는 Publisher로부터 요소를 받는 Publisher

이니셜라이저는 세 개의 인자를 요구한다.

- `upstream` : 상위에 흐르는 Publisher
- `scheduler` : 이전 체인이 동작할 스케줄러
- `options` : 스케줄러의 옵션.
  - `RunLoop`의 경우 스케줄러 옵션을 지원하지 않는다.
  - `DispatchQueue`의 경우 `DispatchQoS`, `DispatchWorkItemFlags`, `DispatchGroup` 등을 정의하여 스케줄러 옵션을 지정할 수 있다.

이 코드를 작성하기 전의 체인은 명시한 스케줄러에서 동작하게 된다.

Combine은 `Scheduler` 프로토콜을 정의하며, `OperationQueue`, `DispatchQueue`, `RunLoop` 등이 프로토콜을 채택한다.

`subscribe` 오퍼레이터와 관련이 있다.

```swift
// Publishers.SubscribeOn Publisher
Publishers.SubscribeOn(upstream: Just(Void()), scheduler: DispatchQueue.main, options: nil)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine SubscribeOn Error")
    case .finished:
      print("Combine SubscribeOn Finish")
    }
  }, receiveValue: {
    print("Combine SubscribeOn")
  })
  .store(in: &cancellables)

// subscribe Operator
Just(Void())
  .subscribe(on: DispatchQueue.main)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine SubscribeOn Error")
    case .finished:
      print("Combine SubscribeOn Finish")
    }
  }, receiveValue: {
    print("Combine SubscribeOn")
  })
  .store(in: &cancellables)

// Combine SubscribeOn
// Combine SubscribeOn Finish
```

`DispatchQueue.main`에서 동작하도록 스케줄러를 명시해 주었으므로 해당 코드가 명시되기 이전의 동작은 메인 스레드에서 이루어지게 된다.

## RxSwift

`subscribeOn` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
Observable.just(Void())
  .subscribeOn(MainScheduler.instance)
  .subscribe(onNext: {
    print("RxSwift SubscribeOn")
  }, onError: { _ in
    print("RxSwift SubscribeOn Error")
  }, onCompleted: {
    print("RxSwift SubscribeOn Finish")
  })
  .disposed(by: disposeBag)
```

## ReactiveSwift

`start` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
SignalProducer(value: Void())
  .start(on: UIScheduler())
  .start { event in
    switch event {
    case .value:
      print("ReactiveSwift SubscribeOn")
    case .failed:
      print("ReactiveSwift SubscribeOn Error")
    case .completed:
      print("ReactiveSwift SubscribeOn Finish")
    default:
      break
    }
  }
```

## 참고

[ReactiveX - Operators - SubscribeOn](http://reactivex.io/documentation/operators/subscribeon.html)