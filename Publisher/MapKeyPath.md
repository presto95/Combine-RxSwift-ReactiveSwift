# Publishers.MapKeyPath

**제네릭 구조체** | 키 경로의 값을 발행하는 Publisher

이니셜라이저를 제공하지 않는다.

타입의 키 경로를 통해 접근한 값으로 매핑하기 위해 사용할 수 있다.

`map` 오퍼레이터와 관련이 있다.

```swift
struct SomeStruct {
  let property = "value"
}

Just(SomeStruct())
  .map(\.property)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine MapKeyPath Error")
    case .finished:
      print("Combine MapKeyPath Finish")
    }
  }, receiveValue: { value in
    print("Combine MapKeyPath : \(value)")
  })
  .store(in: &cancellables)

// Combine MapKeyPath : value
// Combine MapKeyPath Finish
```

`SomeStruct` 구조체의 인스턴스를 발행하는 상위 Publisher로부터 `property` 키 경로를 통해 해당 값에 접근하고 그 값으로 매핑한다.

## RxSwift

해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## ReactiveSwift

해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.