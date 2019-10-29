# Fail

**제네릭 구조체** | 특정 에러와 함께 즉시 종료하는 Publisher

두 개의 이니셜라이저를 제공한다.

- `init(error:)`
  - Error 프로토콜을 채택하는 값을 `error` 인자에 넘겨주어 해당 에러를 내고 즉시 종료하는 Publisher를 만든다.
- `init(outputType:failure:)`
  - Error 프로토콜을 채택하는 값을 `failure` 인자에 넘겨주어 해당 에러를 내고 즉시 종료하는 Publisher를 만든다.
  - `outputType`에는 특정 Output 타입을 가진 Subscriber와 Publisher와 함께 동작해야 할 때 Output 타입의 메타 타입을 넘겨준다.

Just가 특정 값을 내려주고 종료한다면, Fail은 특정 에러와 함께 즉시 종료한다.

```swift
Fail(error: error)
  .sink(receiveCompletion: { _ in print("Combine Fail Error") }, receiveValue: { })
  .store(in: &cancellables)

// Combine Fail Error
```

Emtpy는 값을 내지 않으므로 `receiveValue` 클로저에서는 동작을 확인할 수 없고, `receiveCompletion` 클로저에서 종료하는 동작을 확인할 수 있다.

## RxSwift

Observable 생성 오퍼레이터 `error`를 사용하여 구현할 수 있다.

```swift
Observable<Void>.error(error)
  .subscribe(onError: { _ in print("RxSwift Fail Error") })
  .disposed(by: disposeBag)

// RxSwift Fail Error
```

Combine과 같이 값을 내지 않으므로 `onNext` 클로저에서는 동작을 확인할 수 없고, `onError` 클로저에서 에러를 내는 동작을 확인할 수 있다.

## ReactiveSwift

`SignalProducer`의 이니셜라이저 중 `init(error:)` 이니셜라이저를 사용하여 구현할 수 있다.

```swift
SignalProducer<Void, Error>(error: error)
  .startWithFailed { _ in print("ReactiveSwift Fail Error") }

// ReactiveSwift Fail Error
```

Combine과 같이 값을 내지 않으므로 `startWithValues` 메소드를 사용하면 동작을 확인할 수 없고, `startWithFailed` 메소드를 사용하여 에러를 내는 동작을 확인할 수 있다.

## 참고

[ReactiveX - Operators - Throw](http://reactivex.io/documentation/operators/empty-never-throw.html)