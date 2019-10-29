# Publishers.HandleEvents

**제네릭 구조체** | Publisher의 이벤트가 발생할 때 특정 클로저를 수행하는 Publisher

스트림의 생명 주기 동안 발생하는 여러 이벤트에서 실행할 액션을 등록한다.

이니셜라이저는 여섯 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `receiveSubscription` : Publisher가 상위 Publisher로부터 구독을 전달받을 때 수행할 클로저. 기본값은 nil
- `receiveOutput` : Publisher가 상위 Publisher로부터 값을 전달받을 때 수행할 클로저. 기본값은 nil
- `receiveCompletion` : Publisher가 상위 Publisher로부터 완료를 전달받을 때 수행할 클로저. 기본값은 nil
- `receiveCancel` : 하위 리시버가 발행을 취소할 때 수행할 클로저. 기본값은 nil
- `receiveRequest` : Publisher가 더 많은 요소를 위해 요청을 전달받을 때 수행할 클로저. 기본값은 nil

특정 이벤트가 발생할 때 특정 작업을 수행하기 위해 사용할 수 있다.

Publisher의 생명 주기를 이해하기 위해 사용할 수 있다.

`sink`는 Subscriber에 대한 오퍼레이터고, `handleEvents`는 Publisher에 대한 오퍼레이터다.

- `sink`는 상위 Publisher의 모든 작업을 수행한 후 Subscriber의 구독에 의해 발행된 이벤트에 대해 동작한다.
- `handleEvents`는 구독에 의해 상위 Publisher가 발행하는 이벤트에 대해 동작한다.

`handleEvents` 오퍼레이터는 해당 Publisher를 반환한다.

```swift
// 1 : Publishers.HandleEvents Publisher
Publishers
  .HandleEvents(upstream: Just(Void()),
                receiveSubscription: { _ in print("Combine HandleEvents ReceiveSubscription") },
                receiveOutput: { print("Combine HandleEvents ReceiveOutput") },
                receiveCompletion: { _ in print("Combine HandleEvents ReceiveCompletion") },
                receiveCancel: { print("Combine HandleEvents ReceiveCancel") },
                receiveRequest: { _ in print("Combine HandleEvents ReceiveRequest") })
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine HandleEvents Error")
    case .finished:
      print("Combine HandleEvents Finish")
    }
  }, receiveValue: {
    print("Combine HandleEvents")
  })
  .store(in: &cancellables)

// 2 : handleEvents Operator
Just(Void())
  .handleEvents(receiveSubscription: { _ in print("Combine HandleEvents ReceiveSubscription") },
                receiveOutput: { print("Combine HandleEvents ReceiveOutput") },
                receiveCompletion: { _ in print("Combine HandleEvents ReceiveCompletion") },
                receiveCancel: { print("Combine HandleEvents ReceiveCancel") },
                receiveRequest: { _ in print("Combine HandleEvents ReceiveRequest") })
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine HandleEvents Error")
    case .finished:
      print("Combine HandleEvents Finish")
    }
  }, receiveValue: {
    print("Combine HandleEvents")
  })
  .store(in: &cancellables)

// Combine HandleEvents Receive Request
// Combine HandleEvents Receive Subscription
// Combine HandleEvents Receive Output
// Combine HandleEvents
// Combine HandleEvents Receive Completion
// Combine HandleEvents Finish
```

결과를 통해 Publisher는 에러를 내지 않는 경우 다음과 같은 순서로 동작한다는 것을 알 수 있다.

1. Publisher : Receive Request
2. Publisher : Receive Subscription
3. Publisher : Receive Output
4. Subscriber : Receive Value
5. Publisher : Receive Completion
6. Subscriber : Receive Finished

## RxSwift

Observable 유틸리티 오퍼레이터 `do`를 사용하여 구현할 수 있다.

```swift
Observable.just(Void())
  .do(onNext: { print("RxSwift HandleEvents OnNext") },
      afterNext: { print("RxSwift HandleEvents AfterNext") },
      onError: { _ in print("RxSwift HandleEvents OnError") },
      afterError: { _ in print("RxSwift HandleEvents AfterError") },
      onCompleted: { print("RxSwift HandleEvents OnCompleted") },
      afterCompleted: { print("RxSwift HandleEvents AfterCompleted") },
      onSubscribe: { print("RxSwift HandleEvents OnSubscribe") },
      onSubscribed: { print("RxSwift HandleEvents OnSubscribed") },
      onDispose: { print("RxSwift HandleEvents OnDispose") })
  .subscribe(onNext: {
    print("RxSwift HandleEvents")
  }, onError: { _ in
    print("RxSwift HandleEvents Error")
  }, onCompleted: {
    print("RxSwift HandleEvents Finish")
  })
  .disposed(by: disposeBag)

// RxSwift HandleEvents OnSubscribe
// RxSwift HandleEvents OnNext
// RxSwift HandleEvents
// RxSwift HandleEvents AfterNext
// RxSwift HandleEvents OnCompleted
// RxSwift HandleEvents Finish
// RxSwift HandleEvents AfterCompleted
// RxSwift HandleEvents OnSubscribed
// RxSwift HandleEvents OnDispose
```

결과를 통해 Observable은 에러를 내지 않는 경우 다음과 같은 순서로 동작한다는 것을 알 수 있다.

1. Observable : On Subscribe
2. Observable : On Next
3. Observer : On Next
4. Observable : After Next
5. Observable : On Completed
6. Observer : On Completed
7. Observable : After Completed
8. Observable : On Subscribed
9. Observable : On Dispose

## ReactiveSwift

`on` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
SignalProducer(value: Void())
  .on(starting: { print("ReactiveSwift HandleEvents Starting") },
      started: { print("ReactiveSwift HandleEvents Started") },
      event: { _ in print("ReactiveSwift HandleEvents Event") },
      failed: { _ in print("ReactiveSwift HandleEvents Failed") },
      completed: { print("ReactiveSwift HandleEvents Completed") },
      interrupted: { print("ReactiveSwift HandleEvents Interrupted") },
      terminated: { print("ReactiveSwift HandleEvents Terminated") },
      disposed: { print("ReactiveSwift HandleEvents Dispose") },
      value: { print("ReactiveSwift HandleEvents Value") })
  .start { event in
    switch event {
    case .value:
      print("ReactiveSwift HandleEvents")
    case .failed:
      print("ReactiveSwift HandleEvents Error")
    case .completed:
      print("ReactiveSwift HandleEvents Finish")
    default:
      break
    }
  }

// ReactiveSwift HandleEvents Starting
// ReactiveSwift HandleEvents Event
// ReactiveSwift HandleEvents Value
// ReactiveSwift HandleEvents
// ReactiveSwift HandleEvents Event
// ReactiveSwift HandleEvents Completed
// ReactiveSwift HandleEvents Terminated
// ReactiveSwift HandleEvents Finish
// ReactiveSwift HandleEvents Dispose
// ReactiveSwift HandleEvents Started
```

결과를 통해 Signal은 에러를 내지 않는 경우 다음과 같은 순서로 동작한다는 것을 알 수 있다.

1. Signal : Starting
2. Signal : Event
3. Signal : Value
4. Observer : Value
5. Signal : Completed
6. Signal : Terminated
7. Observer : Finish
8. Signal : Dispose
9. Signal : Started

## 참고

[ReactiveX - Operators - Do](http://reactivex.io/documentation/operators/do.html)