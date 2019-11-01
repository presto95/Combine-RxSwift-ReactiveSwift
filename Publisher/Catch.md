# Publishers.Catch

**제네릭 구조체** | 실패하는 Publisher를 다른 Publisher로 교체하여 상위에 흐르는 Publisher로부터 에러를 처리하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `handler` : 상위에 흐르는 Publisher가 에러를 내는 경우 새로운 Publisher를 만드는 클로저

상위에 흐르는 Publisher와 새로운 Publisher의 Output 타입은 동일해야 한다.

상위 Publisher가 에러를 내는 경우 에러를 내며 종료하는 대신 다른 Publisher로 대체한다.

`catch` 오퍼레이터와 관련이 있다.

```swift
// Publishers.Catch Publisher
Publishers.Catch(upstream: Fail(error: error)) { _ in Just("Error") }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Catch Error")
    case .finished:
      print("Combine Catch Finish")
    }
  }, receiveValue: {
    print("Combine Catch : \($0)")
  })
  .store(in: &cancellables)

// catch Operator
Fail(error: error)
  .catch { _ in Just("Error") }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Catch Error")
    case .finished:
      print("Combine Catch Finish")
    }
  }, receiveValue: {
    print("Combine Catch : \($0)")
  })
  .store(in: &cancellables)

// Combine Catch : Error
// Combine Catch Finish
```

위의 코드는 실패하는 Publisher인 `Fail`로부터 `catch` 오퍼레이터를 사용하여 에러를 내고 종료하는 대신 `Just` Publisher로 대체한다.

## RxSwift

`catchError` 오퍼레이터를 사용하여 구현할 수 있다. 

```swift
Observable.error(error)
  .catchError { _ in Observable.just("Error") }
  .subscribe(onNext: {
    print("RxSwift Catch : \($0)")
  }, onError: { _ in
    print("RxSwift Catch Error")
  }, onCompleted: {
    print("RxSwift Catch Finish")
  })
  .disposed(by: disposeBag)

// RxSwift Catch : Error
// RxSwift Catch Finish
```

## ReactiveSwift

`flatMapError` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
SignalProducer(error: error)
  .flatMapError { error in SignalProducer(value: "Error") }
  .start { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift Catch : \(value)")
    case .failed:
      print("ReactiveSwift Catch Error")
    case .completed:
      print("ReactiveSwift Catch Finish")
    default:
      break
    }
  }

// ReactiveSwift Catch : Error
// ReactiveSwift Catch Finish
```

## 참고

[ReactiveX - Operators - Catch](http://reactivex.io/documentation/operators/catch.html)