# Publishers.TryScan

**제네릭 구조체**

이니셜라이저는 세 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `initialResult` : 초기 값.
- `nextPartialResult` : 요소에 연속적으로 적용할 에러를 던지는 클로저

Publisher가 내는 요소에 연속적으로 함수를 적용하고 실행한 후, 성공적으로 실행된 함수의 반환 값을 낸다. 함수는 에러를 던질 수 있다.

Swift의 `reduce` 함수와 동작이 비슷하지만 `reduce`가 최종 결과만을 반환한다면 `tryScan`은 함수가 적용된 요소와 다음 요소에 대해 함수를 적용하여 각 요소가 점진적으로 교체된다.

함수가 에러를 던지지 않으면 해당 요소에 대한 변환은 적용되어 값을 내고, 에러를 던지면 에러와 함께 종료한다.

`tryScan` 오퍼레이터와 관련이 있다.

`Publishers.Scan`의 동작과 같으나 동작을 수행하다가 에러를 낼 수 있다는 차이가 있다.

## RxSwift

`scan` 오퍼레이터와 관련이 있다.

## ReactiveSwift

해당 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - Scan](http://reactivex.io/documentation/operators/scan.html)

[Publishers.Scan](./Scan.md)