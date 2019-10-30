# Publishers.TryCatch

**제네릭 구조체** | 실패하는 Publisher를 다른 Publisher로 교체하거나 새로운 에러를 내는 것으로 상위에 흐르는 Publisher로부터 에러를 처리하는 Publisher

상위 Publisher가 에러를 내는 경우 에러를 내며 종료하는 대신 다른 Publisher로 대체하거나 새로운 에러를 낸다.

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `handler` : 상위에 흐르는 Publisher가 에러를 내는 경우 그 실패를 인자로 받아 상위 Publisher를 교체할 새로운 Publisher를 반환하거나 에러를 던지는 클로저

상위에 흐르는 Publisher와 이를 대체할 Publisher의 Output 타입은 동일해야 한다.

`tryCatch(_:)` 오퍼레이터는 해당 Publisher를 반환한다.

`Publishers.Catch`의 동작과 같으나 동작을 수행하다가 에러를 낼 수 있다는 차이가 있다.

## RxSwift

Observable 오류 처리 오퍼레이터 `catchError`를 사용하여 구현할 수 있다.

## ReactiveSwift

ReactiveSwift는 `catch`의 동작을 수행할 때 에러를 던질 수 있는 방법을 제공하지 않는다.

## 참고

[ReactiveX - Operators - Catch](http://reactivex.io/documentation/operators/catch.html)

[Publishers.Catch](./Catch.md)