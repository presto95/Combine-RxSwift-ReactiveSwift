# Publishers.TryMap

**제네릭 구조체** | 제공된 에러를 던지는 클로저를 사용하여 상위에 흐르는 Publisher로부터 모든 요소를 변환하는 Publisher

제공하는 클로저에서 에러를 던지는 작업을 수행할 수 있다. 작업이 성공하면 정상적으로 나머지 체인을 수행하고, 작업이 실패하면 에러를 낸다.

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : `Publisher` 프로토콜을 따르는 Publisher를 넘겨준다.
- `transform` : 상위에 흐르는 Publisher로부터 요소들을 변환하기 위한 에러를 던지는 클로저를 넘겨준다.
  - 클로저 문법에서 반환형을 명시해줄 필요가 있다. 그렇지 않으면 Void를 반환하는 것으로 취급된다.

에러를 던지는 작업을 수행하기 위해 사용한다.

`tryMap` 오퍼레이터는 해당 Publisher를 반환한다.

```swift
// 1 : Publishers.TryMap Publisher
Publishers
  .TryMap(upstream: Just(Data())) { data -> ResponseModel in
    try JSONDecoder().decode(ResponseModel.self, from: data)  
  }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine TryMap Error")
    case .finished:
      print("Combine TryMap Finish")
    }
  }, receiveValue: { _ in
    print("Combine TryMap")
  })
  .store(in: &cancellables)

// 2 : tryMap Operator
Just(Data())
  .tryMap { data -> ResponseModel in
    try JSONDecoder().decode(ResponseModel.self, from: data)
  }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine TryMap Error")
    case .finished:
      print("Combine TryMap Finish")
    }
  }, receiveValue: { _ in
    print("Combine TryMap")
  })
  .store(in: &cancellables)

// Combine TryMap Error
```

`Data` 타입의 값을 `JSONDecocer`를 사용하여 특정 타입으로 JSON Decoding을 하는 작업을 클로저에 정의했다. 이 작업은 실패하여 에러를 내게 되고, 에러를 냈을 때 수행할 동작이 실행된다.

## RxSwift

Observable 변환 오퍼레이터 `map`을 사용하여 구현할 수 있다. 

이 오퍼레이터는 기본적으로 에러를 던지는 클로저를 인자로 받으므로 동일한 동작을 구현할 수 있다.

```swift
Observable.just(Data())
  .map { data -> ResponseModel in
    try JSONDecoder().decode(ResponseModel.self, from: data)
  }
  .subscribe(onNext: { _ in
    print("RxSwift TryMap")
  }, onError: { _ in
    print("RxSwift TryMap Error")
  }, onCompleted: {
    print("RxSwift TryMap Finish")
  })
  .disposed(by: disposeBag)

// RxSwift TryMap Error
```

## ReactiveSwift

`attemptMap` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
SignalProducer(value: Data())
  .attemptMap { data -> ResponseModel in
    try JSONDecoder().decode(ResponseModel.self, from: data)
  }
  .start { event in
    switch event {
    case .value:
      print("ReactiveSwift AnyPublisher")
    case .failed:
      print("ReactiveSwift AnyPublisher Error")
    case .completed:
      print("ReactiveSwift AnyPublisher Finish")
    default:
      break
    }
  }

// RxSwift TryMap Error
```

## 참고

[ReactiveX - Operators - Map](http://reactivex.io/documentation/operators/map.html)

[Publishers.Map](./Map.md)