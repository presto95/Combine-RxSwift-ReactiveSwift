# Publishers.CombineLatest3

**제네릭 구조체** | 세 개의 Publisher로부터 최신의 요소를 전달받고 조합하는 Publisher

이니셜라이저는 세 개의 인자를 받는다.

- `a` : 조합할 세 개의 Publisher 중 하나
- `b` : 조합할 세 개의 Publisher 중 또다른 하나
- `c` : 조합할 세 개의 Publisher 중 또다른 하나

`a` Publisher와 `b` Publisher와 `c` Publisher의 에러 타입은 같아야 한다.

세 개의 Publisher에서 최신의 요소를 발행했을 때 이들을 조합하는 동작을 원할 때 사용할 수 있다.

다른 두 개의 Publisher를 인자로 받는 `combineLatest(_:_:)` 오퍼레이터는 해당 Publisher를 반환한다. 이 때 조합 결과는 튜플의 형태로 발행된다.

다른 두 개의 Publisher를 인자로 받고 transform 클로저를 명시한 `combineLatest(_:_:_:)` 오퍼레이터는 `Publishers.Map` Publisher를 반환한다.

```swift
let aSubject = PassthroughSubject<Int, Never>()
let bSubject = PassthroughSubject<Int, Never>()
let cSubject = PassthroughSubject<Int, Never>()

// 1 : Publishers.CombineLatest3
Publishers
  .CombineLatest3(aSubject, bSubject, cSubject)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine CombineLatest3 Error")
    case .finished:
      print("Combine CombineLatest3 Finish")
    }
  }, receiveValue: { value in
    print("Combine CombineLatest3 : \(value)")
  })
  .store(in: &cancellables)

// Combine CombineLatest3 : (2, 4, 5)
// Combine CombineLatest3 : (2, 4, 6)

// 2 : combineLatest(_:_:) Operator
aSubject
  .combineLatest(bSubject, cSubject)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine CombineLatest3 Error")
    case .finished:
      print("Combine CombineLatest3 Finish")
    }
  }, receiveValue: { value in
    print("Combine CombineLatest3 : \(value)")
  })
  .store(in: &cancellables)

// Combine CombineLatest3 : (2, 4, 5)
// Combine CombineLatest3 : (2, 4, 6)

// 3 : combineLatest(_:_:_:) Operator
aSubject
  .combineLatest(bSubject, cSubject) { $0 * $1 * $2 }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine CombineLatest3 Error")
    case .finished:
      print("Combine CombineLatest3 Finish")
    }
  }, receiveValue: { value in
    print("Combine CombineLatest3 : \(value)")
  })
  .store(in: &cancellables)

// Combine CombineLatest3 : 40
// Combine CombineLatest3 : 48

aSubject.send(1)
aSubject.send(2)
bSubject.send(3)
bSubject.send(4)
cSubject.send(5)
cSubject.send(6)
```

## RxSwift

Observable 결합 오퍼레이터 `combineLatest`를 사용하여 구현할 수 있다.

```swift
let aSubject = PublishSubject<Int>()
let bSubject = PublishSubject<Int>()
let cSubject = PublishSubject<Int>()

Observable.combineLatest(aSubject, bSubject, cSubject)
  .subscribe(onNext: { value in
    print("RxSwift CombineLatest3 : \(value)")
  }, onError: { _ in
    print("RxSwift CombineLatest3 Error")
  }, onCompleted: {
    print("RxSwift CombineLatest3 Finish")
  })
  .disposed(by: disposeBag)

aSubject.onNext(1)
aSubject.onNext(2)
bSubject.onNext(3)
bSubject.onNext(4)
cSubject.onNext(5)
cSubject.onNext(6)

// RxSwift CombineLatest3 : (2, 4, 5)
// RxSwift CombineLatest3 : (2, 4, 6)
```

## ReactiveSwift

ReactiveSwift는 세 개 이상의 스트림에 대한 `combineLatest` 동작을 구햔하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - CombineLatest](http://reactivex.io/documentation/operators/combinelatest.html)
[Publishers.CombineLatest](./CombineLatest.md)