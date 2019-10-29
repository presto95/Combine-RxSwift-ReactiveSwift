# Publishers.MapError

**제네릭 구조체** | 상위에 흐르는 Publisher의 어떠한 실패를 새로운 에러로 변환하는 Publisher

제공하는 클로저에서 에러를 변환하는 작업을 수행할 수 있다.

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : `Publisher` 프로토콜을 따르는 Publisher를 넘겨준다.
- `transform` : 상위에 흐르는 Publisher의 실패를 새로운 에러로 변환하는 클로저를 넘겨준다.

에러를 변환하기 위해 사용한다. 에러를 처리하는 것이 아니므로 상위에 흐르는 Publisher가 에러를 내고 별도의 에러 처리 작업이 없다면 에러를 내며 종료하게 된다.

`mapError` 오퍼레이터는 해당 Publisher를 반환한다.

```swift
// 1 : Publishers.MapError Publisher
Publishers.MapError(upstream: Fail<Void, NSError>(error: error), { $0 as Error })
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine MapError Error")
    case .finished:
      print("Combine MapError Finish")
    }
  }, receiveValue: {
    print("Combine MapError")
  })
  .store(in: &cancellables)

// 2 : mapError Operator
Fail(error: error)
  .mapError { $0 as Error }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine MapError Error")
    case .finished:
      print("Combine MapError Finish")
    }
  }, receiveValue: {
    print("Combine MapError")
  })
  .store(in: &cancellables)

// Combine MapError Error
```

두 코드 모두 상위 Publisher로 `NSError` 객체를 받는 `Fail` Publisher를 사용하였다.

 `$0 as Error`를 통해 `NSError` 타입이 `Error` 타입으로 매핑된다.

상위에 흐르는 Publisher가 에러를 내므로 결과적으로 에러를 내며 종료한다.

## RxSwift

내부적으로 에러의 타입으로 `Error`를 노출하므로 `mapError`와 같이 에러의 타입을 변환하는 오퍼레이터를 제공하지 않는다.

```swift
Observable<Void>.error(error)
  .subscribe(onNext: {
    print("RxSwift MapError")
  }, onError: { _ in
    print("RxSwift MapError Error")
  }, onCompleted: {
    print("RxSwift MapError Finish")
  })
  .disposed(by: disposeBag)

// RxSwift MapError Error
```

## ReactiveSwift

`mapError` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
SignalProducer<Void, NSError>(error: error)
  .mapError { $0 as Error }
  .start { event in
    switch event {
    case .value:
      print("ReactiveSwift MapError")
    case .failed:
      print("ReactiveSwift MapError Error")
    case .completed:
      print("ReactiveSwift MapError Finish")
    default:
      break
    }
  }
```