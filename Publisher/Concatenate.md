# Publishers.Concatenate

**제네릭 구조체** | 다른 Publisher가 요소를 방출하기 이전에 한 Publisher의 요소를 모두 방출하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `prefix` : `suffix`가 요소를 다시 발행하기 이전에 모든 요소를 다시 발행하는 Publisher
- `suffix` : `prefix` Publisher가 종료한 직후 다시 발행하는 Publisher

`prefix` Publisher와 `suffix` Publisher의 Output 타입은 동일해야 한다.

Publisher를 이어 붙이거나 요소를 추가할 때 사용할 수 있다.

`append` 오퍼레이터는 다음의 Publisher를 반환한다. 

- 인자에 가변 인자나 시퀀스를 넘겼을 경우 `Publishers.Sequence` Publisher를 반환한다.
- 인자에 Publisher를 넘겼을 경우 해당 Publisher를 반환한다.

`append` 오퍼레이터는 다음의 형태를 갖는다.

- 가변 인자를 받아 기존 Publisher가 종료한 후 이어서 발행한다. ex) `Just(1).append(2, 3)`
- 시퀀스를 받아 기존 Publisher가 종료한 후 이어서 발행한다. ex) `Just(1).append([2, 3])`
- Publisher를 받아 기존 Publisher가 종료한 후 이어서 발행한다. ex) `Just(1).append(Just(2))`

```swift
// Publishers.Concatenate Publisher
Publishers
  .Concatenate(prefix: Just(1), suffix: Just(2))
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Concatenate Error")
    case .finished:
      print("Combine Concatenate Finish")
    }
  }, receiveValue: { value in
    print("Combine Concatenate : \(value)")
  })
  .store(in: &cancellables)

// 2 : append Operator
Just(1)
  .append(Just(2))
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Concatenate Error")
    case .finished:
      print("Combine Concatenate Finish")
    }
  }, receiveValue: { value in
    print("Combine Concatenate : \(value)")
  })
  .store(in: &cancellables)

// Combine Concatenate : 1
// Combine Concatenate : 2
// Combine Concatenate Finish
```

1의 값을 내는 `Just` Publisher에 2의 값을 내는 `Just` Publisher를 이어 붙였다.

결과적으로 1과 2의 값을 내고 종료한다.

## RxSwift

RxSwift는 해당 기능을 구현하기 위한 오퍼레이터를 제공하지 않는다.

비슷한 기능을 하는 `startWith` 오퍼레이터가 있는데, 이것은 주어진 요소를 상위 Observable의 끝이 아닌 처음에 이어 붙인다.

```swift
Observable.just(1)
  .startWith(2)
  .subscribe(onNext: { value in
    print("RxSwift Concatenate : \(value)")
  }, onError: { _ in
    print("RxSwift Concatenate Error")
  }, onCompleted: {
    print("RxSwift Concatenate Finish")
  })
  .disposed(by: disposeBag)

// RxSwift Concatenate : 2
// RxSwift Concatenate : 1
// RxSwift Concatenate Finish
```

1과 2가 아닌, 2와 1의 순서로 값을 발행했음을 확인할 수 있다.

## ReactiveSwift

`concat` 오퍼레이터를 사용하여 구현할 수 있다.

상위 Signal의 Element 타입의 가변 인자를 이어 붙일 수도 있고, Signal을 이어 붙일 수도 있고, 에러를 이어 붙일 수도 있다.

```swift
SignalProducer(value: 1)
  .concat(value: 2)
  .start { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift Concatenate : \(value)")
    case .failed:
      print("ReactiveSwift Concatenate Error")
    case .completed:
      print("ReactiveSwift Concatenate Finish")
    default:
      break
    }
  }

// ReactiveSwift Concatenate : 1
// ReactiveSwift Concatenate : 2
// ReactiveSwift Concatenate Finish
```