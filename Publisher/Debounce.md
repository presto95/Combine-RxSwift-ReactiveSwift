# Publishers.Debounce

**제네릭 구조체** | 이벤트 간에 특정 시간이 지난 후에야 요소를 발행하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `dueTime` : Publisher가 요소를 발행하기 전에 기다려야 하는 시간
- `scheduler` : Publisher가 요소를 발행하는 스케줄러
- `options` : Publisher가 요소를 전달받는 것을 커스터마이징하는 스케줄러 옵션

다른 아이템을 배출하지 않고 주어진 시간이 지나야 마지막에 전달받은 아이템을 발행한다.

특정 시간이 지난 것이 요소를 발행하는 기준이 된다. 기준을 만족하지 않은 요소는 모두 무시된다.

이벤트가 발생하고 주어진 시간 동안 이벤트가 없어야 해당 이벤트를 발행한다.

사용자가 버튼을 탭하여 네트워크 요청을 수행할 수 있다고 하자. 사용자가 버튼을 탭할 때마다 매번 네트워크 요청을 하는 것은 비효율적일 것이다. 이 때 해당 동작을 사용하면 주어진 시간 내에 이루어진 사용자의 반복 입력을 무시하고, 주어진 시간 후에 들어온 마지막 입력만을 받아 처리할 수 있을 것이다.

`debounce` 오퍼레이터와 관련이 있다.

```swift
let subject = PassthroughSubject<Int, Never>()

// Publishers.Debounce Publisher
Publishers.Debounce(upstream: subject, dueTime: .seconds(1), scheduler: DispatchQueue.main, options: nil)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Debounce Error")
    case .finished:
      print("Combine Debounce Finish")
    }
  }, receiveValue: { value in 
    print("Combine Debounce : \(value)")
  })
  .store(in: &cancellables)

// debounce Operator
subject
  .debounce(for: .seconds(1), scheduler: DispatchQueue.main)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Debounce Error")
    case .finished:
      print("Combine Debounce Finish")
    }
  }, receiveValue: { value in
    print("Combine Debounce : \(value)")
  })
  .store(in: &cancellables)

for i in 0 ..< 10 {
  subject.send(i)
}

// 출력은 1초 후에 나타난다.
// Combine Debounce : 9
```

코드를 실행하면 반복문을 통해 `subject`에 0부터 9까지의 값을 전달한다.

1초의 시간을 설정하였으므로 `debounce`에 의해 0의 값이 전달된 때부터 1초 카운트가 시작된다. 하지만 연속적으로 1부터 9까지의 값이 전달되므로 매 값이 전달될 때마다 카운트가 초기화된다고 생각할 수 있다.

9의 값을 전달하고 나서야 더 이상 전달할 값이 없으므로 9의 값을 전달받고 1초가 지난 후 9의 값을 발행하게 된다.

## RxSwift

`debounce` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
let subject = PublishSubject<Int>()

subject
  .debounce(.seconds(1), scheduler: MainScheduler.instance)
  .subscribe(onNext: {
    print("RxSwift Debounce")
  }, onError: { _ in
    print("RxSwift Debounce Error")
  }, onCompleted: {
    print("RxSwift Debounce Finish")
  })
  .disposed(by: disposeBag)

for i in 0 ..< 10 {
  subject.onNext(i)
}

// 출력은 1초 후에 나타난다.
// RxSwift Debounce : 9
```

## ReactiveSwift

`debounce` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
let property = MutableProperty<Int>(0)

property.signal
  .debounce(1, on: QueueScheduler.main)
  .observe { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift Debounce : \(value)")
    case .failed:
      print("ReactiveSwift Debounce Error")
    case .completed:
      print("ReactiveSwift Debounce Finish")
    default:
      break
    }
  }

for i in 0 ..< 10 {
  property.value = i
}

// 출력은 1초 후에 나타난다.
// ReactiveSwift Debounce : 9
```

## 참고

[ReactiveX - Operators - Debounce](http://reactivex.io/documentation/operators/debounce.html)