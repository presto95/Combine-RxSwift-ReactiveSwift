# Publishers.ReplaceEmpty

**제네릭 구조체** | 비어 있는 스트림을 제공된 요소로 교체하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `output` : 상위에 흐르는 Publisher가 어떠한 요소도 전달하지 않고 종료할 때 전달할 요소

상위 Publisher가 즉시 종료하는 `Empty`와 같은 것이나 시퀀스가 비어 있는 `Publishers.Sequence` 등인 경우에 해당 Publisher에 명시된 값으로 교체한다.

값을 내지 않고 종료하는 Publisher가 특정 값을 내고 종료할 수 있게 하기 위해 사용할 수 있다.

`replaceEmpty` 오퍼레이터와 관련이 있다.

```swift
// Publishers.ReplaceEmpty Publisher
Publishers.ReplaceEmpty(upstream: Publishers.Sequence<[Int], Never>(sequence: []), output: 0)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine ReplaceEmpty Error")
    case .finished:
      print("Combine ReplaceEmpty Finish")
    }
  }, receiveValue: { value in
    print("Combine ReplaceEmpty : \(value)")
  })
  .store(in: &cancellables)

// replaceEmpty Operator
Publishers.Sequence<[Int], Never>(sequence: [])
  .replaceEmpty(with: 0)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine ReplaceEmpty Error")
    case .finished:
      print("Combine ReplaceEmpty Finish")
    }
  }, receiveValue: { value in
    print("Combine ReplaceEmpty : \(value)")
  })
  .store(in: &cancellables)

// Combine ReplaceEmpty : 0
// Combine ReplaceEmpty Finish
```

위의 코드 모두 `Publishers.Sequence` Publisher에 빈 배열을 넣어 값을 내지 않고 종료하는 Publisher를 만든다.

`replaceEmpty`에 의해 이 Publisher는 주어진 값으로 대체될 수 있다.

## RxSwift

`ifEmpty` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
Observable.from([])
  .ifEmpty(default: 0)
  .subscribe(onNext: { value in
    print("RxSwift ReplaceEmpty : \(value)")
  }, onError: { _ in
    print("RxSwift ReplaceEmpty Error")
  }, onCompleted: {
    print("RxSwift ReplaceEmpty Finish")
  })
  .disposed(by: disposeBag)

// RxSwift ReplaceEmpty : 0
// RxSwift ReplaceEmpty Finish
```

## ReactiveSwift

해당 기능을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - DefaultIfEmpty](http://reactivex.io/documentation/ko/operators/defaultifempty.html)