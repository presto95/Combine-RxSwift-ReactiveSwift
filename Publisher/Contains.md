# Publishers.Contains

**제네릭 구조체** | 상위에 흐르는 Publisher로부터 특정 요소를 전달받을 때 불리언 값을 배출하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `output` : 상위 Publisher로부터 스캔할 요소

상위 Publisher의 Output 타입은 `Equatable` 프로토콜을 채택해야 한다.

`output`에 넘겨진 요소가 상위 Publisher에 존재하면 true를 반환하고 그렇지 않으면 false를 반환한다.

`contains` 오퍼레이터와 관련이 있다.

```swift
// Publishers.Contains Publisher
Publishers
  .Contains(upstream: Publishers.Sequence<[Int], Never>(sequence: [2, 4, 6]), output: 5)
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

// contains Operator
Publishers.Sequence<[Int], Never>(sequence: [2, 4, 6])
  .contains(5)
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

// Combine Contains : false
// Combine Contains Finish
```

`output` 인자에 특정 값을 넘겨 상위 Publisher가 발행하는 값과 일치하는 것이 있는지 확인하여 불리언 값을 반환한다.

상위 Publisher는 2, 4, 6의 값을 차례대로 내고, 5의 값이 포함되어 있는지를 조사하므로 결과적으로 false를 반환하고 종료한다.

## RxSwift

해당 기능을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## ReactiveSwift

해당 기능을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - Contains](http://reactivex.io/documentation/operators/contains.html)