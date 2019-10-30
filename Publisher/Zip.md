# Publishers.Zip

**제네릭 구조체** | 두 개의 상위 Publisher에 zip 함수를 적용하여 생성되는 Publisher

두 개의 스트림의 배출 항목이 쌍을 이룰 때 그 값들에 대하여 함수를 적용한 값을 발행한다.

이니셜라이저는 두 개의 인자를 받는다.

- `a` : 조합할 Publisher 중 하나
- `b` : 조합할 Publisher 중 또다른 하나

`a` Publisher와 `b` Publisher의 에러 타입을 같아야 한다.

다른 하나의 Publisher를 인자로 받는 `zip(_:)` 오퍼레이터는 해당 Publisher를 반환한다. 이 때 조합 결과는 튜플의 형태로 발행된다.

다른 하나의 Publisher를 인자로 받고 transform 클로저를 명시한 `zip(_:_:)` 오퍼레이터는 `Publishers.Map` Publisher를 반환한다.

```swift
let aSubject = PassthroughSubject<Int, Never>()
let bSubject = PassthroughSubject<String, Never>()

// 1 : Publishers.Zip Publisher
Publishers
  .Zip(aSubject, bSubject)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Zip Error")
    case .finished:
      print("Combine Zip Finish")
    }
  }, receiveValue: { value in
    print("Combine Zip : \(value)")
  })
  .store(in: &cancellables)

// Combine Zip : (1, "a")
// Combine Zip : (2, "b")

// 2 : zip(_:) Operator
aSubject
  .zip(bSubject)
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Zip Error")
    case .finished:
      print("Combine Zip Finish")
    }
  }, receiveValue: { value in
    print("Combine Zip : \(value)")
  })
  .store(in: &cancellables)

// Combine Zip : (1, "a")
// Combine Zip : (2, "b")

// 3 : zip(_:_:) Operator
aSubject
  .zip(bSubject) { "\($0)\($1)" }
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Zip Error")
    case .finished:
      print("Combine Zip Finish")
    }
  }, receiveValue: { value in
    print("Combine Zip : \(value)")
  })
  .store(in: &cancellables)

// Combine Zip : 1a
// Combine Zip : 2b

// 4
aSubject.send(1)
// 5
aSubject.send(2)
// 6
bSubject.send("a")
// 7
bSubject.send("b")
// 8
aSubject.send(3)
```

Int 타입 값을 받는 `aSubject`와 String 타입 값을 받는 `bSubject`를 zip 오퍼레이터를 사용하여 결합하였다.

1과 2의 코드는 다음과 같이 동작한다.

1. 4의 실행에 의해 `aSubject`에 1의 값을 전달한다. `bSubject`에는 해당 값과 쌍을 이룰 수 있는 값이 없으므로 해당 Publisher는 값을 내지 않는다.
2. 5의 실행에 의해 `aSubject`에 2의 값을 전달한다. `bSubject`에는 해당 값과 쌍을 이룰 수 있는 값이 없으므로 해당 Publisher는 값을 내지 않는다.
3. 6의 실행에 의해 `bSubject`에 a의 값을 전달한다. `aSubject`에는 1과 2의 값이 전달되었고, 1의 값이 a의 값과 쌍을 이루어 해당 Publisher는 (1, "a")의 값을 낸다.
4. 7의 실행에 의해 `bSubject`에 b의 값을 전달한다. `bSubject`에는 1과 2의 값이 전달되었고, 2의 값이 b의 값과 쌍을 이루어 해당 Publisher는 (2, "b")의 값을 낸다.
5. 8의 실행에 의해 `aSubject`에 3의 값을 전달한다. `bSubject`에는 해당 값과 쌍을 이룰 수 있는 값이 없으므로 해당 Publisher는 값을 내지 않는다.

3의 코드는 다음과 같이 동작한다.

1. 4의 실행에 의해 `aSubject`에 1의 값을 전달한다. `bSubject`에는 해당 값과 쌍을 이룰 수 있는 값이 없으므로 해당 Publisher는 값을 내지 않는다.
2. 5의 실행에 의해 `aSubject`에 2의 값을 전달한다. `bSubject`에는 해당 값과 쌍을 이룰 수 있는 값이 없으므로 해당 Publisher는 값을 내지 않는다.
3. 6의 실행에 의해 `bSubject`에 a의 값을 전달한다. `aSubject`에는 1과 2의 값이 전달되었고, 1의 값이 a의 값과 쌍을 이루어 해당 Publisher는 1a의 값을 낸다.
4. 7의 실행에 의해 `bSubject`에 b의 값을 전달한다. `bSubject`에는 1과 2의 값이 전달되었고, 2의 값이 b의 값과 쌍을 이루어 해당 Publisher는 2b의 값을 낸다.
5. 8의 실행에 의해 `aSubject`에 3의 값을 전달한다. `bSubject`에는 해당 값과 쌍을 이룰 수 있는 값이 없으므로 해당 Publisher는 값을 내지 않는다.

## RxSwift

Observable 결합 오퍼레이터 `zip`을 사용하여 구현할 수 있다.

```swift
let aSubject = PublishSubject<Int>()
let bSubject = PublishSubject<String>()

Observable.zip(aSubject, bSubject)
  .subscribe(onNext: { value in
    print("RxSwift Zip : \(value)")
  }, onError: { _ in
    print("RxSwift Zip Error")
  }, onCompleted: {
    print("RxSwift Zip Finish")
  })
  .disposed(by: disposeBag)

aSubject.onNext(1)
aSubject.onNext(2)
bSubject.onNext("a")
bSubject.onNext("b")
aSubject.onNext(3)

// RxSwift Zip : (1, "a")
// RxSwift Zip : (2, "b")
```

## ReactiveSwift

`zip` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
let aProperty = MutableProperty<Int>(0)
let bProperty = MutableProperty<String>("")

aProperty.signal
  .zip(with: bProperty.signal)
  .observe { event in
    switch event {
    case let .value(value):
      print("ReactiveSwift Zip : \(value)")
    case .failed:
      print("ReactiveSwift Zip Error")
    case .completed:
      print("ReactiveSwift Zip Finish")
    default:
      break
    }
  }

aProperty.value = 1
aProperty.value = 2
bProperty.value = "a"
bProperty.value = "b"
aProperty.value = 3

// ReactiveSwift Zip : (1, "a")
// ReactiveSwift Zip : (2, "b")
// ReactiveSwift Zip Finish
```

## 참고

[ReactiveX - Operators - Zip](http://reactivex.io/documentation/operators/zip.html)