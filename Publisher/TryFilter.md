# Publishers.TryFilter

**제네릭 구조체** | 제공된 에러를 던지는 클로저와 일치하는 모든 요소를 다시 발행하는 Publisher

`Publishers.Filter`의 동작에서 에러를 던질 수 있는 기능이 추가된 형태다.

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `isIncluded` : 어떤 요소를 다시 발행할지를 나타내는 에러를 던지는 클로저를 넘겨준다. 클로저의 실행 결과는 Bool 타입이며, true이면 해당 요소를 다시 발행한다.

조건에 맞는 요소만 남기는 작업을 하고 싶으며 작업이 에러를 던질 수 있을 때 사용할 수 있다.

`tryFilter` 오퍼레이터는 해당 Publisher를 반환한다.

```swift
// 1 : Publishers.TryFilter Publisher
Publishers
  .TryFilter(upstream: Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3])) {
    if $0 == 3 {
      throw error
    }
    return $0.isMultiple(of: 2)
  }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine TryFilter Error")
    case .finished:
      print("Combine TryFilter Finish")
    }
  }, receiveValue: { value in
    print("Combine TryFilter : \(value)")
  })
  .store(in: &cancellables)

// 2 : tryFilter Operator
Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3])
  .tryFilter {
    if $0 == 3 {
      throw error
    }
    return $0.isMultiple(of: 2)
  }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine TryFilter Error")
    case .finished:
      print("Combine TryFilter Finish")
    }
  }, receiveValue: { value in
    print("Combine TryFilter : \(value)")
  })
  .store(in: &cancellables)

// Combine TryFilter : 2
// Combine TryFilter Error
```

값이 3이면 에러를 던지고, 2의 배수인 요소만을 다시 발행하는 로직을 작성하였다.

그러므로 1, 2, 3의 값을 순서대로 내는 Publisher는 다음과 같이 동작한다.

1. 1의 값은 2의 배수가 아니므로 다시 발행되지 않는다.
2. 2의 값은 2의 배수이므로 다시 발행된다.
3. 3의 값은 에러를 던지는 조건에 의해 에러를 낸다.

## RxSwift

Observable 필터링 오퍼레이터 `filter`를 사용하여 구현할 수 있다.

기본적으로 해당 오퍼레이터가 받는 변환 클로저가 에러를 던질 수 있는 클로저로 정의되어 TryFilter의 동작을 구현할 수 있다.

```swift
Observable.from([1, 2, 3])
  .filter {
    if $0 == 3 {
      throw error
    }
    return $0.isMultiple(of: 2)
  }
  .subscribe(onNext: { value in
    print("RxSwift TryCompactMap : \(value)")
  }, onError: { _ in
    print("RxSwift TryCompactMap Error")
  }, onCompleted: {
    print("RxSwift TryCompactMap Finish")
  })
  .disposed(by: disposeBag)

// RxSwift TryFilter : 2
// RxSwift TryFilter Finish
```

## ReactiveSwift

ReactiveSwift는 `filter`의 동작을 수행할 때 에러를 던질 수 있는 방법을 제공하지 않는다.

## 참고

[ReactiveX - Operators - Filter](http://reactivex.io/documentation/operators/filter.html)

[Publishers.Filter](./Filter.md)