# CurrentValueSubject

**제네릭 클래스** | 하나의 값을 감싸고 값이 변화할 때마다 새로운 요소를 내는 Subject

`PassthroughSubject`와는 다르게 초기값을 가지며, 가장 최근에 발행된 요소에 대한 버퍼를 유지한다.

두 개의 제네릭 타입을 가진다. 하나는 값의 타입을 나타내며, 다른 하나는 에러의 타입을 나타낸다. 에러의 타입은 `Error` 프로토콜을 채택해야 한다.

이니셜라이저에 발행할 초기값을 넘겨주어 인스턴스를 생성한다.

`value` 프로퍼티를 통하여 해당 Subject가 감싼 값에 접근할 수 있다.

```swift
let subject = CurrentValueSubject<Void, Never>(Void())
subject
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine CurrentValueSubject Error")
    case .finished:
      print("Combine CurrentValueSubject Finish")
    }
  }, receiveValue: {
    print("Combine CurrentValueSubject")
  })
  .store(in: &cancellables)
    
subject.send(Void())

// Combine CurrentValueSubject
// Combine CurrentValueSubject
```

초기값을 설정하였으므로 `subject`를 구독한 순간에 값을 받은 것에 대한 클로저가 실행된다.

이후 `subject.send(Void())`를 호출하여 Subject에 값을 전달하였으므로 값을 받은 것에 대한 클로저가 한 번 더 실행된다.

## RxSwift

`BehaviorSubject`를 사용하여 구현할 수 있다.

```swift
let subject = BehaviorSubject(value: Void())
subject
  .subscribe(onNext: {
    print("RxSwift CurrentValueSubject")
  }, onError: { _ in
    print("RxSwift CurrentValueSubject Error")
  }, onCompleted: {
    print("RxSwift CurrentValueSubject Finish")
  })
  .disposed(by: disposeBag)

subject.onNext(Void())

// RxSwift CurrentValueSubject
// RxSwift CurrentValueSubject
```

`onNext(_:)` 메소드를 사용하여 Subject에 값을 전달한다.

## ReactiveSwift

`MutableProperty`를 사용하고 `producer` 프로퍼티를 통해 SignalProducer를 만들어 구현할 수 있다.

```swift
let property = MutableProperty(Void())
property.producer
  .start { event in
    switch event {
    case .value:
      print("ReactiveSwift CurrentValueSubject")
    case .failed:
      print("ReactiveSwift CurrentValueSubject Error")
    case .completed:
      print("ReactiveSwift CurrentValueSubject Finish")
    default:
      break
    }
  }

property.value = Void()

// ReactiveSwift CurrentValueSubject
// ReactiveSwift CurrentValueSubject
// ReactiveSwift CurrentValueSubject Finish
```

`value` 프로퍼티에 값을 할당하여 Property에 값을 전달한다.

`MutableProperty`로부터 `signal` 프로퍼티를 통해 Signal을 만들어 사용한다면, `MutableProperty`의 초기값이 흐르게 되지 않으므로 `CurrentValueSubject`의 동작을 구현할 수 없다.

스코프를 벗어날 때 `property`가 해제되어 SignalProducer도 종료하는 모습을 보여준다.

## 참고

[ReactiveX - Subject](http://reactivex.io/documentation/subject.html)