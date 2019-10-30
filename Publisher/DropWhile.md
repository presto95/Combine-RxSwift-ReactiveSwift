# Publishers.DropWhile

**제네릭 구조체** | 주어진 클로저가 false를 반환할 때까지 상위에 흐르는 Publisher로부터 요소를 생략하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `predicate` : 요소의 생략 여부를 결정하는 클로저

조건에 맞지 않을 때까지는 발행된 값을 무시하기 위해 사용한다.

`drop(while:)` 오퍼레이터는 다음의 Publisher를 반환한다.

- `Publishers.Sequence` Publisher를 반환한다.
- 해당 Publisher를 반환한다.

```swift
// 1 : Publishers.DropWhile Publisher
Publishers
  .DropWhile(upstream: Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3])) { $0 < 3 }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine DropWhile Error")
    case .finished:
      print("Combine DropWhile Finish")
    }
  }, receiveValue: { value in
    print("Combine DropWhile : \(value)")
  })
  .store(in: &cancellables)

// 2 : drop(while:) Operator
Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3])
  .drop { $0 < 3 }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine DropWhile Error")
    case .finished:
      print("Combine DropWhile Finish")
    }
  }, receiveValue: { value in
    print("Combine DropWhile : \(value)")
  })
  .store(in: &cancellables)

// Combine DropWhile : 3
// Combine DropWhile Finish
```

상위 Publisher가 1, 2, 3의 값을 순서대로 내며, 3 미만일 때까지 발행된 값을 생략하라는 조건에 의해 3의 값을 내고 종료한다.

## RxSwift

Observable 필터링 오퍼레이터 `skipWhile` 를 사용하여 구현할 수 있다.

```swift
Observable.from([1, 2, 3])
  .skipWhile { $0 < 3 }
  .subscribe(onNext: { value in
    print("RxSwift DropWhile : \(value)")
  }, onError: { _ in
    print("RxSwift DropWhile Error")
  }, onCompleted: {
    print("RxSwift DropWhile Finish")
  })
  .disposed(by: disposeBag)

// RxSwift DropWhile : 3
// RxSwift DropWhile Finish
```

## ReactiveSwift

`skip(while:)` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
SignalProducer([1, 2, 3])
  .skip { $0 < 3 }
  .start { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift DropWhile : \(value)")
    case .failed:
      print("ReactiveSwift DropWhile Error")
    case .completed:
      print("ReactiveSwift DropWhile Finish")
    default:
      break
    }
  }

// ReactiveSwift DropWhile : 3
// ReactiveSwift DropWhile Finish
```

## 참고

[ReactiveX - Operators - Skip](http://reactivex.io/documentation/operators/skip.html)