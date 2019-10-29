# Pulishers.IgnoreOutput

**제네릭 구조체** | 상위에 흐르는 요소는 모두 무시하나, 완료 상태(종료 또는 실패)는 따르는 Publisher

이니셜라이저는 상위에 흐르는 Publisher를 받는다.

해당 Publisher의 Output 타입은 Never이며, 에러의 타입은 상위 Publisher의 에러 타입을 따라간다.

상위에 흐르는 Publisher의 완료 상태만 받아 처리하고 싶을 때 사용할 수 있다.

`ignoreOutput` 오퍼레이터는 해당 Publisher나 `Emtpy` Publisher를 반환한다. 값을 모두 무시하여 빈 스트림이 되기 때문이다.

```swift
// 1 : Publishers.IgnoreOutput Publisher
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

// 2 : ignoreOutput Operator
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

Observable 필터링 오퍼레이터 `ignoreElements`를 사용하여 구현할 수 있다.

해당 오퍼레이터는 `Completable` 타입을 반환하므로, 에러 또는 종료 이벤트에 대해서만 처리할 수 있다.

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

ReactiveSwift는 해당 기능을 구현할 수 있는 방법을 제공하지 않는다.

## 참고

[ReactiveX - Operators - IgnoreElements](http://reactivex.io/documentation/operators/ignoreelements.html)

