# Publishers.Comparison

**제네릭 구조체** | 다른 Publisherd에서 각각의 새로운 아이템에 대하여 이전에 발행된 아이템과 비교했을 때 오름차순일 때만 아이템을 다시 발행하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `areInIncreasingOrder` : 두 개의 요소를 전달받고 이들이 오름차순이면 true를 반환하는 클로저
  - 첫 번째 인자는 현재 발행되는 요소, 두 번째 인자는 이전에 발행된 요소를 나타낸다.
  - 클로저의 매 실행마다 이 두 개의 값을 비교하여 조건에 맞는 요소를 반환한다.
  - 조건에 따라 오름차순 및 내림차순 설정을 할 수 있다.

상위 Publisher이 내는 값들을 정렬하는 것이 아니라, 오름차순 또는 내림차순 조건에 맞는 하나의 값만을 낸다.

`max`, `min` 오퍼레이터는 해당 Publisher를 반환한다. 이 때 Output 타입이 `Comparable` 프로토콜을 채택하는 경우 별도의 클로저를 구현할 필요가 없다.

```swift
Publishers
  .Comparison(upstream: Publishers.Sequence<[Int], Never>(sequence: [1, 3, 2, 5])) { $0 > $1 }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Comparison Error")
    case .finished:
      print("Combine Comparison Finish")
    }
  }, receiveValue: { value in
    print("Combine Comparison : \(value)")
  })
  .store(in: &cancellables)

// Combine Comparison : 5
// Combine Comparison Finish
```

상위 Publishers는 1, 3, 2, 5의 값을 차례대로 내며, 다음과 같이 동작한다.

`$0 > $1`은 조건을 오름차순으로 설정한다.

1. 1의 값을 내며, 첫 번째로 발행된 요소이므로 넘어간다.
2. 3의 값을 내며, 3 > 1이 true이므로 3을 발행한다.
3. 2의 값을 내며, 2 > 3이 false이므로 3을 발행한다.
4. 5의 값을 내며, 5 > 3이 true이므로 5를 발행한다.

최종적으로 5의 값을 낸다.

```swift
// 1 : max Operator
Publishers.Sequence<[Int], Never>(sequence: [1, 3, 2, 4])
  .max()
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Comparison Error")
    case .finished:
      print("Combine Comparison Finish")
    }
  }, receiveValue: { value in
    print("Combine Comparison : \(value)")
  })
  .store(in: &cancellables)

// Combine Comparison : 4
// Combine Comparison Finish

// 2 : min Operator
Publishers.Sequence<[Int], Never>(sequence: [1, 3, 2, 4])
  .min()
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Comparison Error")
    case .finished:
      print("Combine Comparison Finish")
    }
  }, receiveValue: { value in
    print("Combine Comparison : \(value)")
  })
  .store(in: &cancellables)

// Combine Comparison : 1
// Combine Comparison Finish
```

`max` 및 `min` 오퍼레이터는 말 그대로 상위 Publisher가 종료할 때까지 내어진 값들 중 최대 / 최소 값을 반환한다.

## RxSwift

RxSwift는 해당 기능을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## ReactiveSwift

ReactiveSwift는 해당 기능을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - Max](http://reactivex.io/documentation/operators/max.html)

[ReactiveX - Operators - Min](http://reactivex.io/documentation/operators/min.html)