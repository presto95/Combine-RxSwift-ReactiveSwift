# Publishers.Delay

**제네릭 구조체** | 아래에 흐르는 리시버에게 요소와 완료의 전달을 지연시키는 Publisher

이니셜라이저는 네 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `interval` : 지연시킬 시간
- `tolerance` : 촉발하는 지연된 이벤트에서 허용된 내성
  - 이 프로퍼티를 설정된 시간만큼 시스템은 이벤트의 배출을 여유롭게 할 수 있다. 유연하게(값이 크게) 설정될수록 시스템의 전력 소비를 줄이고 응답성을 좋게 할 수 있다.
- `scheduler` : 지연된 이벤트를 전달하는 스케줄러
- `options` : 스케줄러 옵션

주어진 시간만큼 스트림으로부터의 값 배출을 미룬다.

`delay` 오퍼레이터와 관련이 있다.

```swift
// Publishers.Delay Publisher
Publishers.Delay(upstream: Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3]), interval: .seconds(1), tolerance: .zero, scheduler: DispatchQueue.main)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Delay Error")
    case .finished:
      print("Combine Delay Finish")
    }
  }, receiveValue: { value in
    print("Combine Delay : \(value)")
  })
  .store(in: &cancellables)

// delay Operator
Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3])
  .delay(for: .seconds(1), scheduler: DispatchQueue.main)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Delay Error")
    case .finished:
      print("Combine Delay Finish")
    }
  }, receiveValue: { value in
    print("Combine Delay : \(value)")
  })
  .store(in: &cancellables)

// 출력은 1초 후에 발생한다.
// Combine Delay : 1
// Combine Delay : 2
// Combine Delay : 3
// Combine Delay Finish
```

상위 Publisher는 1, 2, 3의 값을 차례대로 낸다.

1초 지연하여 아래로 보내므로 1초 후에 이벤트가 발생한다.

## RxSwift

`delay` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
Observable.from([1, 2, 3])
  .delay(.seconds(1), scheduler: MainScheduler.instance)
  .subscribe(onNext: { value in
    print("RxSwift Delay : \(value)")
  }, onError: { _ in
    print("RxSwift Delay Error")
  }, onCompleted: {
    print("RxSwift Delay Finish")
  })
  .disposed(by: disposeBag)

// 출력은 1초 후에 발생한다.
// RxSwift Delay : 1
// RxSwift Delay : 2
// RxSwift Delay : 3
// RxSwift Delay Finish
```

## ReactiveSwift

`delay` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
SignalProducer([1, 2, 3])
  .delay(1, on: QueueScheduler.main)
  .start { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift Delay : \(value)")
    case .failed:
      print("ReactiveSwift Delay Error")
    case .completed:
      print("ReactiveSwift Delay Finish")
    default:
      break
    }
  }

// 출력은 1초 후에 발생한다.
// ReactiveSwift Delay : 1
// ReactiveSwift Delay : 2
// ReactiveSwift Delay : 3
// ReactiveSwift Delay Finish
```

## 참고

[ReactiveX - Operators - Delay](http://reactivex.io/documentation/operators/delay.html)