# Publishers.Merge

**제네릭 구조체** | 두 개의 상위 Publisher에 결합 함수를 적용하여 생성되는 Publisher

이니셜라이저는 조합할 두 개의 Publisher를 받는다.

인자에 들어가는 모든 Publisher의 Output 타입과 에러 타입은 같아야 한다.

두 개의 스트림을 하나로 합쳐 하나의 스트림을 다루는 것처럼 하기 위해 사용할 수 있다.

`merge` 오퍼레이터와 관련이 있다.

```swift
let aSubject = PassthroughSubject<Int, Never>()
let bSubject = PassthroughSubject<Int, Never>()

// Publishers.Merge Publisher
Publishers
  .Merge(aSubject, bSubject)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Merge Error")
    case .finished:
      print("Combine Merge Finish")
    }
  }, receiveValue: { value in
    print("Combine Merge : \(value)")
  })
  .store(in: &cancellables)

// merge Operator
aSubject
  .merge(with: bSubject)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Merge Error")
    case .finished:
      print("Combine Merge Finish")
    }
  }, receiveValue: { value in
    print("Combine Merge : \(value)")
  })
  .store(in: &cancellables)

// 1
aSubject.send(1)
// 2
aSubject.send(2)
// 3
bSubject.send(3)
// 4
bSubject.send(4)

// Combine Merge : 1
// Combine Merge : 2
// Combine Merge : 3
// Combine Merge : 4
```

코드는 다음과 같이 동작한다.

1. 1의 코드를 실행하여 `aSubject`에 1의 값을 전달한다. 해당 Publisher는 1의 값을 낸다.
2. 2의 코드를 실행하여 `aSubject`에 2의 값을 전달한다. 해당 Publisher는 2의 값을 낸다.
3. 3의 코드를 실행하여 `bSubject`에 3의 값을 전달한다. 해당 Publisher는 3의 값을 낸다.
4. 4의 코드를 실행하여 `bSubject`에 4의 값을 전달한다. 해당 Publisher는 4의 값을 낸다.

## RxSwift

`merge` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
let aSubject = PublishSubject<Int>()
let bSubject = PublishSubject<Int>()

Observable.merge(aSubject, bSubject)
  .subscribe(onNext: { value in
    print("RxSwift Merge : \(value)")
  }, onError: { _ in
    print("RxSwift Merge Error")
  }, onCompleted: {
    print("RxSwift Merge Finish")
  })
  .disposed(by: disposeBag)

aSubject.onNext(1)
aSubject.onNext(2)
bSubject.onNext(3)
bSubject.onNext(4)

// RxSwift Merge : 1
// RxSwift Merge : 2
// RxSwift Merge : 3
// RxSwift Merge : 4
```

## ReactiveSwift

`merge` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
let aProperty = MutableProperty<Int>(0)
let bProperty = MutableProperty<Int>(0)

aProperty.signal
  .merge(with: bProperty.signal)
  .observe { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift Merge : \(value)")
    case .failed:
      print("ReactiveSwift Merge Error")
    case .completed:
      print("ReactiveSwift Merge Finish")
    default:
      break
    }
  }

aProperty.value = 1
aProperty.value = 2
bProperty.value = 3
bProperty.value = 4

// ReactiveSwift Merge : 1
// ReactiveSwift Merge : 2
// ReactiveSwift Merge : 3
// ReactiveSwift Merge : 4
// ReactiveSwift Merge Finish
```

## 참고

[ReactiveX - Operators - Merge](http://reactivex.io/documentation/operators/merge.html)