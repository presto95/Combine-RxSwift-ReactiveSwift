# Publishers.Drop

**제네릭 구조체** | 나중에 요소가 다시 발행되기 이전에 명시한 개수의 요소를 생략하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `count` : 생략할 요소의 개수

조건 없이 앞에서 몇 개의 요소를 생략하고 싶을 때 사용 가능하다.

초기값을 갖는 `CurrentValueSubject`를 사용하는 경우, 초기값을 무시하기 위해 사용할 수 있다.

`dropFirst` 오퍼레이터와 관련이 있다.

```swift
// Publishers.Drop Publisher
Publishers
  .Drop(upstream: Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3]), count: 2)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Drop Error")
    case .finished:
      print("Combine Drop Finish")
    }
  }, receiveValue: { value in
    print("Combine Drop : \(value)")
  })
  .store(in: &cancellables)

// dropFirst Operator
Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3])
  .dropFirst(2)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Drop Error")
    case .finished:
      print("Combine Drop Finish")
    }
  }, receiveValue: { value in
    print("Combine Drop : \(value)")
  })
  .store(in: &cancellables)

// Combine Drop : 3
// Combine Drop Finish
```

상위 Publisher는 1, 2, 3의 값을 차례대로 낸다.

2개의 요소를 생략하게 하였으므로 최종적으로 3의 값을 내고 종료한다.

## RxSwift

`skip` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
Observable.from([1, 2, 3])
  .skip(2)
  .subscribe(onNext: { value in
    print("RxSwift Drop : \(value)")
  }, onError: { _ in
    print("RxSwift Drop Error")
  }, onCompleted: {
    print("RxSwift Drop Finish")
  })
  .disposed(by: disposeBag)

// RxSwift Drop : 3
// RxSwift Drop Finish
```

## ReactiveSwift

`skip` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
SignalProducer([1, 2, 3])
  .skip(first: 2)
  .start { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift Drop : \(value)")
    case .failed:
      print("ReactiveSwift Drop Error")
    case .completed:
      print("ReactiveSwift Drop Finish")
    default:
      break
    }
  }

// ReactiveSwift Drop : 3
// ReactiveSwift Drop Finish
```

## 참고

[ReactiveX - Operators - Skip](http://reactivex.io/documentation/operators/skip.html)

