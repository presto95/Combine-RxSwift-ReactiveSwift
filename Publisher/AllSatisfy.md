# Publishers.AllSatisfy

**제네릭 구조체** | 전달받은 모든 요소가 주어진 조건을 통과하는지를 나타내는 하나의 불리언 값을 발행하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `predicate` : 각각의 전달받은 요소를 평가하는 클로저

상위 Publisher가 내는 모든 값들이 주어진 조건을 통과하는지를 나타내는 불리언 값을 반환한다.

모든 값들이 주어진 조건을 만족하는지 알기 위해 사용할 수 있다.

`allSatisfy` 오퍼레이터와 관련이 있다.

```swift
// Publishers.AllSatisfy Publisher
Publishers
  .AllSatisfy(upstream: Publishers.Sequence<[Int], Never>(sequence: [2, 4, 7])) { $0.isMultiple(of: 2) }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine AllSatisfy Error")
    case .finished:
      print("Combine AllSatisfy Finish")
    }
  }, receiveValue: { value in
    print("Combine AllSatisfy : \(value)")
  })
  .store(in: &cancellables)

// allSatisfy Operator
Publishers.Sequence<[Int], Never>(sequence: [2, 4, 7])
  .allSatisfy { $0.isMultiple(of: 2) }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine AllSatisfy Error")
    case .finished:
      print("Combine AllSatisfy Finish")
    }
  }, receiveValue: { value in
    print("Combine AllSatisfy : \(value)")
  })
  .store(in: &cancellables)

// Combine AllSatisfy : false
// Combine AllSatisfy Finish
```

상위 Publisher가 2, 4, 7의 값을 차례대로 내고, 조건으로 값이 2의 배수인지를 확인하게 하였다.

2, 4의 값은 2의 배수이나 7의 값은 2의 배수가 아니므로 모든 값들이 조건을 만족하지 않게 된다.

결과적으로 false를 반환하고 종료한다.

## RxSwift

해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## ReactiveSwift

해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - All](http://reactivex.io/documentation/operators/all.html)