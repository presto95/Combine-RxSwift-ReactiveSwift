# Publishers.Reduce

**제네릭 구조체** | 전달받은 모든 요소에 클로저를 적용하고, 상위에 흐르는 Publisher가 종료할 때 축적된 값을 내는 Publisher

Swift의 `reduce` 함수와 같은 동작을 한다.

상위 Publisher가 배출한 항목에 함수를 순서대로 적용하고 함수를 연산한 후 최종 결과를 발행한다.

이니셜라이저는 세 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `initial` : 클로저의 첫 번째 호출에 제공되는 초기 값
- `nextPartialResult` : 새로운 값을 내기 위해 이전까지 축적된 값과 상위 Publisher의 다음 요소를 취하는 클로저

상위 Publisher에 흐르는 모든 값을 모아 어떠한 하나의 값을 내고 싶을 때 사용할 수 있다.

`reduce` 오퍼레이터는 해당 Publisher를 반환한다.

```swift
// 1 : Publishers.Reduce Publisher
Publishers.Reduce(upstream: Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3]), initial: 0) { $0 + $1 }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Reduce Error")
    case .finished:
      print("Combine Reduce Finish")
    }
  }, receiveValue: { value in
    print("Combine Reduce : \(value)")
  })
  .store(in: &cancellables)

// 2 : reduce Operator
Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3])
  .reduce(0) { $0 + $1 }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Reduce Error")
    case .finished:
      print("Combine Reduce Finish")
    }
  }, receiveValue: { value in
    print("Combine Reduce : \(value)")
  })
  .store(in: &cancellables)

// Combine Reduce : 6
// Combine Reduce Finish
```

상위 Publisher가 1, 2, 3의 값을 차례대로 내는데, 초기 값을 0으로 하고 중첩된 이전의 값과 새로운 값에 덧셈 연산을 하는 클로저를 제공하여 요소를 모두 더한 결과를 내도록 하였다.

결과적으로 6의 값을 내고 종료한다.

## RxSwift

Observable 수학 및 집계 오퍼레이터 `reduce`를 사용하여 구현할 수 있다.

```swift
Observable.from([1, 2, 3])
  .reduce(0) { $0 + $1 }
  .subscribe(onNext: { value in
    print("RxSwift Reduce : \(value)")
  }, onError: { _ in
    print("RxSwift Reduce Error")
  }, onCompleted: {
    print("RxSwift Reduce Finish")
  })
  .disposed(by: disposeBag)

// RxSwift Reduce : 6
// RxSwift Reduce Finish
```

## ReactiveSwift

`reduce` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
SignalProducer([1, 2, 3])
  .reduce(0) { $0 + $1 }
  .start { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift Reduce : \(value)")
    case .failed:
      print("ReactiveSwift Reduce Error")
    case .completed:
      print("ReactiveSwift Reduce Finish")
    default:
      break
    }
  }

// ReactiveSwift Reduce : 6
// ReactiveSwift Reduce Finish
```

## 참고

[ReactiveX - Operators - Reduce](http://reactivex.io/documentation/operators/reduce.html)