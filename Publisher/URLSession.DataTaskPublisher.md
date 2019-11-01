# URLSession.DataTaskPublisher

**구조체** | URL 세션 데이터 작업의 수행 결과를 전달하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `request` : URLRequest 객체
- `session` : URLSession 객체

기존 `URLSession.dataTask` 작업을 Publisher를 사용하여 처리할 수 있도록 해준다.

URLSession의 `dataTaskPublisher` 메소드로 해당 Publisher를 만들 수 있다. 해당 메소드는 URL 또는 URLRequest 객체를 인자로 받는다.

```swift
// URLSession.DataTaskPublisher Publisher
let session = URLSession.shared
let request = URLRequest(url: url)
URLSession.DataTaskPublisher(request: request, session: session)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine DataTaskPublisher Error")
    case .finished:
      print("Combine DataTaskPublisher Finish")
    }
  }, receiveValue: { value in
    print("Combine DataTaskPublisher : \(value)")
  })
  .store(in: &cancellables)

// dataTaskPublisher Property
URLSession.shared.dataTaskPublisher(for: url)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine DataTaskPublisher Error")
    case .finished:
      print("Combine DataTaskPublisher Finish")
    }
  }, receiveValue: { value in
    print("Combine DataTaskPublisher : \(value)")
  })
  .store(in: &cancellables)
```

