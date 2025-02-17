# Publishers.ReplaceError

**제네릭 구조체** | 스트림에 있는 어떠한 에러를 제공된 요소로 교체하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `output` : 상위에 흐르는 Publisher로부터 에러를 대체하기 위해 사용되는 요소

`Publishers.Catch`가 상위 Publisher가 에러를 낼 때 다른 Publisher로 교체하는 동작을 제공한다면, `Publishers.ReplaceError`는 상위 Publisher가 에러를 낼 때 특정 요소로 교체하는 동작을 제공한다.

`Publishers.Catch`의 동작보다 더욱 간단하게 에러를 내는 Publisher를 처리하기 위해 사용할 수 있다.

`replaceError` 오퍼레이터와 관련이 있다.

```swift
// Publishers.ReplaceError Publisher
Publishers.ReplaceError(upstream: Fail(error: error), output: Void())
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine ReplaceError Error")
    case .finished:
      print("Combine ReplaceError Finish")
    }
  }, receiveValue: {
    print("Combine ReplaceError")
  })
  .store(in: &cancellables)

// replaceError Operator
Fail(error: error)
  .replaceError(with: Void())
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine ReplaceError Error")
    case .finished:
      print("Combine ReplaceError Finish")
    }
  }, receiveValue: {
    print("Combine ReplaceError")
  })
  .store(in: &cancellables)

// Combine ReplaceError
// Combine ReplaceError Finish
```

상위 Publisher가 `Fail`이어서 에러를 내는 것인데, `replaceError`를 통해 Void 값으로 에러를 대체한다.

## RxSwift

`catchErrorJustReturn` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
Observable.error(error)
  .catchErrorJustReturn(Void())
  .subscribe(onNext: {
    print("RxSwift ReplaceError")
  }, onError: { _ in
    print("RxSwift ReplaceError Error")
  }, onCompleted: {
    print("RxSwift ReplaceError Finish")
  })
  .disposed(by: disposeBag)

// RxSwift ReplaceError
// RxSwift ReplaceError Finish
```

## ReactiveSwift

해당 기능을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - Catch](http://reactivex.io/documentation/operators/catch.html)

[Publishers.Catch](./Catch.md)

