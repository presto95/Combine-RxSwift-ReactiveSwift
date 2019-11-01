# Publishers.MeasureInterval

**제네릭 구조체** | 상위 Publisher로부터 전달받은 이벤트들 사이의 시간 간격을 측정하고 배출하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `scheduler` : 요소를 전달하는 스케줄러

이벤트가 일어나는 시간의 간격을 측정하여 발행한다.

`measureInterval` 오퍼레이터와 관련이 있다.

```swift
let subject = PassthroughSubject<Void, Never>()

// Publishers.MeasureInterval Publisher
Publishers.MeasureInterval(upstream: subject, scheduler: DispatchQueue.main)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine MeasureInterval Error")
    case .finished:
      print("Combine MeasureInterval Finish")
    }
  }, receiveValue: { value in
    print("Combine MeasureInterval : \(value.timeInterval)")
  })
  .store(in: &cancellables)

// measureInterval Operator
subject
  .measureInterval(using: DispatchQueue.main)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine MeasureInterval Error")
    case .finished:
      print("Combine MeasureInterval Finish")
    }
  }, receiveValue: { value in
    print("Combine MeasureInterval : \(value.timeInterval)")
  })
  .store(in: &cancellables)

Timer.scheduledTimer(withTimeInterval: 0.1, repeats: true) { _ in
  subject.send(Void())
}

// Combine MeasureInterval : nanoseconds(100535457)
// Combine MeasureInterval : nanoseconds(101047592)
// Combine MeasureInterval : nanoseconds(99104295)
// ...
```

0.1초마다 `subject`에 Void 값을 전달하도록 하였다.

이벤트 간에 0.1초 언저리의 시간 간격이 발생하여 시간 간격이 값으로 발행된다.

## RxSwift

해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## ReactiveSwift

해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - TimeInterval](http://reactivex.io/documentation/operators/timeinterval.html)