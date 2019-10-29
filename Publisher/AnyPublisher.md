# AnyPublisher

**제네릭 구조체** | 타입을 지운 Publisher

Subscriber나 다른 Publisher에게 Publisher의 상세 타입을 노출하고 싶지 않을 때 특정 Publisher를 AnyPublisher에 래핑하여 사용한다.

Combine에서는 각각의 Publisher가 `Just`, `Fail`, `Publishers.Map`과 같은 별도의 타입을 가지며, 각 오퍼레이터 또한 별도의 타입을 반환한다.

그렇기 때문에 최종 결과는 타입을 지워서 외부에 노출할 필요가 있다.

각 Publisher의 `eraseToAnyPublisher()` 메소드를 사용하면 타입을 지운 `AnyPublisher` 타입을 반환하게 된다.

```swift
func combinePublisher() -> AnyPublisher<Void, Never> {
  Just(Void()).eraseToAnyPublisher()
}

combinePublisher()
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine AnyPublisher Error")
    case .finished:
      print("Combine AnyPublisher Finish")
    }
  }, receiveValue: {
    print("Combine AnyPublisher")
  })
  .store(in: &cancellables)

// Combine AnyPublisher
// Combine AnyPublisher Finish
```

## RxSwift

RxSwift의 각 오퍼레이터는 Observable의 값이나 에러의 제네릭 타입을 바꾸는 것으로 동작하기 때문에 타입을 지울 필요가 없다. 

Observable이 AnyPublisher의 역할을 하는 것이다.

```swift
func rxSwiftObservable() -> Observable<Void> {
  Observable.just(Void())
}

rxSwiftObservable()
  .subscribe(onNext: {
    print("RxSwift AnyPublisher")
  }, onError: { _ in
    print("RxSwift AnyPublisher Error")
  }, onCompleted: {
    print("RxSwift AnyPublisher Finish")
  })
  .disposed(by: disposeBag)

// RxSwift AnyPublisher
// RxSwift AnyPublisher Finish
```

## ReactiveSwift

ReactiveSwift의 각 오퍼레이터는 Signal이나 SignalProducer의 값이나 에러의 제네릭 타입을 바꾸는 것으로 동작하기 때문에 타입을 지울 필요가 없다. 

Signal과 SignalProducer가 AnyPublisher의 역할을 하는 것이다.

```swift
func reactiveSwiftSignalProducer() -> SignalProducer<Void, Never> {
  SignalProducer(value: Void())
}

reactiveSwiftSignalProducer()
  .start { event in
    switch event {
    case .value:
      print("ReactiveSwift AnyPublisher")
    case .failed:
      print("ReactiveSwift AnyPublisher Error")
    case .completed:
      print("ReactiveSwift AnyPublisher Finish")
    default:
      break
    }
  }

// ReactiveSwift AnyPublisher
// ReactiveSwift AnyPublisher Finish
```

## 참고

[Wikipedia - Type erasure](https://en.wikipedia.org/wiki/Type_erasure)