# PassthroughSubject

**제네릭 클래스** | 하위 Subscriber에게 요소를 알리는 Subject

존재하는 명령형 코드를 Combine 모델로 마이그레이션하기 위해 편리하게 사용될 수 있다.

`CurrentValueSubject`와는 다르게 초기값을 갖지 않으며, 가장 최근에 발행된 요소에 대한 버퍼를 유지하지 않는다. 현재 Subject가 가지고 있는 값에 접근할 수도 없다.

두 개의 제네릭 타입을 가진다. 하나는 값의 타입을 나타내며, 다른 하나는 에러의 타입을 나타낸다. 에러의 타입은 `Error` 프로토콜을 채택해야 한다.

```swift
let subject = PassthroughSubject<Void, Never>()
subject
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine PassthroughSubject Error")
    case .finished:
      print("Combine PassthroughSubject Finish")
    }
  }, receiveValue: {
    print("Combine PassthroughSubject")
  })
  .store(in: &cancellables)
    
subject.send(Void())

// Combine PassthroughSubject
```

`subject.send(Void())`를 호출한 시점에 Subject에 값을 전달하였으므로 값을 받은 것에 대한 클로저가 실행된다.

## RxSwift

`PublishSubject`를 사용하여 구현할 수 있다.

```swift
let subject = PublishSubject<Void>()
subject
  .subscribe(onNext: {
    print("RxSwift PassthroughSubject")
  }, onError: { _ in
    print("RxSwift PassthroughSubject Error")
  }, onCompleted: {
    print("RxSwift PassthroughSubject Finish")
  })
  .disposed(by: disposeBag)

subject.onNext(Void())

// RxSwift PassthroughSubject
```

`onNext(_:)` 메소드를 사용하여 Subject에 값을 전달한다.

## ReactiveSwift

`MutableProperty`를 사용하고 `signal` 프로퍼티를 통해 Signal을 만들어 구현할 수 있다.

```swift
let property = MutableProperty(Void())
property.signal
  .observe { event in
    switch event {
    case .value:
      print("ReactiveSwift PassthroughSubject")
    case .failed:
      print("ReactiveSwift PassthroughSubject Error")
    case .completed:
      print("ReactiveSwift PassthroughSubject Finish")
    default:
      break
    }
  }

property.value = Void()

// ReactiveSwift PassthroughSubject
// ReactiveSwift PassthroughSubject Finish
```

`value` 프로퍼티에 값을 할당하여 Property에 값을 전달한다.

`MutableProperty`로부터 `producer` 프로퍼티를 통해 SignalProducer를 만들어 사용한다면, `MutableProperty`의 초기값이 흐르게 되므로 `PassthroughSubject`의 동작을 구현할 수 없다.

스코프를 벗어날 때 `property`가 해제되어 Signal도 종료하는 모습을 보여준다.

## 참고

[ReactiveX - Subject](http://reactivex.io/documentation/subject.html)