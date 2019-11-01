# Publishers.CollectByCount

**제네릭 구조체** | 아이템의 최대 개수를 일시적으로 기억하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `count` : 발행하기 이전에 기억할 전달받은 요소의 최대 개수

기억할 수 있는 요소의 최대 개수를 채우면 배열의 형태로 값을 한꺼번에 낸다.

요소의 최대 개수를 채우지 못하고 에러를 내면 기억된 값을 모두 무시하고 에러를 낸다.

요소의 최대 개수를 채우지 못하고 종료하면 배열의 형태로 기억된 값을 한꺼번에 낸 후 종료한다.

`collect` 오퍼레이터와 관련이 있다.

```swift
let subject = PassthroughSubject<Int, Never>()

// Publishers.CollectByCount Publisher
Publishers.CollectByCount(upstream: subject, count: 2)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine CollectByCount Error")
    case .finished:
      print("Combine CollectByCount Finish")
    }
  }, receiveValue: { value in
    print("Combine CollectByCount : \(value)")
  })
  .store(in: &cancellables)

// collect Operator
subject
  .collect(2)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine CollectByCount Error")
    case .finished:
      print("Combine CollectByCount Finish")
    }
  }, receiveValue: { value in
    print("Combine CollectByCount : \(value)")
  })
  .store(in: &cancellables)

// 1
subject.send(1)
// 2
subject.send(2)
// 3
subject.send(3)
// 4
subject.send(4)
// 5
subject.send(5)
// 6 
subject.send(completion: .finished)

// Combine CollectByCount : [1, 2]
// Combine CollectByCount : [3, 4]
// Combine CollectByCount : [5]
// Combine CollectByCount Finish
```

두 코드 모두 최대 두 개의 요소를 기억할 수 있도록 하였다.

코드는 다음과 같이 동작한다.

1. 1의 코드에 의해 `subject`에 1의 값을 전달한다. 이 값은 기억되고, 최대 개수를 채우지 못했으므로 값을 발행하지 않는다.
2. 2의 코드에 의해 `subject`에 2의 값을 전달한다. 이 값은 기억되고, 최대 개수를 채웠으므로 [1, 2]의 값을 발행한다.
3. 3의 코드에 의해 `subject`에 3의 값을 전달한다. 이 값은 기억되고, 최대 개수를 채우지 못했으므로 값을 발행하지 않는다.
4. 4의 코드에 의해 `subject`에 4의 값을 전달한다. 이 값은 기억되고, 최대 개수를 채웠으므로 [3, 4]의 값을 발행한다.
5. 5의 코드에 의해 `subject`에 5의 값을 전달한다. 이 값을 기억되고, 최대 개수를 채우지 못했으므로 값을 발행하지 않는다.
6. 6의 코드에 의해 `subject`에 종료를 전달한다. 버퍼에 기억된 5의 값을 배열의 형태로 만들어 [5]의 값을 발행하고 종료한다.

## RxSwift

`buffer` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
let subject = PublishSubject<Int>()

subject
  .buffer(timeSpan: .never, count: 2, scheduler: MainScheduler.instance)
  .subscribe(onNext: { value in
    print("RxSwift CollectByCount : \(value)")
  }, onError: { _ in
    print("RxSwift CollectByCount Error")
  }, onCompleted: {
    print("RxSwift CollectByCount Finish")
  })
  .disposed(by: disposeBag)

subject.onNext(1)
subject.onNext(2)
subject.onNext(3)
subject.onNext(4)
subject.onNext(5)
subject.onCompleted()

// RxSwift CollectByCount : [1, 2]
// RxSwift CollectByCount : [3, 4]
// RxSwift CollectByCount : [5]
// RxSwift CollectByCount Finish
```

## ReactiveSwift

`collect` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
let property = MutableProperty<Int>(0)

property.signal
  .collect(count: 2)
  .observe { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift CollectByCount : \(value)")
    case .failed:
      print("ReactiveSwift CollectByCount Error")
    case .completed:
      print("ReactiveSwift CollectByCount Finish")
    default:
      break
    }
  }

property.value = 1
property.value = 2
property.value = 3

// ReactiveSwift CollectByCount : [1, 2]
// ReactiveSwift CollectByCount : [3]
// ReactiveSwift CollectByCount Finish
```

## 참고

[ReactiveX - Operators - To](http://reactivex.io/documentation/operators/to.html)