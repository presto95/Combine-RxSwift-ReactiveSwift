# Publishers.FirstWhere

**제네릭 구조체** | 특정 조건 클로저를 만족하기 위한 스트림의 첫 번째 요소만을 발행하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `predicate` : 어떤 요소를 발행할지 결정하는 클로저

상위 Publisher로부터 조건 클로저에 만족하는 요소를 먼저 뽑아내고, 그 중에서 첫 번째 요소만을 발행한다.

`first(where:)` 오퍼레이터는 다음의 Publisher를 반환한다.

- 상위 Publisher의 에러 타입이 Never인 경우 `Optional.Publisher` Publisher를 반환한다.
- 해당 Publisher를 반환한다.

```swift
// 1 : Publishers.FirstWhere Publisher
Publishers
  .FirstWhere(upstream: Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3, 4, 5])) { $0.isMultiple(of: 2) }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine FirstWhere Error")
    case .finished:
      print("Combine FirstWhere Finish")
    }
  }, receiveValue: { value in
    print("Combine FirstWhere : \(value)")
  })
  .store(in: &cancellables)

// 2 : first(where:) Operator
Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3, 4, 5])
  .first { $0.isMultiple(of: 2) }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine FirstWhere Error")
    case .finished:
      print("Combine FirstWhere Finish")
    }
  }, receiveValue: { value in
    print("Combine FirstWhere : \(value)")
  })
  .store(in: &cancellables)

// Combine FirstWhere : 2
// Combine FirstWhere Finish
```

상위 Publisher는 1, 2, 3, 4, 5의 값을 차례대로 내고, 2의 배수의 조건을 설정했다.

값들 중 2의 배수인 2, 4 중 첫 번째 요소는 2이므로, 최종적으로 2의 값을 내고 종료한다.

## RxSwift

RxSwift는 해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## ReactiveSwift

ReactiveSwift는 해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.