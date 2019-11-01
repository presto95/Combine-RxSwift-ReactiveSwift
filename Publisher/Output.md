# Publishers.Output

**제네릭 구조체** | 발행된 요소의 시퀀스 내에서 범위로 지정된 요소들을 발행하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `range` : 발행할 요소가 속한 범위

`output` 오퍼레이터와 관련이 있다.

```swift
// 1 : Publishers.Output Publisher
Publishers
  .Output(upstream: Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3]), range: 0 ..< 2)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Output Error")
    case .finished:
      print("Combine Output Finish")
    }
  }, receiveValue: { value in
    print("Combine Output : \(value)")
  })
  .store(in: &cancellables)

// Combine Output : 1
// Combine Output : 2
// Combine Output Finish

// 2 : prefix Operator
Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3])
  .prefix(2)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Output Error")
    case .finished:
      print("Combine Output Finish")
    }
  }, receiveValue: { value in
    print("Combine Output : \(value)")
  })
  .store(in: &cancellables)

// Combine Output : 1
// Combine Output : 2
// Combine Output Finish

// 3 : output Operator
Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3])
  .output(at: 1)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Output Error")
    case .finished:
      print("Combine Output Finish")
    }
  }, receiveValue: { value in
    print("Combine Output : \(value)")
  })
  .store(in: &cancellables)

// Combine Output : 2
// Combine Output Finish

// 4 : output Operator
Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3])
  .output(in: 0 ..< 2)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Output Error")
    case .finished:
      print("Combine Output Finish")
    }
  }, receiveValue: { value in
    print("Combine Output : \(value)")
  })
  .store(in: &cancellables)

// Combine Output : 1
// Combine Output : 2
// Combine Output Finish
```

각 코드의 상위 Publisher는 1, 2, 3의 값을 차례대로 낸다.

1의 코드는 `0 ..< 2`의 범위에 의해 해당 범위에 있는 1, 2의 값을 내고 종료한다.

2의 코드는 `prefix(2)` 에 의해 사용하여 앞에서부터 두 개의 요소를 발행한다. 따라서 1, 2의 값을 내고 종료한다.

3의 코드는 `output(at: 1)` 에 의해 첫 번째 인덱스에 있는 2의 값을 내고 종료한다.

4의 코드는 `output(in: 0 ..< 2)` 에 의해 범위에 있는 1, 2의 값을 내고 종료한다.

## RxSwift

`elementAt` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
Observable.from([1, 2, 3])
  .elementAt(1)
  .subscribe(onNext: { value in
    print("RxSwift Output : \(value)")
  }, onError: { _ in
    print("RxSwift Output Error")
  }, onCompleted: {
    print("RxSwift Output Finish")
  })
  .disposed(by: disposeBag)

// RxSwift Output : 2
// RxSwift Output Finish
```

## ReactiveSwift

해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - ElementAt](http://reactivex.io/documentation/operators/elementat.html)