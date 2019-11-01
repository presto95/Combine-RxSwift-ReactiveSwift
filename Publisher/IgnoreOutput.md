# Pulishers.IgnoreOutput

**제네릭 구조체** | 상위에 흐르는 요소는 모두 무시하나, 완료 상태(종료 또는 실패)는 따르는 Publisher

이니셜라이저는 한 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher

상위에 흐르는 Publisher의 에러나 완료와 같은 완료 상태만 받아 처리하고 싶을 때 사용할 수 있다.

`ignoreOutput` 오퍼레이터와 관련이 있다.

```swift
// Publishers.IgnoreOutput Publisher
Publishers
  .IgnoreOutput(upstream: Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3]))
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine IgnoreOutput Error")
    case .finished:
      print("Combine IgnoreOutput Finish")
    }
  }, receiveValue: { value in
    print("Combine IgnoreOutput : \(value)")
   })
  .store(in: &cancellables)

// ignoreOutput Operator
Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3])
  .ignoreOutput()
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine IgnoreOutput Error")
    case .finished:
      print("Combine IgnoreOutput Finish")
    }
  }, receiveValue: { value in
    print("Combine IgnoreOutput : \(value)")
  })
  .store(in: &cancellables)

// Combine IgnoreOutput Finish
```

상위 Publisher가 1, 2, 3의 값을 차례대로 내지만 `ignoreOutput`에 의해 모두 무시되고, 정상적으로 종료하기 때문에 종료에 대해서만 처리한다.

## RxSwift

`ignoreElements` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
Observable.from([1, 2, 3])
  .ignoreElements()
  .subscribe(onCompleted: {
    print("RxSwift IgnoreOutput Finish")
  }, onError: { _ in
    print("RxSwift IgnoreOutput Error")
  })
  .disposed(by: disposeBag)

// RxSwift IgnoreOutput Finish
```

## ReactiveSwift

해당 기능을 구현할 수 있는 방법을 제공하지 않는다.

## 참고

[ReactiveX - Operators - IgnoreElements](http://reactivex.io/documentation/operators/ignoreelements.html)

