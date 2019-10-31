# Publishers.CollectByTime

**제네릭 구조체** | 아이템을 일시적으로 기억하고 주기적으로 발행하는 Publisher

이니셜라이저는 세 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `strategy` : 요소를 모으고 발행하는 전략
- `options` : 전략에 사용되는 스케줄러 옵션

`strategy` 인자는 `Publishers.TimeGroupingStrategy` 타입의 인스턴스를 받으며, 다음의 케이스로 구성되어 있다.

- `byTime` : 특정 시간 동안 요소를 모으고 발행함
- `byTimeOrCount` : 특정 시간 동안 요소를 모으고 발행하거나, 버퍼가 최대 크기에 도달하면 발행함

`collect(_:options:)` 오퍼레이터는 해당 Publisher를 반환한다.

```swift
// 1 : Publishers.CollectByTime Publisher
let subject = PassthroughSubject<Int, Never>()

Publishers.CollectByTime(upstream: subject, strategy: .byTimeOrCount(DispatchQueue.main, 1, 2), options: nil)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine CollectByTime Error")
    case .finished:
      print("Combine CollectByTime Finish")
    }
  }, receiveValue: { value in
    print("Combine CollectByTime : \(value)")
  })
  .store(in: &cancellables)

// 2: collect(_:options:) Operator
subject
  .collect(.byTimeOrCount(DispatchQueue.main, 1, 2))
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine CollectByTime Error")
    case .finished:
      print("Combine CollectByTime Finish")
    }
  }, receiveValue: { value in
    print("Combine CollectByTime : \(value)")
  })
  .store(in: &cancellables)
```

두 코드 모두 `.byTimeOrCount` 전략을 설정했으며, 메인 쓰레드에서 동작하게 된다.

1초마다 버퍼에 기억한 요소를 배열의 형태로 만들어 값을 발행하거나, 시간이 지나기 전에 버퍼에 기억할 수 있는 최대의 요소인 두 개의 요소가 저장되면 요소를 배열의 형태로 만들어 값을 발행한다.

## RxSwift

Observable 변환 오퍼레이터 `buffer`를 사용하여 구현할 수 있다.

```swift
let subject = PublishSubject<Int>()

subject
  .buffer(timeSpan: .seconds(1), count: 2, scheduler: MainScheduler.instance)
  .subscribe(onNext: { value in
    print("RxSwift CollectByTime : \(value)")
  }, onError: { _ in
    print("RxSwift CollectByTime Error")
  }, onCompleted: {
    print("RxSwift CollectByTime Finish")
  })
  .disposed(by: disposeBag)
```

## ReactiveSwift

`collect(every:on:skipEmpty:discardWhenCompleted:)` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
let property = MutableProperty<Int>(0)

property.signal
  .collect(every: .seconds(1), on: QueueScheduler.main)
  .observe { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift CollectByTime : \(value)")
    case .failed:
      print("ReactiveSwift CollectByTime Error")
    case .completed:
      print("ReactiveSwift CollectByTime Finish")
    default:
      break
    }
  }
```