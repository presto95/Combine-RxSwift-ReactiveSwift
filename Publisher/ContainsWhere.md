# Publishers.ContainsWhere

**제네릭 구조체** | 조건 클로저를 만족하는 요소를 전달받느냐에 따라 불리언 값을 방출하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `predicate` : 요소가 조건과 맞는지 판단하기 위한 클로저

`predicate`에 넘겨진 클로저를 통과하는 요소가 있다면 true를 반환하고 그렇지 않으면 false를 반환한다.

`contains` 오퍼레이터와 관련이 있다.

```swift
// Publishers.ContainsWhere Publisher
Publishers
  .ContainsWhere(upstream: Publishers.Sequence<[Int], Never>(sequence: [2, 4, 6])) { $0 == 5 }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine ContainsWhere Error")
    case .finished:
      print("Combine ContainsWhere Finish")
    }
  }, receiveValue: { value in
    print("Combine ContainsWhere : \(value)")
  })
  .store(in: &cancellables)

// contains Operator
Publishers.Sequence<[Int], Never>(sequence: [2, 4, 6])
  .contains { $0 == 5 }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Contains Error")
    case .finished:
      print("Combine Contains Finish")
    }
  }, receiveValue: { value in
    print("Combine Contains : \(value)")
  })
  .store(in: &cancellables)

// Combine ContainsWhere : false
// Combine ContainsWhere Finish
```

클로저로 조건 평가식을 넘겨 해당 조건을 통과하는 요소가 있는지에 대한 불리언 값을 발행한다.

상위 Publisher는 2, 4, 6의 값을 순서대로 내며, 조건인 `$0 == 5`를 통과하는 요소가 하나도 없으므로 결과적으로 false를 내고 종료한다.

## RxSwift

해당 기능을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## ReactiveSwift

해당 기능을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - Contains](http://reactivex.io/documentation/operators/contains.html)