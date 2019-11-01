# Future

**제네릭 클래스** | 결국에는 하나의 값을 낸 후 종료하거나 실패하는 Publisher

이니셜라이저는 한 개의 인자를 받는다.

- `attemptToFulfill` : Publisher가 요소를 배출하거나 에러로 종료할 때 Publisher가 호출하는 `Future.Promise`

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

Future는 하나의 값을 낸 후 종료하거나 실패한다. 

1의 코드가 실행되면 값을 내므로 Publisher는 종료한다. 그렇기 때문에 2의 코드는 효력이 없게 된다.

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

마찬가지로 1의 코드를 실행하면 에러를 내므로 Publisher는 에러를 낸다. 

그렇기 때문에 2의 코드는 효력이 없게 된다.

## RxSwift

Single을 사용하여 구현할 수 있다.

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

해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[Combine's Future](https://www.thomasvisser.me/2019/07/06/combine-future/)

[ReactiveX - Operator - Create](http://reactivex.io/documentation/operators/create.html)

[ReactiveX - Single](http://reactivex.io/documentation/single.html)