# Publishers.Map

**제네릭 구조체** | 제공된 클로저를 사용하여 상위에 흐르는 Publisher로부터 모든 요소들을 변환하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `transform` : 상위에 흐르는 Publisher로부터 요소들을 변환하기 위한 클로저

변환 함수를 적용하여 요소를 변환하기 위해 사용한다.

Swift의 `map` 함수와 같은 동작을 한다.

`map` 오퍼레이터와 관련이 있다.

```swift
// Publishers.Map Publisher
Publishers.Map(upstream: Publishers.Sequence<[Int], Never>(sequence: [1, 2])) { $0 * 2 }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Map Error")
    case .finished:
      print("Combine Map Finish")
    }
  }, receiveValue: { number in
    print("Combine Map : \(number)")
  })
  .store(in: &cancellables)

// map Operator
Publishers.Sequence<[Int], Never>(sequence: [1, 2])
  .map { $0 * 2 }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Map Error")
    case .finished:
      print("Combine Map Finish")
    }
  }, receiveValue: { number in
    print("Combine Map : \(number)")
  })
  .store(in: &cancellables)

// Combine Map : 2
// Combine Map : 4
// Combine Map Finish
```

`Publishers.Sequence` Publisher에서 1과 2의 값이 차례대로 나오고, 이 요소에 대하여 2를 곱하는 변환 함수를 적용하여 2와 4의 값을 낸 후 종료한다.

## RxSwift

`map` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
Observable.from([1, 2])
  .map { $0 * 2 }
  .subscribe(onNext: { number in
    print("RxSwift Map : \(number)")
  }, onError: { _ in
    print("RxSwift Map Error")
  }, onCompleted: {
    print("RxSwift Map Finish")
  })
  .disposed(by: disposeBag)

// RxSwift Map : 2
// RxSwift Map : 4
// RxSwift Map Finish
```

## ReactiveSwift

`map` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
SignalProducer([1, 2])
  .map { $0 * 2 }
  .start { event in
    switch event {
    case let .value(number):
      print("ReactiveSwift Map : \(number)")
    case .failed:
      print("ReactiveSwift Map Error")
    case .completed:
      print("ReactiveSwift Map Finish")
    default:
      break
    }
  }

// ReactiveSwift Map : 2
// ReactiveSwift Map : 4
// ReactiveSwift Map Finish
```

## 참고

[ReactiveX - Operators - Map](http://reactivex.io/documentation/operators/map.html)