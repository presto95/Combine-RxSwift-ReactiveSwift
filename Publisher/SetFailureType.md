# Publishers.SetFailureType

**제네릭 구조체** | 특정 실패 타입을 전달하는 것처럼 보이는 Publisher

이는 실제로 특정 타입의 에러를 내지는 않고 정상적으로 종료하지만, 다른 에러 타입이 맞지 않는 Publisher와 함께 동작하기 위해 에러의 타입을 일치시키기 위해 사용할 수 있다.

예를 들어 1번 Publisher가 에러를 내지 않고 (에러의 타입이 Never) 2번 Publisher가 에러를 내는데 두 개의 Publisher를 조합해야 한다면 1번 Publisher가 2번 Publisher의 에러 타입의 에러를 내는 것처럼 행동하도록 하여 타입을 맞추어 주어야 할 필요가 있을 것이다.

이 때 해당 Publisher를 사용하여 실제로 특정 타입의 에러를 내지는 않지만 에러의 타입을 맞춰줄 수 있다.

이니셜라이저로 상위에 흐르는 Publisher를 받는다.

`setFailureType` 오퍼레이터는 해당 Publisher를 반환한다.

```swift
// 1 : Publishers.SetFailureType Publisher
Just(Void())
  .setFailureType(to: SomeError.self)
  .combineLatest(Fail<Void, SomeError>(error: .some))
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine SetFailureType Error")
    case .finished:
      print("Combine SetFailureType Finish")
    }
  }, receiveValue: { _ in
    print("Combine SetFailureType")
  })
  .store(in: &cancellables)

// 2 : setFailureType Operator
Publishers.SetFailureType<Just<Void>, SomeError>(upstream: Just(Void()))
  .combineLatest(Fail<Void, SomeError>(error: .some))
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine SetFailureType Error")
    case .finished:
      print("Combine SetFailureType Finish")
    }
  }, receiveValue: { _ in
    print("Combine SetFailureType")
  })
  .store(in: &cancellables)

// Combine SetFailureType Error
```

`Just` Publisher는 에러를 내지 않는 Publisher이지만 `Fail` Publisher와 조합하기 위해 `Fail` Publisher의 에러 타입인 `SomeError` 타입의 에러로 타입을 맞추기 위해 `setFailureType`이 요구하는 에러의 타입에 `SomeError.self`를 명시해 주었다.

이를 통해 `Just` Publisher와 `Fail` Publisher는 조합이 가능하게 되었다.

코드 실행 결과로, `Fail` Publisher가 내는 에러가 처리되지 않았기 때문에 조합된 Publisher는 에러를 내게 된다.

## RxSwift

RxSwift는 Observable이 에러의 타입을 요구하지 않으므로 이러한 동작을 구현할 필요가 없고, 구현되어 있지도 않다.

## ReactiveSwift

`promoteError` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
SignalProducer(value: Void())
  .promoteError(SomeError.self)
  .combineLatest(with: SignalProducer<Void, SomeError>(error: .some))
  .start { event in
    switch event {
    case .value:
      print("ReactiveSwift SetFailureType")
    case .failed:
      print("ReactiveSwift SetFailureType")
    case .completed:
      print("ReactiveSwift SetFailureType")
    default:
      break
    }
  }
```

