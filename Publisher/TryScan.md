# Publishers.TryScan

**제네릭 구조체**

현재 문서화가 되어 있지 않지만 RxSwift의 `scan` 오퍼레이터의 동작에서 에러를 던지는 클로저를 넘겨줄 수 있다는 것이 추가된다.

Publisher가 내는 요소에 연속적으로 함수를 적용하고 실행한 후, 성공적으로 실행된 함수의 반환 값을 낸다. 함수는 에러를 던질 수 있다.

이니셜라이저는 세 개의 인자를 받는다.

- `upstream` : `Publisher` 프로토콜을 따르는 Publisher를 넘겨준다.
- `initialResult` : 초기 값. 
- `nextPartialResult` : 다음 부분 결과. 요소에 연속적으로 적용할 클로저를 넘겨준다. 클로저는 에러를 던질 수 있다.

Swift의 `reduce` 함수와 동작이 비슷하지만 `reduce`가 최종 결과만을 반환한다면 `tryScan`은 함수가 적용된 요소와 다음 요소에 대해 함수를 적용하여 각 요소가 점진적으로 교체된다.

함수가 에러를 던지지 않으면 해당 요소에 대한 변환은 적용되어 값을 내고, 에러를 던지면 에러와 함께 종료한다.

`tryScan` 오퍼레이터는 해당 Publisher를 반환한다.

```swift
// 1 : Publishers.TryScan Publisher
Publishers
  .TryScan(upstream: Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3]), initialResult: 0) { current, next in
    if current == 3 {
      throw error
    } else {
      return current + next
    }
  }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine TryScan Error")
    case .finished:
      print("Combine TryScan Finish")
    }
  }, receiveValue: { value in
    print("Combine TryScan : \(value)")
  })
  .store(in: &cancellables)

// 2 : tryScan Operator
Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3])
  .tryScan(0) { current, next in
    if current == 3 {
      throw error
    } else {
      return current + next
    }
  }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine TryScan Error")
    case .finished:
      print("Combine TryScan Finish")
    }
  }, receiveValue: { value in
    print("Combine TryScan : \(value)")
  })
  .store(in: &cancellables)

// Combine TryScan : 1
// Combine TryScan : 3
// Combine TryScan Error
```

`Scan`을 사용했다면 1, 3, 6의 값을 차례대로 내고 종료할 것이다. 

하지만 값이 3일 때 에러를 내도록 하였으므로 1과 3의 값을 차례대로 낸 후 에러를 내어 종료한다.

## RxSwift

Observable 변환 오퍼레이터 `scan`을 사용하여 구현할 수 있다.

기본적으로 해당 오퍼레이터가 받는 변환 클로저가 에러를 던질 수 있는 클로저로 정의되어 TryScan의 동작을 구현할 수 있다.

```swift
Observable.from([1, 2, 3])
  .scan(0) { current, next in
    if current == 3 {
      throw error
    } else {
      return current + next
    }
  }
  .subscribe(onNext: { value in
    print("RxSwift TryScan : \(value)")
  }, onError: { _ in
    print("RxSwift TryScan Error")
  }, onCompleted: {
    print("RxSwift TryScan Finish")
  })
  .disposed(by: disposeBag)

// RxSwift TryScan : 1
// RxSwift TryScan : 3
// RxSwift TryScan Error
```

## ReactiveSwift

ReactiveSwift는 `scan`의 동작을 수행할 때 에러를 던질 수 있는 방법을 제공하지 않는다.

## 참고

[ReactiveX - Operators - Scan](http://reactivex.io/documentation/operators/scan.html)

[Publishers.Scan](./Scan.md)