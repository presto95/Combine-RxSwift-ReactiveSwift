# Publishers.TryReduce

**제네릭 구조체** | 전달받은 모든 요소에 에러를 던지는 클로저를 적용하고, 상위에 흐르는 Publisher가 종료할 때 축적된 값을 내는 Publisher

`Publishers.Reduce`의 동작에서 에러를 던질 수 있는 기능이 추가된 형태다.

이니셜라이저는 세 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `initial` : 클로저의 첫 번째 호출에 제공되는 초기 값
- `nextPartialResult` : 새로운 값을 내기 위해 이전까지 축적된 값과 상위 Publisher의 다음 요소를 취하는 에러를 던지는 클로저

상위 Publisher에 흐르는 모든 값을 모아 어떠한 하나의 값을 내고 싶으며 해당 작업이 에러를 낼 수 있을 때 사용할 수 있다.

`tryReduce` 오퍼레이터는 해당 Publisher를 반환한다.

```swift
// 1 : Publishers.TryReduce Publisher
Publishers
  .TryReduce(upstream: Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3]), initial: 0) {
    if $1 == 3 { throw error }
    return $0 + $1
  }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine TryReduce Error")
    case .finished:
      print("Combine TryReduce Finish")
    }
  }, receiveValue: { value in
    print("Combine TryReduce : \(value)")
  })
  .store(in: &cancellables)

// 2 : tryReduce Operator
Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3])
  .tryReduce(0) {
    if $1 == 3 { throw error }
    return $0 + $1
  }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine TryReduce Error")
    case .finished:
      print("Combine TryReduce Finish")
    }
  }, receiveValue: { value in
    print("Combine TryReduce : \(value)")
  })
  .store(in: &cancellables)

// Combine TryReduce Error
```

상위 Publisher는 1, 2, 3의 값을 차례대로 낸다. 코드는 다음과 같이 동작한다.

1. 1의 값은 초기 값 0과 더해져 1의 값을 축적한다.
2. 2의 값은 축적된 값 1과 더해져 3의 값을 축적한다.
3. 3의 값은 에러를 내는 조건에 의해 에러를 낸다.

값이 모두 사용되기 전에 에러를 내므로 최종적으로 에러를 낸다.

## RxSwift

Observable 수학 및 집계 오퍼레이터 `reduce`를 사용하여 구현할 수 있다.

기본적으로 해당 오퍼레이터가 받는 변환 클로저가 에러를 던질 수 있는 클로저로 정의되어 TryReduce의 동작을 구현할 수 있다.

```swift
Observable.from([1, 2, 3])
  .reduce(0) {
    if $1 == 3 { throw error }
    return $0 + $1
  }
  .subscribe(onNext: { value in
    print("RxSwift TryReduce : \(value)")
  }, onError: { _ in
    print("RxSwift TryReduce Error")
  }, onCompleted: {
    print("RxSwift TryReduce Finish")
  })
  .disposed(by: disposeBag)

// RxSwift TryReduce Error
```

## ReactiveSwift

ReactiveSwift는 `reduce`의 동작을 수행할 때 에러를 던질 수 있는 방법을 제공하지 않는다.

## 참고

[ReactiveX - Operators - Reduce](http://reactivex.io/documentation/operators/reduce.html)

[Publishers.Reduce](./Reduce.md)