# Publishers.TryScan

**제네릭 구조체**

Publisher가 내는 요소에 연속적으로 함수를 적용하고 실행한 후, 성공적으로 실행된 함수의 반환 값을 낸다. 함수는 에러를 던질 수 있다.

이니셜라이저는 세 개의 인자를 받는다.

- `upstream` : `Publisher` 프로토콜을 따르는 Publisher를 넘겨준다.
- `initialResult` : 초기 값. 
- `nextPartialResult` : 다음 부분 결과. 요소에 연속적으로 적용할 클로저를 넘겨준다. 클로저는 에러를 던질 수 있다.

Swift의 `reduce` 함수와 동작이 비슷하지만 `reduce`가 최종 결과만을 반환한다면 `tryScan`은 함수가 적용된 요소와 다음 요소에 대해 함수를 적용하여 각 요소가 점진적으로 교체된다.

함수가 에러를 던지지 않으면 해당 요소에 대한 변환은 적용되어 값을 내고, 에러를 던지면 에러와 함께 종료한다.

`tryScan` 오퍼레이터는 해당 Publisher를 반환한다.

`Publishers.Scan`의 동작과 같으나 동작을 수행하다가 에러를 낼 수 있다는 차이가 있다.

## RxSwift

Observable 변환 오퍼레이터 `scan`을 사용하여 구현할 수 있다.

## ReactiveSwift

ReactiveSwift는 `scan`의 동작을 수행할 때 에러를 던질 수 있는 방법을 제공하지 않는다.

## 참고

[ReactiveX - Operators - Scan](http://reactivex.io/documentation/operators/scan.html)

[Publishers.Scan](./Scan.md)