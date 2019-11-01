# Publishers.Concatenate

**제네릭 구조체** | 다른 Publisher가 요소를 방출하기 이전에 한 Publisher의 요소를 모두 방출하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `prefix` : `suffix`가 요소를 다시 발행하기 이전에 모든 요소를 다시 발행하는 Publisher
- `suffix` : `prefix` Publisher가 종료한 직후 다시 발행하는 Publisher

`prefix` Publisher와 `suffix` Publisher의 Output 타입과 에러 타입은 동일해야 한다.

Publisher를 이어 붙이거나 요소를 추가할 때 사용할 수 있다.

`append` 및 `prepend` 오퍼레이터와 관련이 있다.

`append` 오퍼레이터는 다음의 형태를 갖는다.

- 가변 인자를 받아 기존 Publisher가 종료한 후 이어서 발행한다. ex) `Just(1).append(2, 3)`
- 시퀀스를 받아 기존 Publisher가 종료한 후 이어서 발행한다. ex) `Just(1).append([2, 3])`
- Publisher를 받아 기존 Publisher가 종료한 후 이어서 발행한다. ex) `Just(1).append(Just(2))`

`prepend` 오퍼레이터는 다음의 형태를 갖는다.

- 가변 인자를 받아 기존 Publisher의 앞에 이어 붙여 발행한다. ex) `Just(1).prepend(2, 3)`
- 시퀀스를 받아 기존 Publisher의 앞에 이어 붙여 발행한다. ex) `Just(1).prepend([2, 3])`
- Publisher를 받아 기존 Publisher의 앞에 이어 붙여 발행한다. ex) `Just(1).prepend(Just(2))`

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

// append Operator
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

// prepend Operator
Just(1)
  .prepend(Just(2))
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

// Combine Concatenate : 2
// Combine Concatenate : 1
// Combine Concatenate Finish
```

1번 코드와 2번 코드의 경우 1의 값을 내는 `Just` Publisher의 끝에 2의 값을 내는 `Just` Publisher를 이어 붙였다. 결과적으로 1과 2의 값을 차례대로 내고 종료한다.

3번 코드의 경우 1의 값을 내는 `Just` Publisher의 앞에 2의 값을 내는 `Just` Publisher를 이어 붙였다. 결과적으로 2와 1의 값을 차례대로 내고 종료한다.

## RxSwift

`concat` 오퍼레이터를 사용하여 `append` 오퍼레이터의 기능을 구현할 수 있다.

`startWith` 오퍼레이터를 사용하여 `prepend` 오퍼레이터의 기능을 구현할 수 있다.

```swift
// concat Operator
Observable.just(1)
  .concat(Observable.just(2))
  .subscribe(onNext: { value in
    print("RxSwift Concatenate : \(value)")
  }, onError: { _ in
    print("RxSwift Concatenate Error")
  }, onCompleted: {
    print("RxSwift Concatenate Finish")
  })
  .disposed(by: disposeBag)

// RxSwift Concatenate : 1
// RxSwift Concatenate : 2
// RxSwift Concatenate Finish

// startWith Operator
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

## ReactiveSwift

`concat` 오퍼레이터를 사용하여 `append` 오퍼레이터의 기능을 구현할 수 있다.

`prefix` 오퍼레이터를 사용하여 `prepend` 오퍼레이터의 기능을 구현할 수 있다.

```swift
// concat Operator
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

// prefix Operator
SignalProducer(value: 1)
  .prefix(value: 2)
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

// ReactiveSwift Concatenate : 2
// ReactiveSwift Concatenate : 1
// ReactiveSwift Concatenate Finish
```

## 참고

[ReactiveX - Operators - StartWith](http://reactivex.io/documentation/operators/startwith.html)

