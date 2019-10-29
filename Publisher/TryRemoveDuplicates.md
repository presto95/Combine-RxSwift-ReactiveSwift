# Publishers.TryRemoveDuplicates

**제네릭 구조체** | 이전 요소와 일치하지 않는 요소만 발행하는데, 제공된 에러를 던지는 클로저를 사용하여 일치 여부를 평가하는 Publisher

발행되려는 요소가 이전에 발행된 것과 일치한다면 해당 요소를 발행하지 않는다.

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `predicate` : 두 요소가 중복되었는지 평가하기 위한 에러를 던지는 클로저. 

중복된 값이 들어오는 경우 같은 값을 반복 발행하는 것을 방지하며 해당 작업이 에러를 던질 수 있을 때 사용한다.

`tryRemoveDuplicates` 오퍼레이터는 해당 Publisher를 반환한다.

```swift
// 1 : Publishers.TryRemoveDuplicates Publisher
Publishers
  .TryRemoveDuplicates(upstream: Publishers.Sequence<[Int], Never>(sequence: [1, 2, 2, 3])) {
    if $1 == 3 {
      throw SomeError.some
    }
    return $0 == $1
  }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine TryRemoveDuplicates Error")
    case .finished:
      print("Combine TryRemoveDuplicates Finish")
    }
  }, receiveValue: { value in
    print("Combine TryRemoveDuplicates : \(value)")
  })
  .store(in: &cancellables)

// 2 : tryRemoveDuplicates Operator
Publishers.Sequence<[Int], Never>(sequence: [1, 2, 2, 3])
  .tryRemoveDuplicates {
    if $1 == 3 {
      throw SomeError.some
    }
    return $0 == $1
  }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine TryRemoveDuplicates Error")
    case .finished:
      print("Combine TryRemoveDuplicates Finish")
    }
  }, receiveValue: { value in
    print("Combine TryRemoveDuplicates : \(value)")
  })
  .store(in: &cancellables)

// Combine TryRemoveDuplicates : 1
// Combine TryRemoveDuplicates : 2
// Combine TryRemoveDuplicates Error
```

기본적으로 요소가 중복되는 경우 값을 내는 것을 무시하도록 하였고, 3의 값이 발행되려 한다면 에러를 내도록 하였다.

그러므로 1, 2, 2, 3의 값을 차례대로 내는 Publisher는 다음과 같이 동작한다.

1. 1의 값에서 이전에 발행된 값이 없으므로 다시 발행된다.
2. 2의 값에서 이전에 발행된 값이 1이므로 중복되지 않아 다시 발행된다.
3. 2의 값에서 이전에 발행된 값이 2이므로 중복되어 다시 발행되지 않는다.
4. 3의 값에서 에러는 내는 로직에 의해 에러를 낸다.

## RxSwift

Observable 필터링 오퍼레이터 `distinctUntilChanged`를 사용하여 구현할 수 있다.

기본적으로 해당 오퍼레이터가 받는 변환 클로저가 에러를 던질 수 있는 클로저로 정의되어 TryRemoveDuplicates의 동작을 구현할 수 있다.

```swift
Observable.from([1, 2, 2, 3])
  .distinctUntilChanged {
    if $1 == 3 {
      throw SomeError.some
    }
    return $0 == $1
  }
  .subscribe(onNext: { value in
    print("RxSwift TryRemoveDuplicates : \(value)")
  }, onError: { _ in
    print("RxSwift TryRemoveDuplicates Error")
  }, onCompleted: {
    print("RxSwift TryRemoveDuplicates Finish")
  })
  .disposed(by: disposeBag)

// RxSwift TryRemoveDuplicates : 1
// RxSwift TryRemoveDuplicates : 2
// RxSwift TryRemoveDuplicates Error
```

## ReactiveSwift

ReactiveSwift는 `skipRepeats`의 동작을 수행할 때 에러를 던질 수 있는 방법을 제공하지 않는다.

## 참고

[ReactiveX - Operators - Distinct](http://reactivex.io/documentation/operators/distinct.html)

[Publishers.RemoveDuplicates](./RemoveDuplicates.md)