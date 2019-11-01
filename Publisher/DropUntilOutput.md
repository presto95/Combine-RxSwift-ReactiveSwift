# Publishers.DropUntilOutput

**제네릭 구조체** | 두 번째 Publisher로부터 요소를 전달받을 때까지 상위에 흐르는 Publisher로부터 요소를 무시하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `other` : 처음 배출되는 요소를 모니터링할 Publisher

`other`에서 요소가 처음으로 발행되기 전까지는 `upstream`에서 발행하는 요소를 무시한다.

`drop` 오퍼레이터와 관련이 있다.

```swift
let sourceSubject = PassthroughSubject<Int, Never>()
let otherSubject = PassthroughSubject<Int, Never>()

// Publishers.DropUntilOutput Publisher
Publishers
  .DropUntilOutput(upstream: sourceSubject, other: otherSubject)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine DropUntilOutput Error")
    case .finished:
      print("Combine DropUntilOutput Finish")
    }
  }, receiveValue: { value in
    print("Combine DropUntilOutput : \(value)")
  })
  .store(in: &cancellables)

// drop Operator
sourceSubject
  .drop(untilOutputFrom: otherSubject)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine DropUntilOutput Error")
    case .finished:
      print("Combine DropUntilOutput Finish")
    }
  }, receiveValue: { value in
    print("Combine DropUntilOutput : \(value)")
  })
  .store(in: &cancellables)

// 1
sourceSubject.send(1)
// 2
otherSubject.send(2)
// 3
sourceSubject.send(3)

// Combine DropUntilOutput : 3
```

다음과 같은 순서로 코드가 동작한다.

1. 1의 코드를 실행하면 `sourceSubject` Subject에 1의 값을 전달하지만, `otherSubject`가 요소를 발행한 적이 없으므로 전달된 값을 무시한다.
2. 2의 코드를 실행하면 `otherSubject` Subject에 2의 값을 전달한다.
3. 3의 코드를 실행하면 `sourceSubject` Subject에 3의 값을 전달하며, `otherSubject`가 요소를 발행한 적이 있으므로 전달된 값을 발행한다.

결과적으로 값을 전달받을 때 수행할 클로저를 실행한다.

## RxSwift

`skipUntil` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
let sourceSubject = PublishSubject<Int>()
let otherSubject = PublishSubject<Int>()

sourceSubject.skipUntil(otherSubject)
  .subscribe(onNext: { value in
    print("RxSwift DropUntilOutput : \(value)")
  }, onError: { _ in
    print("RxSwift DropUntilOutput Error")
  }, onCompleted: {
    print("RxSwift DropUntilOutput Finish")
  })
  .disposed(by: disposeBag)

sourceSubject.onNext(1)
otherSubject.onNext(2)
sourceSubject.onNext(3)

// RxSwift DropUntilOutput : 3
```

## ReactiveSwift

`skip` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
let sourceProperty = MutableProperty(0)
let otherProperty = MutableProperty(0)

sourceProperty.signal
  .skip(until: otherProperty.signal)
  .observe { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift DropUntilOutput : \(value)")
    case .failed:
      print("ReactiveSwift DropUntilOutput Error")
    case .completed:
      print("ReactiveSwift DropUntilOutput Finish")
    default:
      break
    }
  }

sourceProperty.value = 1
otherProperty.value = 2
sourceProperty.value = 3

// ReactiveSwift DropUntilOutput : 3
// ReactiveSwift DropUntilOutput Finish
```

## 참고

[ReactiveX - Operators - Skip](http://reactivex.io/documentation/operators/skip.html)

