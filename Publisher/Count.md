# Publishers.Count

**제네릭 구조체** | 상위에 흐르는 Publisher로부터 전달받은 요소들의 개수를 발행하는 Publisher

이니셜라이저는 한 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher

상위 Publisher가 발행하는 요소의 개수를 배출한다.

`count` 오퍼레이터와 관련이 있다.

```swift
// Publishers.Count Publisher
Publishers
  .Count(upstream: Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3]))
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Count Error")
    case .finished:
      print("Combine Count Finish")
    }
  }, receiveValue: { value in
    print("Combine Count : \(value)")
  })
  .store(in: &cancellables)

// count Operator
Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3])
  .count()
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Count Error")
    case .finished:
      print("Combine Count Finish")
    }
  }, receiveValue: { value in
    print("Combine Count : \(value)")
  })
  .store(in: &cancellables)

// Combine Count : 3
// Combine Count Finish
```

상위 Publisher에 흐르는 값의 개수가 3이므로 최종적으로 3의 값을 내고 종료한다.

## RxSwift

해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## ReactiveSwift

해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - Count](http://reactivex.io/documentation/operators/count.html)