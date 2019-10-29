# Publishers.ReplaceError

**제네릭 구조체** | 스트림에 있는 어떠한 에러를 제공된 요소로 교체하는 Publisher

`Publishers.Catch`가 상위 Publisher가 에러를 낼 때 다른 Publisher로 교체하는 동작을 제공한다면, `Publishers.ReplaceError`는 상위 Publisher가 에러를 낼 때 특정 요소로 교체하는 동작을 제공한다.

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `output` : 상위에 흐르는 Publisher로부터 에러를 대체하기 위해 사용되는 요소

`Publishers.Catch`의 동작보다 더욱 간단하게 에러를 내는 Publisher를 처리하기 위해 사용할 수 있다.

`replaceError` 오퍼레이터는 해당 Publisher를 반환한다.

```swift
// 1 : Publishers.ReplaceError Publisher
Publishers.ReplaceError(upstream: Fail(error: NSError()), output: Void())
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

// 2 : replaceError Operator
Fail(error: NSError())
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

Observable 오류 처리 오퍼레이터 `catchErrorJustReturn`을 사용하여 구현할 수 있다.

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

`flatMapError` 오퍼레이터를 사용하여 상위 스트림이 에러를 낼 때 다른 Signal로 변환할 수 있으나, 제공된 요소로 교체하는 동작은 제공하지 않는다.

## 참고

[ReactiveX - Operators - Catch](http://reactivex.io/documentation/operators/catch.html)

[Publishers.Catch](./Catch.md)

