# Just

**제네릭 구조체** | 각각의 Subscriber에게 오직 한 번만 출력을 내고 종료하는 Publisher

이니셜라이저는 한 개의 인자를 받는다.

- `output` : Publihser가 배출할 한 개의 요소

기본적인 Publisher 중 하나로, Convenience Publisher로 정의되어 있어 `Publishers` 열거형에 속해 있지 않고 단독적인 구조체의 형태를 갖는다.

값을 내고 종료하는 것과 에러를 내지 않는다는 것을 보장한다.

```swift
Just("Combine Just")
  .sink(receiveValue: { print($0) })
  .store(in: &cancellables)

// Combine Just
```

## RxSwift

`just` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
Observable.just("RxSwift Just")
  .subscribe(onNext: { print($0) })
  .disposed(by: disposeBag)

// RxSwift Just
```

## ReactiveSwift

`init(value:)` 이니셜라이저를 사용하여 구현할 수 있다.

```swift
SignalProducer(value: "ReactiveSwift Just")
  .startWithValues { print($0) }

// ReactiveSwift Just
```

## 참고

[ReactiveX - Operators - Just](http://reactivex.io/documentation/operators/just.html)

