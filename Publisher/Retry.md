# Publishers.Retry

**제네릭 구조체** | 실패한 상위 Publisher에 대하여 구독을 다시 생성하는 것을 시도하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `retries` : 최대 재시도 횟수. nil이 들어가면 상위 Publisher가 에러를 내지 않을 때까지 재시도한다.

예를 들어 특정 네트워크 요청이 성공할 때까지 다시 요청하려 한다면 이 동작을 사용할 수 있다.

`retry` 오퍼레이터와 관련이 있다.

```swift
// Publishers.Retry Publisher
Publishers.Retry(upstream: Fail<Void, Error>(error: error), retries: 2)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Retry Error")
    case .finished:
      print("Combine Retry Finish")
    }
  }, receiveValue: { value in
    print("Combine Retry : \(value)")
  })
  .store(in: &cancellables)

// retry Operator
Fail<Void, Error>(error: error)
  .retry(2)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Retry Error")
    case .finished:
      print("Combine Retry Finish")
    }
  }, receiveValue: { value in
    print("Combine Retry : \(value)")
  })
  .store(in: &cancellables)

// Combine Retry Error
```

위의 코드는 상위 Publisher로 `Fail` Publisher를 명시하였다.

그러므로 첫 구독에서 에러가 내려오게 되고, 이후 두 번 재시도한다.

두 번의 재시도에도 불구하고 에러가 내려오므로 결과적으로 에러를 낸다.

## RxSwift

`retry` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
Observable<Void>.error(error)
  .retry(2)
  .subscribe(onNext: { value in
    print("RxSwift Retry : \(value)")
  }, onError: { _ in
    print("RxSwift Retry Error")
  }, onCompleted: {
    print("RxSwift Retry Finish")
  })
  .disposed(by: disposeBag)

// RxSwift Retry Error
```

## ReactiveSwift

`retry` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
SignalProducer<Void, Error>(error: error)
  .retry(upTo: 2)
  .start { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift Retry : \(value)")
    case .failed:
      print("ReactiveSwift Retry Error")
    case .completed:
      print("ReactiveSwift Retry Finish")
    default:
      break
    }
  }

// ReactiveSwift Retry Error
```

## 참고

[ReactiveX - Operators - Retry](http://reactivex.io/documentation/operators/retry.html)