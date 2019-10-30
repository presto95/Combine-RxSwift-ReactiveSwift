# Publishers.MergeMany

**제네릭 구조체** | 여러 개의 상위 Publisher에 결합 함수를 적용하여 생성되는 Publisher

아직 문서화가 되어 있지 않지만 이니셜라이저가 가변 인자를 받는 것을 보아 조합할 Publisher의 개수에 제한을 두지 않은 Publisher로 볼 수 있다.

이니셜라이저는 조합할 Publisher들을 명시한 가변 인자를 받는다.

인자에 들어가는 모든 Publisher의 Output 타입과 에러 타입은 같아야 한다.

여러 개의 스트림을 하나로 합쳐 하나의 스트림을 다루는 것처럼 하기 위해 사용할 수 있다.

`merge(with:)` 오퍼레이터는 다음의 Publisher를 반환한다.

- 인자에 Subject가 들어가는 경우 `Publishers.MergeMany` Publisher를 반환한다.
- 인자에 Publisher가 들어가는 경우 `Publishers.Merge` Publisher를 반환한다.

`Publishers.Merge`와 취할 수 있는 Publisher의 개수만 다를 뿐 동작은 같다.

## RxSwift

Observable 결합 오퍼레이터 `merge`를 사용하여 구현할 수 있다.

## ReactiveSwift

ReactiveSwift는 세 개 이상의 스트림에 대한 `merge` 동작을 구현하기 위한 오퍼레이터를 제공하지 않는다.

## 참고

[ReactiveX - Operators - Merge](http://reactivex.io/documentation/operators/merge.html)

[Publishers.Merge](./Merge.md)