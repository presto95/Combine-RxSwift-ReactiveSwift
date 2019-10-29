# Subscriber

Subscriber는 Publisher로부터 입력을 받기 위한 것이다.

`Publisher` 타입에서 다음의 Subscriber를 오퍼레이터로 제공한다.

- `sink(receiveCompletion:receiveValue:)`
  - 완료 신호를 받고 새로운 요소를 받을 때마다 주어진 클로저를 실행한다.
- `assign(to:on:)`
  - 새롭게 받은 값을 주어진 인스턴스의 키 경로로 식별되는 프로퍼티에 할당한다.

Combine에서 `Subscriber`는 프로토콜이다.

