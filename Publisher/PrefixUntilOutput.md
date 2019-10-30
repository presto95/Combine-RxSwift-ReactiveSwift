# Publishers.PrefixUntilOutput

**제네릭 구조체**

현재 문서화는 되어 있지 않다.

다른 Publisher가 값을 낼 때까지 기존 Publisher가 요소를 발행하는 동작을 구현하기 위해 사용한다.

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `other` : 첫 출력이 `upstream` Publisher가 종료하게 하는 Publisher

`prefix(untilOutputFrom:)` 오퍼레이터는 해당 Publisher를 반환한다.

```swift
let sourceSubject = PassthroughSubject<Int, Never>()
let otherSubject = PassthroughSubject<Int, Never>()

// 1 : Publishers.PrefixUntilOutput Publisher
Publishers
  .PrefixUntilOutput(upstream: sourceSubject, other: otherSubject)
    .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine PrefixUntilOutput Error")
    case .finished:
      print("Combine PrefixUntilOutput Finish")
    }
  }, receiveValue: { value in
    print("Combine PrefixUntilOutput : \(value)")
  })
  .store(in: &cancellables)

// 2 : prefix(untilOutputFrom:) Operator
sourceSubject
  .prefix(untilOutputFrom: otherSubject)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine PrefixUntilOutput Error")
    case .finished:
      print("Combine PrefixUntilOutput Finish")
    }
  }, receiveValue: { value in
    print("Combine PrefixUntilOutput : \(value)")
  })
  .store(in: &cancellables)

// 3
sourceSubject.send(1)
// 4
otherSubject.send(2)
// 5
sourceSubject.send(3)

// Combine PrefixUntilOutput : 1
// Combine PrefixUntilOutput Finish
```

3의 코드를 실행하면 `sourceSubject`에 1의 값을 전달하며, `otherSubject`가 값을 발행한 적이 없으므로 그대로 1의 값을 낸다.

4의 코드를 실행하면 `otherSubject`에 2의 값을 전달한다. 이 때 `sourceSubject`는 다른 Publisher가 값을 전달받았으므로 종료한다.

5의 코드를 실행하면 `sourceSubject`에 3의 값을 전달한다. 하지만 `sourceSubject`는 4의 코드의 실행에 의해 이미 종료하였으므로 효력이 없다.

## RxSwift

Observable 필터링 오퍼레이터 `takeUntil`을 사용하여 구현할 수 있다.

```swift
let sourceSubject = PublishSubject<Int>()
let otherSubject = PublishSubject<Int>()

sourceSubject
  .takeUntil(otherSubject)
  .subscribe(onNext: { value in
    print("RxSwift PrefixUntilOutput : \(value)")
  }, onError: { _ in
    print("RxSwift PrefixUntilOutput Error")
  }, onCompleted: {
    print("RxSwift PrefixUntilOutput Finish")
  })
  .disposed(by: disposeBag)

sourceSubject.onNext(1)
otherSubject.onNext(2)
sourceSubject.onNext(3)

// RxSwift PrefixUntilOutput : 1
// RxSwift PrefixUntilOutput Finish
```

## ReactiveSwift

`take(until:)` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
let sourceProperty = MutableProperty(0)
let otherProperty = MutableProperty(0)

sourceProperty.signal
  .take(until: otherProperty.signal.map(value: Void()))
  .observe { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift PrefixUntilOutput : \(value)")
    case .failed:
      print("ReactiveSwift PrefixUntilOutput Error")
    case .completed:
      print("ReactiveSwift PrefixUntilOutput Finish")
    default:
      break
    }
  }

sourceProperty.value = 1
otherProperty.value = 2
sourceProperty.value = 3

// ReactiveSwift PrefixUntilOutput : 1
// ReactiveSwift PrefixUntilOutput Finish
```

## 참고

[ReactiveX - Operators - Take](http://reactivex.io/documentation/operators/take.html)