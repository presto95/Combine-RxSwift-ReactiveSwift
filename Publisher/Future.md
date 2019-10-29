# Future

**제네릭 클래스** | 결국에는 하나의 값을 낸 후 종료하거나 실패하는 Publisher

Future는 `Future.Promise`라는 타입 별칭으로 클로저를 제공한다. 이 클로저는 Result 타입의 인자를 하나 받고 반환값이 없는 형태다.

이 클로저를 사용하여 Future의 이니셜라이저로 넘어가는 클로저에서 어떠한 작업을 수행하고 성공했을 때의 값이나 실패했을 때의 에러를 `Future.Promise` 클로저로 전달한다.

결국 Future는 어떠한 작업을 수행한 후 하나의 값을 낸 후 종료하거나 실패한다.

```swift
Future<Void, Error> { promise in
  // 1
  promise(.success(Void()))
  // 2
  promise(.failure(error))
}
.sink(receiveCompletion: { completion in
  switch completion {
  case .failure:
    print("Combine Future Error")
  case .finished:
    print("Combine Future Finish")
  }
}, receiveValue: {
  print("Combine Future")
})
.store(in: &cancellables)

// Combine Future
// Combine Future Finish
```

Future는 하나의 값을 낸 후 종료하거나 실패한다. 1의 코드가 실행되면 값을 내므로 Publisher는 종료한다. 그렇기 때문에 2의 코드는 효력이 없게 된다.

```swift
Future<Void, Error> { promise in
  // 1
  promise(.failure(error))
  // 2
  promise(.success(Void()))
}
.sink(receiveCompletion: { completion in
  switch completion {
  case .failure:
    print("Combine Future Error")
  case .finished:
    print("Combine Future Finish")
  }
}, receiveValue: {
  print("Combine Future")
})
.store(in: &cancellables)

// Combine Future Error
```

마찬가지로 1의 코드를 실행하면 에러를 내므로 Publisher는 에러를 낸다. 그렇기 때문에 2의 코드는 효력이 없게 된다.

## RxSwift

Observable 생성 오퍼레이터 `create`를 사용하면 다음과 같은 코드를 작성할 수 있다.

```swift
Observable<Void>.create { observer in
  observer.onNext(Void())
  observer.onCompleted()
  // 에러를 내는 경우 위의 코드 대신 다음과 같이 작성한다.
  // observer.onError(error)
  return Disposables.create()
}
.subscribe(onNext: { print("RxSwift Future") })
.disposed(by: disposeBag)

// RxSwift Future
```

위의 코드에서 `observer.onCompleted()`를 작성하지 않으면 해당 Observable은 값을 낸 후 종료하지 않는다.

그러므로 Combine의 Future의 동작을 구현하려면 `.onNext`를 호출한 후 반드시 `.onCompleted`를 호출해야 한다. `.onError`를 호출하면 에러로 인해 종료하므로 `.onCompleted`를 호출할 필요가 없다.

또한 RxSwift의 Single이라는 Trait을 사용하여 구현할 수 있다. Single은 하나의 아이템을 내며 종료하거나, 실패하는 동작을 나타내므로 이를 사용하여 Combine의 Future를 구현할 수 있다.

```swift
Single<Void>.create { observer in
  observer(.success(Void()))
  // 에러를 내는 경우 위의 코드 대신 다음과 같이 작성한다.
  // observer(.failure(error))
  return Disposables.create()
}
.subscribe(onSuccess: { print("RxSwift Future") })
.disposed(by: disposeBag)

// RxSwift Future
```

## ReactiveSwift

`SignalProducer`의 이니셜라이저 중 액션을 클로저로 받는 `init(_:)` 이니셜라이저를 사용하여 구현할 수 있다.

RxSwift의 Single과 같은 것을 제공하지 않으므로 값 이벤트를 낸 후 반드시 종료 이벤트를 내야 Combine의 Future의 동작을 구현할 수 있다. 

```swift
SignalProducer<Void, Never> { observer, lifetime in
  observer.send(value: Void())
  observer.sendCompleted()
  // 에러를 내는 경우 위의 코드 대신 다음과 같이 작성한다.
  // observer.send(error: error)
}
.startWithValues { print("ReactiveSwift Future") }

// ReactiveSwift Future
```

## 참고

[Combine's Future](https://www.thomasvisser.me/2019/07/06/combine-future/)

[ReactiveX - Operator - Create](http://reactivex.io/documentation/operators/create.html)

[ReactiveX - Single](http://reactivex.io/documentation/single.html)