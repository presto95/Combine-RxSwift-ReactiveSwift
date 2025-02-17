# Publishers.Collect

**제네릭 구조체** | 아이템을 일시적으로 기억하는 Publisher

이니셜라이저는 한 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher

값을 받을 때마다 동작하지 않고, 에러 또는 종료 이벤트가 날 때까지 값을 기억해 두었다가 배열의 형태로 값을 한꺼번에 낸다.

에러를 내면 그 시점까지 모아 놓은 값들 대신 에러를 낸다.

종료를 내면 그 시점까지 모아 놓은 값들을 배열의 형태로 한꺼번에 낸다.

`collect` 오퍼레이터와 관련이 있다.

```swift
let subject = PassthroughSubject<Int, Error>()

// Publishers.Collect Publisher
 Publishers.Collect(upstream: subject)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Collect Error")
    case .finished:
      print("Combine Collect Finish")
    }
  }, receiveValue: { value in
    print("Combine Collect : \(value)")
  })
  .store(in: &cancellables)

// collect Operator
subject
  .collect()
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Collect Error")
    case .finished:
      print("Combine Collect Finish")
    }
  }, receiveValue: { value in
    print("Combine Collect : \(value)")
  })
  .store(in: &cancellables)

// 1
subject.send(1)
subject.send(2)
subject.send(completion: .finished)

// Combine Collect : [1, 2]
// Combine Collect Finish

// 2
subject.send(1)
subject.send(completion: .failure(error))

// Combine Collect Error
```

1의 경우 완료 이벤트를 내기까지 전달된 1, 2의 값이 배열의 형태로 묶여 한번에 발행된다. 

2의 경우 에러로 인해 종료했기 때문에 결과적으로 에러를 낸다.

## RxSwift

`toArray` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
let subject = PublishSubject<Int>()

subject
  .toArray()
  .subscribe(onSuccess: { value in
    print("RxSwift Collect : \(value)")
  }, onError: { _ in
    print("RxSwift Collect Error")
  })
  .disposed(by: disposeBag)

subject.onNext(1)
subject.onNext(2)
subject.onCompleted()

// RxSwift Collect : [1, 2]
```

## ReactiveSwift

`collect` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
let property = MutableProperty<Int>(0)

property.signal
  .collect()
  .observe { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift Collect : \(value)")
    case .failed:
      print("ReactiveSwift Collect Error")
    case .completed:
      print("ReactiveSwift Collect Finish")
    default:
      break
    }
  }

property.value = 1
property.value = 2

// ReactiveSwift Collect : [1, 2]
// ReactiveSwift Collect Finish
```

## 참고

[ReactiveX - Operators - To](http://reactivex.io/documentation/operators/to.html)

