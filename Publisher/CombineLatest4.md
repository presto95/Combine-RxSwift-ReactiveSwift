# Publishers.CombineLatest4

**제네릭 구조체** | 네 개의 Publisher로부터 최신의 요소를 전달받고 조합하는 Publisher

이니셜라이저는 내 개의 인자를 받는다.

- `a` : 조합할 네 개의 Publisher 중 하나
- `b` : 조합할 네 개의 Publisher 중 또다른 하나
- `c` : 조합할 네 개의 Publisher 중 또다른 하나
- `d` : 조합할 네 개의 Publisher 중 또다른 하나

`a` Publisher와 `b` Publisher와 `c` Publisher와 `d` Publisher의 에러 타입은 같아야 한다.

네 개의 Publisher에서 최신의 요소를 발행했을 때 이들을 조합하는 동작을 원할 때 사용할 수 있다.

다른 세 개의 Publisher를 인자로 받는 `combineLatest(_:_:_:)` 오퍼레이터는 해당 Publisher를 반환한다. 이 때 조합 결과는 튜플의 형태로 발행된다.

다른 세 개의 Publisher를 인자로 받고 transform 클로저를 명시한 `combineLatest(_:_:_:_:)` 오퍼레이터는 `Publishers.Map` Publisher를 반환한다.

```swift
let aSubject = PassthroughSubject<Int, Never>()
let bSubject = PassthroughSubject<Int, Never>()
let cSubject = PassthroughSubject<Int, Never>()
let dSubject = PassthroughSubject<Int, Never>()

// 1 : Publishers.CombineLatest4 Publisher
Publishers
  .CombineLatest4(aSubject, bSubject, cSubject, dSubject)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine CombineLatest4 Error")
    case .finished:
      print("Combine CombineLatest4 Finish")
    }
  }, receiveValue: { value in
    print("Combine CombineLatest4 : \(value)")
  })
  .store(in: &cancellables)

// Combine CombineLatest4 : (2, 4, 6, 7)
// Combine CombineLatest4 : (2, 4, 6, 8)

// 2 : combineLatest(_:_:_:) Operator
aSubject
  .combineLatest(bSubject, cSubject, dSubject)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine CombineLatest4 Error")
    case .finished:
      print("Combine CombineLatest4 Finish")
    }
  }, receiveValue: { value in
    print("Combine CombineLatest4 : \(value)")
  })
  .store(in: &cancellables)

// Combine CombineLatest4 : (2, 4, 6, 7)
// Combine CombineLatest4 : (2, 4, 6, 8)  

// 3 : combineLatest(_:_:_:_:) Operator
aSubject
  .combineLatest(bSubject, cSubject, dSubject) { $0 * $1 * $2 * $3 }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine CombineLatest4 Error")
    case .finished:
      print("Combine CombineLatest4 Finish")
    }
  }, receiveValue: { value in
    print("Combine CombineLatest4 : \(value)")
  })
  .store(in: &cancellables)

// Combine CombineLatest4 : 336
// Combine CombineLatest4 : 384

aSubject.send(1)
aSubject.send(2)
bSubject.send(3)
bSubject.send(4)
cSubject.send(5)
cSubject.send(6)
dSubject.send(7)
dSubject.send(8)
```

## RxSwift

Observable 결합 오퍼레이터 `combineLatest`를 사용하여 구현할 수 있다.

```swift
let aSubject = PublishSubject<Int>()
let bSubject = PublishSubject<Int>()
let cSubject = PublishSubject<Int>()
let dSubject = PublishSubject<Int>()

Observable.combineLatest(aSubject, bSubject, cSubject, dSubject)
  .subscribe(onNext: { value in
    print("RxSwift CombineLatest4 : \(value)")
  }, onError: { _ in
    print("RxSwift CombineLatest4 Error")
  }, onCompleted: {
    print("RxSwift CombineLatest4 Finish")
  })
  .disposed(by: disposeBag)

aSubject.onNext(1)
aSubject.onNext(2)
bSubject.onNext(3)
bSubject.onNext(4)
cSubject.onNext(5)
cSubject.onNext(6)
dSubject.onNext(7)
dSubject.onNext(8)

// RxSwift CombineLatest4 : (2, 4, 6, 7)
// RxSwift CombineLatest4 : (2, 4, 6, 8)
```

## ReactiveSwift

ReactiveSwift는 세 개 이상의 스트림에 대한 `combineLatest` 동작을 구햔하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - CombineLatest](http://reactivex.io/documentation/operators/combinelatest.html)
[Publishers.CombineLatest](./CombineLatest.md)