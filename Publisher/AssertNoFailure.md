# Publishers.AssertNoFailure

**제네릭 구조체** | 어떠한 실패를 전달받으면 치명적인 에러를 발생시키고, 그렇지 않으면 전달받은 모든 입력을 다시 발행하는 Publisher

이니셜라이저는 네 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `prefix` : 에러 메세지의 시작에 이어 붙일 문자열
- `file` : 에러 메세지에 사용될 파일 이름
- `line` : 에러 메세지에 사용될 줄 번호

말 그대로 실패가 없음을 단언하기 위해 사용한다.

테스트 중에는 활성화되나 실제 코드의 성능에는 영향을 미치지 않는 내부 검사를 하기 위해 사용할 수 있다.

`assertNoFailure` 오퍼레이터와 관련이 있다.

```swift
// Publishers.AssertNoFailure Publisher
Publishers.AssertNoFailure(upstream: Fail<Void, Error>(error: error), prefix: "prefix", file: #file, line: #line)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine AssertNoFailure Error")
    case .finished:
      print("Combine AssertNoFailure Finish")
    }
  }, receiveValue: { value in
    print("Combine AssertNoFailure : \(value)")
  })
  .store(in: &cancellables)

// assertNoFailure Operator
Fail<Void, Error>(error: error)
  .assertNoFailure()
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine AssertNoFailure Error")
    case .finished:
      print("Combine AssertNoFailure Finish")
    }
  }, receiveValue: { value in
    print("Combine AssertNoFailure : \(value)")
  })
  .store(in: &cancellables)

// 출력 값이 없고, 런타임 에러 발생
```

상위 Publisher가 `Fail` Publisher이기 때문에 에러를 내고, `assertNoFailure`에 의해 런타임 에러가 발생한다.

## RxSwift

해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## ReactiveSwift

해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.