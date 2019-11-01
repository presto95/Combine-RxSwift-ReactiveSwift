# Optional.Publisher

**구조체** | 옵셔널이 값을 가지고 있으면, 각 Subscriber에게 정확히 한 번 옵셔널 값을 발행하는 Combine Publisher

이니셜라이저는 한 개의 인자를 받는다.

- `output` : 각 Subscriber에게 전달할 옵셔널 값

- 옵셔널 값이 nil이 아니라면 옵셔널이 벗겨진 값을 전달하고 종료한다.
- 옵셔널 값이 nil이라면 값을 전달하지 않고 대신 정상적으로 종료한다.

```swift
// 1
Optional.Publisher(Void())
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Optional Error")
    case .finished:
      print("Combine Optional Finish")
    }
  }, receiveValue: {
    print("Combine Optional")
  })
  .store(in: &cancellables)

// Combine Optional
// Combine Optional Finish

// 2
Optional.Publisher(nil)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Optional Error")
    case .finished:
      print("Combine Optional Finish")
    }
  }, receiveValue: {
    print("Combine Optional")
  })
  .store(in: &cancellables)

// Combine Optional Finish
```

1의 코드는 인자로 nil이 아닌 들어갔으므로 옵셔널을 벗긴 값을 내고 종료한다.

2의 코드는 인자로 nil이 들어갔으므로 그대로 종료한다.