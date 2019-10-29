# Deferred

**제네릭 구조체** | 새로운 Subscriber를 위한 Publisher를 생성하기 위해 제공된 클로저를 실행하기 전에 구독을 기다리는 Publisher

이니셜라이저는 지연 실행할 Publisher를 만드는 클로저를 받는다. 이 클로저는 Deferred Publisher를 구독할 때마다 실행되어 각각의 옵저버가 새로운 스트림을 받도록 한다.

```swift
Deferred { Just(Void()) }
  .sink(receiveValue: { print("Combine Deferred") })
  .store(in: &cancellables)

// Combine Deferred
```

## RxSwift

Observable 생성 오퍼레이터 `deferred`를 사용하여 구현할 수 있다.

```swift
Observable.deferred { Observable.just(Void()) }
  .subscribe(onNext: { print("RxSwift Deferred") })
  .disposed(by: disposeBag)

// RxSwift Deferred
```

## ReactiveSwift

ReactiveSwift에는 `defer`와 같은 오퍼레이터를 제공하지 않지만, 다음의 [이슈](https://github.com/ReactiveCocoa/ReactiveSwift/issues/42)에서 `SignalProducer`가 해당 개념을 구현하고 있다는 것을 확인할 수 있다.

`SignalProducer`는 이를 구독하는 옵저버가 없다면 스트림을 생성하지 않으며, 이를 구독하는 각각의 옵저버마다 새로운 스트림을 만들기 때문이다.

```swift
SignalProducer(value: Void())
  .startWithValues { print("ReactiveSwift Deferred") }

// ReactiveSwift Deferred
```

## 참고

[ReactiveX - Operators - Defer](http://reactivex.io/documentation/operators/defer.html)