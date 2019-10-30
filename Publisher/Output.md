# Publishers.Output

**제네릭 구조체** | 발행된 요소의 시퀀스 내에서 범위로 지정된 요소들을 발행하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `range` : 발행할 요소가 속한 범위.

아래와 같이 다양한 오퍼레이터 구현이 있으며, 각 오퍼레이터는 다음의 Publisher를 반환한다.

- `output(at:)` 오퍼레이터는 `Optional.Publisher` Publisher를 반환한다.
- `output(in:)` 오퍼레이터는 `Publishers.Sequence` Publisher를 반환한다.
- `prefix(_:)` 오퍼레이터는 `Publishers.Sequence` Publisher를 반환한다.
- 위에 명시한 모든 오퍼레이터는 해당 Publisher를 반환한다.

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

// 2 : prefix(_:) Operator
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

// 3 : output(at:) Operator
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

// 4 : output(in:) Operator
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

1의 코드는 `0 ..< 2`의 범위를 제공하여 해당 범위에 있는 1, 2의 값을 내고 종료한다.

2의 코드는 `prefix(2)` 오퍼레이터를 사용하여 앞에서부터 두 개의 요소를 발행한다. 따라서 1, 2의 값을 내고 종료한다.

3의 코드는 `output(at: 1)` 오퍼레이터를 사용하여 1번째 인덱스에 있는 2의 값을 내고 종료한다.

4의 코드는 `output(in: 0 ..< 2)` 오퍼레이터를 사용하여 해당 범위에 있는 1, 2의 값을 내고 종료한다.

## RxSwift

RxSwift는 해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

Observable 필터링 오퍼레이터 `elementAt(_:)`을 사용하여 `output(at:)` 오퍼레이터의 동작을 구현할 수 있다.

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

ReactiveSwift는 해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - ElementAt](http://reactivex.io/documentation/operators/elementat.html)