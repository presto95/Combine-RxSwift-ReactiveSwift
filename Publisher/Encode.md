# Publishers.Encode

**제네릭 구조체**

이니셜라이저는 두 개의 인자를 받는다.

- `upstream `: 상위에 흐르는 Publisher
- `encoder` : 사용할 인코더

`encoder`는 Combine의 `TopLevelEncoder` 프로토콜을 채택해야 하는데, 현재 `JSONEncoder`와 `PropertyListEncoder`가 이 프로토콜을 채택하고 있다.

상위 스트림의 요소를 인코딩하기 위해 사용할 수 있다.

`encode` 오퍼레이터와 관련이 있다.

```swift
struct SimpleJSON: Encodable {
  let key: String
}
let simpleJSON = SimpleJSON(key: "value")

// Publishers.Encode Publisher
Publishers.Encode(upstream: Just(simpleJSON), encoder: JSONEncoder())
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Encode Error")
    case .finished:
      print("Combine Encode Finish")
    }
  }, receiveValue: { value in
    print("Combine Encode : \(value)")
  })
  .store(in: &cancellables)

// encode Operator
Just(simpleJSON)
  .encode(encoder: JSONEncoder())
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Encode Error")
    case .finished:
      print("Combine Encode Finish")
    }
  }, receiveValue: { value in
    print("Combine Encode : \(value)")
  })
  .store(in: &cancellables)

// Combine Encode : 15 bytes
// Combine Encode Finish  
```

상위 Publisher는 `Encodable` 프로토콜을 따르는 구조체의 인스턴스를 발행하며, `JSONEncoder`를 사용하여 인코딩한다.

## RxSwift

해당 기능을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## ReactiveSwift

해당 기능을 구현하기 위한 오퍼레이터를 제공하지 않는다.