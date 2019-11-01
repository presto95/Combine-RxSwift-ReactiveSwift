# Publishers.Timeout

**제네릭 구조체**

이니셜라이저는 다섯 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `interval` : 타임아웃을 일으킬 시간
- `scheduler` : 요소를 발행하는 스케줄러
- `options` : 스케줄러 옵션
- `customError` : 타임아웃이 발생했을 때 발행할 에러를 만드는 클로저

상위 Publisher에 주어진 시간 동안 요소가 전달되지 않을 때 타임아웃이 발생하여 Publisher를 종료한다.

`customError`가 nil이면 종료하며, nil이 아니면 에러를 낸다.

`timeout` 오퍼레이터와 관련이 있다.

```swift
let subject = PassthroughSubject<Int, Error>()

// Publishers.Timeout (customError != nil)
Publishers.Timeout(upstream: subject, interval: .milliseconds(500), scheduler: DispatchQueue.main, options: nil) { error }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine TimeOut Error")
    case .finished:
      print("Combine TimeOut Finish")
    }
  }, receiveValue: { value in
    print("Combine TimeOut : \(value)")
  })
  .store(in: &cancellables)

// Combine Timeout Error

// timeout Operator (customError == nil)
subject
  .timeout(.milliseconds(500), scheduler: DispatchQueue.main)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine TimeOut Error")
    case .finished:
      print("Combine TimeOut Finish")
    }
  }, receiveValue: { value in
    print("Combine TimeOut : \(value)")
  })
  .store(in: &cancellables)

// Combine Timeout Finish
```

`subject`에 값을 전달하는 코드가 없으므로, 코드를 실행하면 0.5초 후에 타임아웃이 발생한다.

`customError`를 정의한 경우 타임아웃이 발생할 때 에러를 내며, `customError`를 정의하지 않은 경우 타임아웃이 발생할 때 종료한다.

## RxSwift

`timeout` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
let subject = PublishSubject<Int>()

subject
  .timeout(.milliseconds(500), scheduler: MainScheduler.instance)
  .subscribe(onNext: { value in
    print("RxSwift Timeout : \(value)")
  }, onError: { _ in
    print("RxSwift Timeout Error")
  }, onCompleted: {
    print("RxSwift Timeout Finish")
  })
  .disposed(by: disposeBag)

// RxSwift Timeout Error
```

## ReactiveSwift

`timeout` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
let property = MutableProperty<Int>(0)

property.signal
  .timeout(after: 0.5, raising: error, on: QueueScheduler.main)
  .observe { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift Timeout : \(value)")
    case .failed:
      print("ReactiveSwift Timeout Error")
    case .completed:
      print("ReactiveSwift Timeout Finish")
    default:
      break
    }
  }

// ReactiveSwift Timeout Error
```