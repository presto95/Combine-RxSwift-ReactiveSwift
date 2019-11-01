# Publishers.TryCatch

**제네릭 구조체** | 실패하는 Publisher를 다른 Publisher로 교체하거나 새로운 에러를 내는 것으로 상위에 흐르는 Publisher로부터 에러를 처리하는 Publisher

이니셜라이저는 두 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `handler` : 상위에 흐르는 Publisher가 에러를 내는 경우 그 실패를 인자로 받아 상위 Publisher를 교체할 새로운 Publisher를 반환하거나 에러를 던지는 클로저

상위에 흐르는 Publisher와 이를 대체할 Publisher의 Output 타입은 동일해야 한다.

상위 Publisher가 에러를 내는 경우 에러를 내며 종료하는 대신 다른 Publisher로 대체하거나 새로운 에러를 낸다.

`tryCatch` 오퍼레이터와 관련이 있다.

`Publishers.Catch`의 동작과 같으나 동작을 수행하다가 에러를 낼 수 있다는 차이가 있다.

## RxSwift

`catchError` 오퍼레이터를 사용하여 구현할 수 있다.

## ReactiveSwift

해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - Catch](http://reactivex.io/documentation/operators/catch.html)

[Publishers.Catch](./Catch.md)