# Publishers.TryCompactMap

**제네릭 구조체** | 받은 각각의 요소에 대하여 에러를 던지는 클로저를 호출하여 nil이 아닌 결과를 다시 발행하는 Publisher

`Publishers.CompactMap`의 동작에서 에러를 던질 수 있는 기능이 추가된 형태다.

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `transform` : 상위에 흐르는 Publisher로부터 값을 받고 옵셔널 값을 반환하는 에러를 던지는 클로저

시퀀스에서 nil 값을 없애는 등의 작업을 하고 싶으며 작업이 에러를 던질 수 있을 때 사용할 수 있다.

`tryCompactMap` 오퍼레이터는 해당 Publisher를 반환한다.

```swift
// 1 : Publishers.TryCompactMap Publisher
Publishers
  .TryCompactMap(upstream: Publishers.Sequence<[Int?], Never>(sequence: [1, nil, 3, 4])) { element -> Int? in
    if let element = element {
      if element.isMultiple(of: 2) {
        throw error
      } else {
        return element
      }
    }
    return element
  }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine TryCompactMap Error")
    case .finished:
       print("Combine TryCompactMap Finish")
    }
  }, receiveValue: { value in
    print("Combine TryCompactMap : \(value)")
  })
  .store(in: &cancellables)

// 2 : tryCompactMap Operator
Publishers.Sequence<[Int?], Never>(sequence: [1, nil, 3, 4])
  .tryCompactMap { element -> Int? in
    if let element = element {
      if element.isMultiple(of: 2) {
         throw error
      } else {
        return element
      }
    }
    return element
  }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine TryCompactMap Error")
    case .finished:
      print("Combine TryCompactMap Finish")
    }
  }, receiveValue: { value in
    print("Combine TryCompactMap : \(value)")
  })
  .store(in: &cancellables)

// Combine TryCompactMap : 1
// Combine TryCompactMap : 3
// Combine TryCompactMap Error
```

값이 2의 배수이면 에러를 던지는 로직을 작성하였다.

그러므로 1, nil, 3, 4의 값을 순서대로 내는 Publisher는 다음과 같이 동작한다.

1. 1의 값은 2의 배수가 아니므로 다시 발행한다.
2. nil 값은 nil이므로 다시 발행되지 않는다.
3. 3의 값은 2의 배수가 아니므로 다시 발행한다.
4. 4의 값은 2의 배수이므로 에러를 낸다.

## RxSwift

Observable 필터링 오퍼레이터 `compactMap`을 사용하여 구현할 수 있다.

기본적으로 해당 오퍼레이터가 받는 변환 클로저가 에러를 던질 수 있는 클로저로 정의되어 TryCompactMap의 동작을 구현할 수 있다.

ReactiveX의 오퍼레이터 목록에는 없으나, Swift 언어가 옵셔널 타입을 갖는 특성상 언어의 스펙에 맞게 해당 오퍼레이터가 구현되어 있다.

```swift
Observable.from([1, nil, 3, 4])
  .compactMap { element -> Int? in
    if let element = element {
      if element.isMultiple(of: 2) {
        throw error
      } else {
        return element
      }
    }
    return element
  }
  .subscribe(onNext: { value in
    print("RxSwift TryCompactMap : \(value)")
  }, onError: { _ in
    print("RxSwift TryCompactMap Error")
  }, onCompleted: {
    print("RxSwift TryCompactMap Finish")
  })
  .disposed(by: disposeBag)

// RxSwift TryCompactMap : 1
// RxSwift TryCompactMap : 3
// RxSwift TryCompactMap Error
```

## ReactiveSwift

ReactiveSwift는 `filterMap`의 동작을 수행할 때 에러를 던질 수 있는 방법을 제공하지 않는다.