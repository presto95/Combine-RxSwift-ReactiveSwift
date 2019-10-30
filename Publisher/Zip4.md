# Publishers.Zip4

**제네릭 구조체** | 네 개의 상위 Publisher에 zip 함수를 적용하여 생성되는 Publisher

네 개의 스트림의 배출 항목이 쌍을 이룰 때 그 값들에 대하여 함수를 적용한 값을 발행한다.

이니셜라이저는 조합할 네 개의 Publisher를 받는다.

인자에 들어가는 모든 Publisherd의 에러 타입은 같아야 한다.

다른 세 개의 Publisher를 인자로 받는 `zip(_:_:_:)` 오퍼레이터는 해당 Publisher를 반환한다. 이 때 조합 결과는 튜플의 형태로 발행된다.

다른 세 개의 Publisher를 인자로 받고 transform 클로저를 명시한 `zip(_:_:_:_:)` 오퍼레이터는 `Publishers.Map` Publisher를 반환한다.

`Publishers.Zip`과 취할 수 있는 Publisher의 개수만 다를 뿐 동작은 같다.

## RxSwift

Observable 결합 오퍼레이터 `zip`을 사용하여 구현할 수 있다.

## ReactiveSwift

ReactiveSwift는 세 개 이상의 스트림에 대한 `zip` 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - Zip](http://reactivex.io/documentation/operators/zip.html)

[Publishers.Zip](./Zip.md)