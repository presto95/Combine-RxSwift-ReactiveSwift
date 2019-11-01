# Publishers.Zip4

**제네릭 구조체** | 네 개의 상위 Publisher에 zip 함수를 적용하여 생성되는 Publisher

네 개의 스트림의 배출 항목이 쌍을 이룰 때 그 값들에 대하여 함수를 적용한 값을 발행한다.

이니셜라이저는 조합할 네 개의 Publisher를 받는다.

인자에 들어가는 모든 Publisherd의 에러 타입은 같아야 한다.

`zip` 오퍼레이터와 관련이 있다.

`Publishers.Zip`과 취할 수 있는 Publisher의 개수만 다를 뿐 동작은 같다.

## RxSwift

`zip` 오퍼레이터를 사용하여 구현할 수 있다.

## ReactiveSwift

세 개 이상의 스트림에 대한 `zip` 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - Zip](http://reactivex.io/documentation/operators/zip.html)

[Publishers.Zip](./Zip.md)