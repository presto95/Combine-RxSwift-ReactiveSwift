# Empty

**제네릭 구조체** | 어떠한 값도 절대 발행하지 않으며, 선택적으로 즉시 종료하는 Publisher

두 개의 이니셜라이저를 제공한다.

- `init(completeImmediately:)` 
  - 인자의 기본값은 true이다.
  - 인자의 값이 true이면 즉시 종료하고, false이면 종료하지 않는다.
- `init(completeImmediately:outputType:failureType)`
  - `completeImmediately` 인자의 기본값은 true이며, 값에 따른 동작 변화는 위와 같다.
  - `outputType`과 `failureType`은 해당 Publisher를 Subscriber나 다른 Publisher와 연결할 때 연결할 대상의 Output 타입과 에러 타입을 맞추기 위해 명시해 준다.
    - 그러므로 인자에 메타 타입을 전달한다.

값을 전달하고 싶지 않으며, 즉시 종료하거나 종료하지 않는 Publisher를 만들고 싶을 때 사용한다.

```swift
// 1
Empty<Void, Never>()
  .sink(receiveCompletion: { _ in print("Combine Empty Finish") }, receiveValue: { })
  .store(in: &cancellables)

// Combine Empty Finish

// 2
Empty<Void, Never>(completeImmediately: false)
  .sink(receiveCompletion: { _ in print("Combine Never Finish") }, receiveValue: { })
  .store(in: &cancellables)

// 아무것도 출력되지 않음
```

Emtpy는 값을 내지 않으므로 `receiveValue` 클로저에서는 동작을 확인할 수 없고, `receiveCompletion` 클로저에서 종료하는 동작을 확인할 수 있다.

1번 코드의 경우 Publisher가 바로 종료하여 `receiveCompletion`에 등록한 클로저가 실행되는 것을 확인할 수 있다.

2번 코드의 경우 Publisher가 종료하지 않으므로 아무런 출력도 확인할 수 없다.

## RxSwift

Observable 생성 오퍼레이터 `empty`와 `never`를 사용하여 구현할 수 있다.

`empty`의 경우 Combine의 `Empty(completeImmediately: true)`의 동작과 같다.

`never`의 경우 Combine의 `Empty(completeImmediately: false)`의 동작과 같다.

```swift
// 1
Observable<Void>.empty()
  .subscribe(onNext: nil, onCompleted: { print("RxSwift Empty Finish") })
  .disposed(by: disposeBag)

// RxSwift Empty Finish

// 2
Observable<Void>.never()
  .subscribe(onNext: nil, onCompleted: { print("RxSwift Never Finish" )})
  .disposed(by: disposeBag)

// 아무것도 출력되지 않음
```

Combine과 같이 값을 내지 않으므로 `onNext` 클로저에서는 동작을 확인할 수 없고, `onCompleted` 클로저에서 종료하는 동작을 확인할 수 있다.

1번 코드의 경우 Observable이 바로 종료하여 `onCompleted`에 등록한 클로저가 실행되는 것을 확인할 수 있다.

2번 코드의 경우 Observable이 종료하지 않으므로 아무런 출력도 확인할 수 없다.

## ReactiveSwift

`SignalProducer`의 타입 저장 프로퍼티인 `empty`와 `never`를 사용하여 구현할 수 있다.

`empty`의 경우 Combine의 `Empty(completeImmediately: true)`의 동작과 같다.

`never`의 경우 Combine의 `Empty(completeImmediately: false)`의 동작과 같다.

```swift
// 1
SignalProducer<Void, Never>.empty
  .startWithCompleted { print("ReactiveSwift Empty Finish") }

// ReactiveSwift Empty Finish

// 2
SignalProducer<Void, Never>.never
  .startWithCompleted { print("ReactiveSwift Never Finish") }

// 아무것도 출력되지 않음
```

Combine과 같이 값을 내지 않으므로 `startWithValues` 메소드를 사용하면 동작을 확인할 수 없고, `startWithCompleted` 메소드를 사용하여 종료하는 동작을 확인할 수 있다.

1번 코드의 경우 Signal이 바로 종료하여 등록한 클로저가 실행되는 것을 확인할 수 있다.

2번 코드의 경우 Signal이 종료하지 않으므로 아무런 출력도 확인할 수 없다.

## 참고

[ReactiveX - Operators - Empty | Never](http://reactivex.io/documentation/operators/empty-never-throw.html)