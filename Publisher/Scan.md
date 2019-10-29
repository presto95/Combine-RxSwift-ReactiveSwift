# Publishers.Scan

**제네릭 구조체**

현재 문서화가 되어 있지 않지만 RxSwift의 `scan` 오퍼레이터와 동작이 일치한다.

Publisher가 내는 요소에 연속적으로 함수를 적용하고 실행한 후, 성공적으로 실행된 함수의 반환 값을 낸다.

이니셜라이저는 세 개의 인자를 받는다.

- `upstream` : `Publisher` 프로토콜을 따르는 Publisher를 넘겨준다.
- `initialResult` : 초기 값. 
- `nextPartialResult` : 다음 부분 결과. 요소에 연속적으로 적용할 클로저를 넘겨준다.

Swift의 `reduce` 함수와 동작이 비슷하지만 `reduce`가 최종 결과만을 반환한다면 `scan`은 함수가 적용된 요소와 다음 요소에 대해 함수를 적용하여 각 요소가 점진적으로 다시 발행된다.

`scan` 오퍼레이터는 해당 Publisher를 반환한다.

```swift
// 1 : Publishers.Scan Publisher
Publishers.Scan(upstream: Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3]), initialResult: 0) { $0 + $1 }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Scan Error")
    case .finished:
      print("Combine Scan Finish")
    }
  }, receiveValue: { value in
    print("Combine Scan : \(value)")
  })
  .store(in: &cancellables)

// 2 : scan Operator
Publishers.Sequence<[Int], Never>(sequence: [1, 2, 3])
  .scan(0) { $0 + $1 }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Scan Error")
    case .finished:
      print("Combine Scan Finish")
    }
  }, receiveValue: { value in
    print("Combine Scan : \(value)")
  })
  .store(in: &cancellables)

// Combine Scan : 1
// Combine Scan : 3
// Combine Scan : 6
// Combine Scan Finish
```

`Publishers.Sequence` Publisher를 사용하여 1, 2, 3의 값을 차례대로 내도록 하였고, `scan`을 적용하여 점진적으로 이전의 값과 현재의 값을 더한 값을 반환하도록 하였다.

1. 1이 초기값 0과 더해져 1의 값을 낸다.
2. 2가 이전의 값 1과 더해져 3의 값을 낸다.
3. 3이 이전의 값 3과 더해져 6의 값을 낸다.

결과적으로 1, 3, 6의 값을 차례대로 내고 종료한다.

## RxSwift

Observable 변환 오퍼레이터 `scan`을 사용하여 구현할 수 있다.

```swift
Observable.from([1, 2, 3])
  .scan(0) { $0 + $1 }
  .subscribe(onNext: { value in
    print("RxSwift Scan : \(value)")
  }, onError: { _ in
    print("RxSwift Scan Error")
  }, onCompleted: {
    print("RxSwift Scan Finish")
  })
  .disposed(by: disposeBag)

// RxSwift Scan : 1
// RxSwift Scan : 3
// RxSwift Scan : 6
// RxSwift Scan Finish
```

## ReactiveSwift

`scan` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
SignalProducer([1, 2, 3])
  .scan(0) { $0 + $1 }
  .start { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift Scan : \(value)")
    case .failed:
      print("ReactiveSwift Scan Error")
    case .completed:
      print("ReactiveSwift Scan Finish")
    default:
      break
    }
  }

// ReactiveSwift Scan : 1
// ReactiveSwift Scan : 3
// ReactiveSwift Scan : 6
// ReactiveSwift Scan Finish
```

## 참고

[ReactiveX - Operators - Scan](http://reactivex.io/documentation/operators/scan.html)