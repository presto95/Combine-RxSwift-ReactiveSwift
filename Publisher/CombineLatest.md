# Publishers.CombineLatest

**제네릭 구조체** | 두 개의 Publisher로부터 최신의 요소를 전달받고 조합하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `a` : 조합할 Publisher 중 하나
- `b` : 조합할 Publisher 중 또다른 하나

`a` Publisher와 `b` Publisher의 에러 타입은 같아야 한다.

두 개의 Publisher에서 최신의 요소를 발행했을 때 이들을 조합하는 동작을 원할 때 사용할 수 있다.

예를 들어 두 개의 입력 폼이 있고 모두 값이 입력되어야 버튼을 활성화해야 한다는 요구사항이 있을 때 이 동작을 사용할 수 있다. 두 개의 입력에 대한 Publisher에서 모두 값을 발행해야 하기 때문이다.

다른 하나의 Publisher를 인자로 받는 `combineLatest(_:)` 오퍼레이터는 해당 Publisher를 반환한다. 이 때 조합 결과는 튜플의 형태로 발행된다.

다른 하나의 Publisher를 인자로 받고 transform 클로저를 명시한 `combineLatest(_:_:)` 오퍼레이터는 `Publishers.Map` Publisher를 반환한다.

```swift
let aSubject = PassthroughSubject<Int, Never>()
let bSubject = PassthroughSubject<Int, Never>()

// 1 : Publishers.CombineLatest Publisher
Publishers
  .CombineLatest(aSubject, bSubject)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine CombineLatest Error")
    case .finished:
      print("Combine CombineLatest Finish")
    }
  }, receiveValue: { value in
    print("Combine CombineLatest : \(value)")
  })
  .store(in: &cancellables)

// Combine CombineLatest : (2, 3)
// Combine CombineLatest : (2, 4)
// Combine CombineLatest Finish

// 2 : combineLatest(_:) Operator
aSubject
  .combineLatest(bSubject)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine CombineLatest Error")
    case .finished:
      print("Combine CombineLatest Finish")
    }
  }, receiveValue: { value in
    print("Combine CombineLatest : \(value)")
  })
  .store(in: &cancellables)

// Combine CombineLatest : (2, 3)
// Combine CombineLatest : (2, 4)
// Combine CombineLatest Finish

// 3 : combineLatest(_:_:) Operator
aSubject
  .combineLatest(bSubject) { $0 * $1 }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine CombineLatest Error")
    case .finished:
      print("Combine CombineLatest Finish")
    }
  }, receiveValue: { value in
    print("Combine CombineLatest : \(value)")
  })
  .store(in: &cancellables)

// Combine CombineLatest : 6
// Combine CombineLatest : 8
// Combine CombineLatest Finish

// 4
aSubject.send(1)
// 5
aSubject.send(2)
// 6
bSubject.send(3)
// 7
bSubject.send(4)
```

두 개의 Subject로부터 최신의 요소를 전달받고 조합하도록 하였다.

1번 및 2번 코드의 경우 다음과 같이 동작한다.

1. 4의 실행에 의해 `aSubject`에 1의 값을 전달한다. `bSubject`에 값이 전달된 적이 없으므로 해당 Publisher는 값을 내지 않는다.
2. 5의 실행에 의해 `aSubject`에 2의 값을 전달한다. `bSubject`에 값이 전달된 적이 없으므로 해당 Publisher는 값을 내지 않는다.
3. 6의 실행에 의해 `bSubject`에 3의 값을 전달한다. `aSubject`에 값이 전달된 적이 있으므로 해당 Publisher는 `aSubject`의 최신 값인 2와 `bSubject`의 최신 값인 3을 튜플의 형태로 조합하여 (2, 3)의 값을 낸다.
4. 7의 실행에 의해 `bSubject`에 4의 값을 전달한다. `aSubject`에 값이 전달된 적이 있으므로 해당 Publisher는 `aSubject`의 최신 값인 2와 `bSubject`의 최신 값인 4를 튜플의 형태로 조합하여 (2, 4)의 값을 낸다.

3번 코드의 경우 다음과 같이 동작한다.

1. 4의 실행에 의해 `aSubject`에 1의 값을 전달한다. `bSubject`에 값이 전달된 적이 없으므로 해당 Publisher는 값을 내지 않는다.
2. 5의 실행에 의해 `aSubject`에 2의 값을 전달한다. `bSubject`에 값이 전달된 적이 없으므로 해당 Publisher는 값을 내지 않는다.
3. 6의 실행에 의해 `bSunject`에 3의 값을 전달한다. `aSubject`에 값이 전달된 적이 있으므로 해당 Publisher는 `aSubject`의 최신 값인 2와 `bSubject`의 최신 값인 3을 곱하여 6의 값을 낸다.
4. 7의 실행에 의해 `bSunject`에 4의 값을 전달한다. `aSubject`에 값이 전달된 적이 있으므로 해당 Publisher는 `aSubject`의 최신 값인 2와 `bSubject`의 최신 값인 4를 곱하여 8의 값을 낸다.

## RxSwift

Observable 결합 오퍼레이터 `combineLatest`를 사용하여 구현할 수 있다.

```swift
let aSubject = PublishSubject<Int>()
let bSubject = PublishSubject<Int>()

Observable.combineLatest(aSubject, bSubject)
  .subscribe(onNext: { value in
    print("RxSwift CombineLatest : \(value)")
  }, onError: { _ in
    print("RxSwift CombineLatest Error")
  }, onCompleted: {
    print("RxSwift CombineLatest Finish")
  })
  .disposed(by: disposeBag)

aSubject.onNext(1)
aSubject.onNext(2)
bSubject.onNext(3)
bSubject.onNext(4)

// RxSwift CombineLatest : (2, 3)
// RxSwift CombineLatest : (2, 4)
```

## ReactiveSwift

`combineLatest` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
let aProperty = MutableProperty<Int>(0)
let bProperty = MutableProperty<Int>(0)

aProperty.signal
  .combineLatest(with: bProperty.signal)
  .observe { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift CombineLatest : \(value)")
    case .failed:
      print("ReactiveSwift CombineLatest Error")
    case .completed:
      print("ReactiveSwift CombineLatest Finish")
    default:
      break
    }
  }

aProperty.value = 1
aProperty.value = 2
bProperty.value = 3
bProperty.value = 4

// ReactiveSwift CombineLatest : (2, 3)
// ReactiveSwift CombineLatest : (2, 4)
// ReactiveSwift CombineLatest Finish
```

## 참고

[ReactiveX - Operators - CombineLatest](http://reactivex.io/documentation/operators/combinelatest.html)