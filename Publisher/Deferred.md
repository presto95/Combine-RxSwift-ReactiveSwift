# Deferred

**제네릭 구조체** | 새로운 Subscriber를 위한 Publisher를 생성하기 위해 제공된 클로저를 실행하기 전에 구독을 기다리는 Publisher

이니셜라이저는 한 개의 인자를 받는다.

- `createPublisher` : `subscribe`를 호출할 때 실행할 클로저. 클로저는 지연 실행할 Publisher를 반환한다.

이니셜라이저는 지연 실행할 Publisher를 만드는 클로저를 받는다. 이 클로저는 `Deferred` Publisher를 구독할 때마다 실행되어 각각의 옵저버가 새로운 스트림을 받도록 한다.

```swift
Deferred { Just(Void()) }
  .sink(receiveValue: { print("Combine Deferred") })
  .store(in: &cancellables)

// Combine Deferred
```

## RxSwift

`deferred` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
Observable.deferred { Observable.just(Void()) }
  .subscribe(onNext: { print("RxSwift Deferred") })
  .disposed(by: disposeBag)

// RxSwift Deferred
```

## ReactiveSwift

해당 동작을 구현하는 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - Defer](http://reactivex.io/documentation/operators/defer.html)