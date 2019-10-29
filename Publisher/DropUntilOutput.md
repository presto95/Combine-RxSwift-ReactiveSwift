# Publishers.DropUntilOutput

**제네릭 구조체** | 두 번째 Publisher로부터 요소를 전달받을 때까지 상위에 흐르는 Publisher로부터 요소를 무시하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `other` : 처음 배출되는 요소를 모니터링할 Publisher

`other`에서 요소가 처음으로 발행되기 전까지는 `upstream`에서 발행하는 요소를 무시한다.

`drop(untilOutputFrom:)` 오퍼레이터는 해당 Publisher를 반환한다.

```swift
let sourceSubject = PassthroughSubject<Void, Never>()
let otherSubject = PassthroughSubject<Void, Never>()

// 1 : Publishers.DropUntilOutput Publisher
Publishers
  .DropUntilOutput(upstream: sourceSubject, other: otherSubject)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine DropUntilOutput Error")
    case .finished:
      print("Combine DropUntilOutput Finish")
    }
  }, receiveValue: {
    print("Combine DropUntilOutput")
  })
  .store(in: &cancellables)

// 2 : drop(untilOutputFrom:) Operator
sourceSubject
  .drop(untilOutputFrom: otherSubject)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine DropUntilOutput Error")
    case .finished:
      print("Combine DropUntilOutput Finish")
    }
  }, receiveValue: {
    print("Combine DropUntilOutput")
  })
  .store(in: &cancellables)

// 3
sourceSubject.send(Void())
// 4
otherSubject.send(Void())
// 5
sourceSubject.send(Void())

// Combine DropUntilOutput
```

3의 코드를 실행하면 `sourceSubject` Subject에 값을 전달하지만, `otherSubject`가 요소를 발행한 적이 없으므로 전달된 값을 무시한다.

4의 코드를 실행하면 `otherSubject` Subject에 값을 전달한다.

5의 코드를 실행하면 `sourceSubject` Subject에 값을 전달하며, `otherSubject`가 요소를 발행한 적이 있으므로 전달된 값을 발행한다.

결과적으로 값을 전달받을 때 수행할 클로저를 실행한다.

## RxSwift

Observable 필터링 오퍼레이터 `skipUntil`을 사용하여 구현할 수 있다.

```swift
let sourceSubject = PublishSubject<Void>()
let otherSubject = PublishSubject<Void>()

sourceSubject.skipUntil(otherSubject)
  .subscribe(onNext: {
    print("RxSwift DropUntilOutput")
  }, onError: { _ in
    print("RxSwift DropUntilOutput Error")
  }, onCompleted: {
    print("RxSwift DropUntilOutput Finish")
  })
  .disposed(by: disposeBag)

sourceSubject.onNext(Void())
otherSubject.onNext(Void())
sourceSubject.onNext(Void())

// RxSwift DropUntilOutput
```

## ReactiveSwift

`skip(until:)` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
let sourceProperty = MutableProperty(Void())
let otherProperty = MutableProperty(Void())

sourceProperty.signal
  .skip(until: otherProperty.signal)
  .observe { event in
    switch event {
    case .value:
      print("ReactiveSwift DropUntilOutput")
    case .failed:
      print("ReactiveSwift DropUntilOutput Error")
    case .completed:
      print("ReactiveSwift DropUntilOutput Finish")
    default:
      break
    }
  }

sourceProperty.value = Void()
otherProperty.value = Void()
sourceProperty.value = Void()

// ReactiveSwift DropUntilOutput
// ReactiveSwift DropUntilOutput Finish
```

## 참고

[ReactiveX - Operators - Skip](http://reactivex.io/documentation/operators/skip.html)

