# Publishers.Breakpoint

**제네릭 구조체** | 제공된 클로저가 디버거에서 그 프로세스를 중단하는 것을 필요로 할 때 디버거 시그널을 일으키는 Publisher

이니셜라이저는 네 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `receiveSubscription` : Publisher가 구독을 전달받을 때 실행되며, true 값을 반환하여 디버거 시그널을 일으킬 수 있는 클로저
- `receiveOutput` : Publisher가 상위 Publisher로부터 출력을 전달받을 때 실행되며, true 값읇 반환하여 디버거 시그널을 일으킬 수 있는 클로저
- `receiveCompletion` : Publisher가 완료를 전달받을 때 실행되며, true 값을 반환하여 디버거 시그널을 일으킬 수 있는 클로저

`handleEvents`와 같은 시점에 동작하나 클로저가 true를 반환하게 하면 해당 이벤트 시점에서 디버거 시그널을 일으킬 수 있다.

`breakpoint` 및 `breakpointOnError` 오퍼레이터와 관련이 있다.

```swift
// 1. Publishers.Breakpoint Publisher
Publishers.Breakpoint(upstream: Just(Void()),
                          receiveSubscription: { _ in true },
                          receiveOutput: { _ in true },
                          receiveCompletion: { _ in true })
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Breakpoint Error")
    case .finished:
      print("Combine Breakpoint Finish")
    }
  }, receiveValue: {
    print("Combine Breakpoint")
  })
  .store(in: &cancellables)

// 2. breakpoint Operator
Just(Void())
  .breakpoint(receiveSubscription: { _ in true },
              receiveOutput: { _ in true },
              receiveCompletion: { _ in true })
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Breakpoint Error")
    case .finished:
      print("Combine Breakpoint Finish")
    }
  }, receiveValue: {
    print("Combine Breakpoint")
  })
  .store(in: &cancellables)

// 3. breakpointOnError Operator
Fail<Void, Error>(error: error)
  .breakpointOnError()
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Breakpoint Error")
    case .finished:
      print("Combine Breakpoint Finish")
    }
  }, receiveValue: {
    print("Combine Breakpoint")
  })
  .store(in: &cancellables)
```

1과 2의 코드는 구독, 값 발행, 완료 이벤트가 일어날 때 디버거 시그널을 일으킨다.

3의 코드는 상위 Publisher가 에러를 내므로 `breakpointOnError`에 의해 디버거 시그널을 일으킨다.

## RxSwift

해당 동작을 구현하는 오퍼레이터를 제공하지 않는다.

## ReactiveSwift

해당 동작을 구현하는 오퍼레이터를 제공하지 않는다.