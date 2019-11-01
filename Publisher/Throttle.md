# Publishers.Throttle

**제네릭 구조체** | 주어진 시간 내에 상위 Publisher가 발행한 가장 최신 또는 첫 번째 요소를 발행하는 Publisher

이니셜라이저는 네 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `interval` : 가장 최신의 요소를 찾고 배출할 시간 간격
- `scheduler` : 요소를 발행하는 스케줄러
- `latest` : 가장 최신의 요소를 발행하는지를 가리키는 불리언 값

요소가 배출되고 주어진 시간이 지나고서야 그 시간 내에 배출된 첫 번째 또는 가장 최신의 요소를 발행한다.

`debounce`는 주어진 시간 동안 값을 전달하지 않아야 마지막에 전달된 값을 발행하지만, `throttle`은 주어진 시간이 지날 때 그 시간 내에 배출된 첫 번째 또는 가장 최신의 요소를 발행한다.

`throttle` 오퍼레이터와 관련이 있다.

```swift
let subject = PassthroughSubject<Int, Never>()

// Publishers.Throttle Publisher
Publishers.Throttle(upstream: subject, interval: .seconds(1), scheduler: DispatchQueue.main, latest: true)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Throttle Error")
    case .finished:
      print("Combine Throttle Finish")
    }
  }, receiveValue: { value in
    print("Combine Throttle : \(value)")
  })
  .store(in: &cancellables)

// Combine Throttle : 9

// throttle Operator (latest: true)
subject
  .throttle(for: .seconds(1), scheduler: DispatchQueue.main, latest: true)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Throttle Error")
    case .finished:
      print("Combine Throttle Finish")
    }
  }, receiveValue: { value in
    print("Combine Throttle : \(value)")
  })
  .store(in: &cancellables)

// Combine Throttle : 9  

// throttle Operator (latest: false)
subject
  .throttle(for: .seconds(1), scheduler: DispatchQueue.main, latest: false)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Throttle Error")
    case .finished:
      print("Combine Throttle Finish")
    }
  }, receiveValue: { value in
    print("Combine Throttle : \(value)")
  })
  .store(in: &cancellables)

// Combine Throttle : 0  

for i in 0 ..< 10 {
  subject.send(i)
}
```

1과 2의 코드는 값이 발행되면 1초의 카운트를 제어 카운트가 끝나면 해당 시간 내에 발행된 최신의 요소를 발행한다.

3의 코드는 값이 발행되면 1초의 카운트를 제어 카운트가 끝나면 해당 시간 내에 발행된 첫 번째 요소를 발행한다.

코드를 실행하면 반복문을 통해 0부터 9까지 `subject`에 값을 전달하는데, 이 동작이 1초 안에 모두 이루어지므로 카운트가 끝날 때 스트림의 첫 번째 값은 0, 최신 값은 9일 것이다.

> 현재 런타임 에러가 발생하여 실제 동작을 확인할 수 없다. [포럼](https://forums.swift.org/t/exc-bad-instruction-with-combine-throttle/28850)

## RxSwift

`throttle` 오퍼레이터를 사용하여 구현할 수 있다.

카운트의 시작을 불러 일으키는 첫 번째 값과 카운트가 끝날 때의 최신 값을 모두 배출하는 것으로 구현되어 있다.

```swift
let subject = PublishSubject<Int>()

subject
  .throttle(.seconds(1), scheduler: MainScheduler.instance)
  .subscribe(onNext: { value in
    print("RxSwift Throttle : \(value)")
  }, onError: { _ in
    print("RxSwift Throttle Error")
  }, onCompleted: {
    print("RxSwift Throttle Finish")
  })
  .disposed(by: disposeBag)

for i in 0 ..< 10 {
  subject.onNext(i)
}

// RxSwift Throttle : 0
// RxSwift Throttle : 9
```

## ReactiveSwift

`throttle` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
let property = MutableProperty<Int>(0)

property.signal
  .throttle(1, on: QueueScheduler.main)
  .observe { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift Throttle : \(value)")
    case .failed:
      print("ReactiveSwift Throttle Error")
    case .completed:
      print("ReactiveSwift Throttle Finish")
    default:
      break
    }
}

for i in 0 ..< 10 {
  property.value = i
}

// ReactiveSwift Throttle : 9
```

## 참고

[Publishers.Debounce](./Debounce.md)