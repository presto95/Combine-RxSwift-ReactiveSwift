# Publishers.Decode

**제네릭 구조체**

이니셜라이저는 두 개의 인자를 받는다.

- `upstream `: 상위에 흐르는 Publisher
- `decoder` : 사용할 디코더

`decoder`는 Combine의 `TopLevelDecoder` 프로토콜을 채택해야 하는데, 현재 `JSONDecoder`와 `PropertyListDecoder`가 이 프로토콜을 채택하고 있다.

상위 스트림의 요소를 디코딩하기 위해 사용할 수 있다.

`decode` 오퍼레이터와 관련이 있다.

```swift
struct SimpleJSON: Decodable {
  let key: String
}
let jsonData = "{ \"key\": \"value\" }".data(using: .utf8)!

// Publishers.Decode Publisher
Publishers.Decode<Just<JSONDecoder.Input>, SimpleJSON, JSONDecoder>(upstream: Just(jsonData), decoder: JSONDecoder())
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Decode Error")
    case .finished:
      print("Combine Decode Finish")
    }
  }, receiveValue: { value in
    print("Combine Decode : \(value)")
  })
  .store(in: &cancellables)

// encode Operator
Just(jsonData)
  .decode(type: SimpleJSON.self, decoder: JSONDecoder())
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Decode Error")
    case .finished:
      print("Combine Decode Finish")
    }
  }, receiveValue: { value in
    print("Combine Decode : \(value)")
  })
  .store(in: &cancellables)

// Combine Decode : SimpleJSON(key: "value")
// Combine Decode Finish
```

상위 Publisher는 JSON 형식의 데이터를 갖는 `Data`를 발행하며, `JSONDecoder`를 사용하여 디코딩한다.

## RxSwift

해당 기능을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## ReactiveSwift

해당 기능을 구현하기 위한 오퍼레이터를 제공하지 않는다.