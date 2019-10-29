# Subject

Subject는 요소를 발행하기 위해 외부 호출자에게 메소드를 노출하는 Publisher다.

Subject도 Publisher인데, 스트림에 값을 주입할 수 있다.

Combine을 사용하여 명령형 코드를 마이그레이션할 때 유용하게 사용될 수 있다.

`send()` 메소드 패밀리를 제공한다.

- Output 타입이 `Void`일 때 `send()` 메소드로 이벤트가 발생했으나 전달할 필요는 없음을 알린다.
  - `send(Void())`를 호출하여 Void 값을 전달하는 것과 다르게 동작한다.
- `send(_:)` 메소드로 Subscriber에게 값을 전달한다.
- `send(completion:)` 메소드로 Subscriber에게 에러 또는 종료 신호를 전달한다.
- `send(subscription:)` 메소드로 Subscriber에게 구독 정보를 전달한다.

Combine에서 `Subject`는 프로토콜이다. `Publisher` 프로토콜을 채택하며, 구조체는 해당 프로토콜을 채택할 수 없다.

`CurrentValueSubject`와 `PassthroughSubject`가 해당 프로토콜을 구현한다.

일반적으로 'Hot Observable'의 개념을 갖는다.

## RxSwift

RxSwift에도 Subject의 개념이 존재한다. 이들은 `SubjectType` 프로토콜을 채택한다.

`AsyncSubject` / `BehaviorSubject` / `PublishSubject` / `ReplaySubject` 등이 존재한다.

Combine의 `CurrentValueSubject`는 초기값을 가지므로 RxSwift의 `BehaviorSubject`와 비교할 수 있다. 

Combine의 `PassthroughSubject`는 초기값을 갖지 않고 최근에 발행된 요소를 기억하는 버퍼를 갖지 않으므로 RxSwift의 `PublishSubject`와 비교할 수 있다.

## ReactiveSwift

ReactiveSwift에도 Subject의 개념이 존재한다. `Property`라는 이름으로 구현되어 `PropertyProtocol` 프로토콜을 채택한다.

`Property` / `MutableProperty` 등이 존재한다. `Property`는 읽기 전용인 반면에 `MutableProperty`는 읽고 쓰기가 모두 가능하다.

Combine과 RxSwift가 초기값을 갖는 Subject와 초기값을 갖지 않는 Subject를 구분해 놓은 반면 ReactiveSwift의 Property는 초기값을 가질 수 있다.

각 Property에서 Signal 또는 SignalProducer를 만들 수 있으며, 각각 'Hot Observable'과 'Cold Observable'의 개념을 적용하고 싶을 때 사용할 수 있다.

## 문서화된 항목

- [CurrentValueSubject](./CurrentValueSubject.md)
- [PassthroughSubject](./PassthroughSubject.md)