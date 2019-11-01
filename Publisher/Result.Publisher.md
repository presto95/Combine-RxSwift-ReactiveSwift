# Result.Publisher

**구조체** | 결과*result*를 발행하고 정상적으로 종료하거나, 어떠한 요소도 발행하지 않고 실패하는 Combine Publisher

이니셜라이저는 다음의 형태를 갖는다.

- Result 타입의 값을 넘겨줄 수 있다. ex) `init(.success(value))` / `init(.failure(error))`
- Output 타입의 값을 넘겨줄 수 있다. ex) `init(value)`
- Failure 타입의 값을 넘겨줄 수 있다. ex) `init(error)`

Result가 success 케이스인 경우 Output을 전달하기 이전에 적어도 하나의 값을 위한 요청을 전달받을 때까지 기다린다. failure 케이스인 경우 구독에 대하여 즉시 실패를 전달한다.

`Just` Publisher와 비교하여, 해당 Publisher는 값을 전달하는 대신 에러를 내며 종료할 수 있다.

`Optional.Publisher`와 비교하여, 해당 Publisher는 항상 하나의 값을 전달하며, 그렇지 않다면 에러를 내며 종료한다.

Result 타입의 `publisher` 프로퍼티를 통해 `Result.Publisher` 구조체를 만들 수 있다.

```swift
// 1
Result<Void, Error>.Publisher(.success(Void()))
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Result Error")
    case .finished:
      print("Combine Result Finish")
    }
  }, receiveValue: {
    print("Combine Result")
  })
  .store(in: &cancellables)

// Combine Result
// Combine Result Finish

// 2
Result<Void, Error>.Publisher(.failure(error))
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Result Error")
    case .finished:
      print("Combine Result Finish")
    }
  }, receiveValue: {
    print("Combine Result")
  })
  .store(in: &cancellables)

// Combine Result Error

// 3
Result.success(Void())
  .publisher
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Result Error")
    case .finished:
      print("Combine Result Finish")
    }
  }, receiveValue: {
    print("Combine Result")
  })
  .store(in: &cancellables)

// Combine Result
// Combine Result Finish

// 4
Result.failure(error)
.publisher
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Result Error")
    case .finished:
      print("Combine Result Finish")
    }
  }, receiveValue: {
    print("Combine Result")
  })
  .store(in: &cancellables)

// Combine Result Error
```

1의 코드는 인자로 success 케이스가 들어갔으므로 해당 값을 내고 종료한다.

2의 코드는 인자로 failure 케이스가 들어갔으므로 에러를 낸다.

3의 코드는 Result 타입에서 `publisher` 프로퍼티를 통해 `Result.Publisher`를 만들었고, 1의 코드와 같은 동작을 한다.

4의 코드는 Result 타입에서 `publisher` 프로퍼티를 통해 `Result.Publisher`를 만들었고, 2의 코드와 같은 동작을 한다.