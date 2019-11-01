# Just

**제네릭 구조체** | 각각의 Subscriber에게 오직 한 번만 출력을 내고 종료하는 Publisher

기본적인 Publisher 중 하나로, Convenience Publisher로 정의되어 있어 `Publishers` 열거형에 속해 있지 않고 단독적인 구조체의 형태를 갖는다.

Publisher 체인을 시작할 때 사용할 수 있다.

Just의 에러 타입은 `Never`로, 이는 해당 Publisher가 에러를 내지 않는다는 것을 의미한다.

정리하자면, 값을 내고 종료하는 것과 에러를 내지 않는다는 것을 보장한다.

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

`SignalProducer`의 이니셜라이저 중 에러가 `Never`일 때 사용 가능한 `init(value:)` 이니셜라이저를 사용하여 구현할 수 있다.

```swift
SignalProducer(value: "ReactiveSwift Just")
  .startWithValues { print($0) }

// ReactiveSwift Just
```

## 참고

[ReactiveX - Operators - Just](http://reactivex.io/documentation/operators/just.html)

