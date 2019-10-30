# Publisher

Publisher는 시간이 지남에 따라 값의 시퀀스를 전달할 수 있다.

Combine에서 `Publisher`는 프로토콜로 정의되어 있으며, 다음의 타입이 이 프로토콜을 채택한다.

- `Publishers` 열거형의 중첩 타입으로 정의된 여러 구조체
- `Just`, `Empty`와 같은 Convenience Publisher
- 타입을 지운 Publisher인 `AnyPublisher` 구조체
- 등등

두 개의 연관 타입이 정의되어 있다. 

- 첫 번째 연관 타입은 값의 타입(`Output`)이다.
- 두 번째 연관 타입은 에러의 타입(`Failure`)이다. 
  - 에러의 타입은 `Error` 프로토콜을 채택해야 한다.

`Publisher` 프로토콜의 익스텐션으로 여러 오퍼레이터*operator*가 정의되어 있으며, 이를 사용해서 이벤트 처리 체인을 구성할 수 있다.

각 오퍼레이터는 `Publisher` 프로토콜을 구현하는 타입을 반환하며, 대부분은 `Publishers` 열거형의 익스텐션에 구현된 중첩 타입이다. 예를 들어 `map(_:)` 오퍼레이터는 `Publishers.Map` 구조체를 반환한다. 

오퍼레이터의 이름과 Publisher의 이름이 항상 동일한 것은 아니며, 오퍼레이터는 그 이름과 비슷한 이름을 가진 Publisher를 항상 반환한다고 말할 수 없다.

예를 들어 오퍼레이터 수행 결과가 시퀀스 형태로 나오는 경우 `Publishers.Sequence`, 결과가 있을 수도 있고 없을 수도 있는 경우 `Optional.Publisher`, 결과가 성공 또는 실패 정보를 담는다면 `Result.Publisher` 등의 Publisher를 반환할 수 있다.

## RxSwift

일반적으로 `Publisher` 프로토콜을 구현하는 타입은 RxSwift의 `Observable`과 비교 가능하다.

에러 연관 타입이 `Never`로 에러 값을 전달하지 않는다는 것이 보장되는 것은 RxCocoa의 `Driver`와 비교 가능하다.

하나의 값을 전달하고 종료하거나, 에러를 내고 종료하는 `Single`의 동작은 Combine의 `Future` Publisher를 사용하여 구현할 수 있다.

`Completable`, `Maybe`와 같은 RxSwift의 Traits는 Combine에 별도의 타입으로 구현되어 있지 않다.

## ReactiveSwift

일반적으로 `Publisher` 프로토콜을 구현하는 타입은 ReactiveSwift의 `SignalProducer`와 `Signal`과 비교 가능하다.

`SignalProducer`는 'Cold Observable', `Signal`은 'Hot Observable'을 의미하는데, Combine에서는 이 동작을 별도의 타입으로 구분하지 않는다.

## Publisher와 Operator 쌍

### Convenience Publishers

|Publisher|관련 Operator|
|---|---|
|[Future](./Future.md)|X|
|[Just](./Just.md)|X|
|[Deferred](./Deferred.md)|X|
|[Empty](./Empty.md)|`ignoreOutput()`|
|[Fail](./Fail.md)|X|
|Record|X|

### Publisher in Publishers Enum

#### Convenience Publishers

|Publisher|관련 Operator|
|---|---|
|[Sequence](./Sequence.md)|`append(_:)` / `prepend(_:)` / `drop(while:)` / `prefix(while:)` / `prefix(_:)` / `output(in:)`|
|[Catch](./Catch.md)|`catch(_:)`|

#### Working with Subscribers

|Publisher|관련 Operator|
|---|---|
|[ReceiveOn](./ReceiveOn.md)|`receive(on:options:)`|
|[SubscribeOn](./SubscribeOn.md)|`subscribe(on:options:)`|

#### Mapping Elements

|Publisher|관련 Operator|
|---|---|
|[TryScan](./TryScan.md)|`tryScan(_:_:)`|
|[TryMap](./TryMap.md)|`tryMap(_:)`|
|FlatMap|`flatMap(maxPublishers:_:)`|
|[Map](./Map.md)|`map(_:)` / `replaceNil(with:)` / `combineLatest` 패밀리 / `zip` 패밀리|
|[MapError](./MapError.md)|`mapError(_:)`|
|[Scan](./Scan.md)|`scan(_:_:)`|
|[SetFailureType](./SetFailureType.md)|`setFailureType(to:)`|

#### Filtering Elements

|Publisher|관련 Operator|
|---|---|
|[CompactMap](./CompactMap.md)|`compactMap(_:)`|
|[Filter](./Filter.md)|`filter(_:)`|
|[RemoveDuplicates](./RemoveDuplicates.md)|`removeDuplicates` 패밀리|
|[ReplaceEmpty](./ReplaceEmpty.md)|`replaceEmpty(with:)`|
|[ReplaceError](./ReplaceError.md)|`replaceError(with:)`|
|[TryCompactMap](./TryCompactMap.md)|`tryCompactMap(_:)`|
|[TryFilter](./TryFilter.md)|`tryFilter(_:)`|
|[TryRemoveDuplicates](./TryRemoveDuplicates.md)|`tryRemoveDuplicates(_:)`|

#### Reducing Elements

|Publisher|관련 Operator|
|---|---|
|[Collect](./Collect.md)|`collect()`|
|CollectByCount|`collect(_:)`|
|CollectByTime|`collect(_:options:)`|
|[IgnoreOutput](./IgnoreOutput.md)|`ignoreOutput()`|
|[Reduce](./Reduce)|`reduce(_:_:)`|
|[TryReduce](./TryReduce.md)|`tryReduce(_:_:)`|

#### Applying Mathematical Operations on Elements

|Publisher|관련 Operator|
|---|---|
|[Comparison](./Comparison.md)|`max` 패밀리 / `min` 패밀리|
|TryComparison|`tryMax(_:)` / `tryMin(_:)`|
|[Count](./Count.md)|`count()`|

#### Applying Matching Criteria to Elements

|Publisher|관련 Operator|
|---|---|
|TryAllSatisfy|`tryAllSatisfy(_:)`|
|[AllSatisfy](./AllSatisfy.md)|`allSatisfy(_:)`|
|[Contains](./Contains.md)|`contains(_:)`|
|[ContainsWhere](./ContainsWhere.md)|`contains(where:)`|
|TryContainsWhere|`tryContains(where:)`|

#### Applying Sequence Operations to Elements

|Publisher|관련 Operator|
|---|---|
|[FirstWhere](./FirstWhere.md)|`first(where:)`|
|[LastWhere](./LastWhere.md)|`last(where:)`|
|[DropUntilOutput](./DropUntilOutput.md)|`drop(untilOutputFrom:)`|
|[DropWhile](./DropWhile.md)|`drop(while:)`|
|TryDropWhile|`tryDrop(while:)`|
|[Concatenate](./Concatenate.md)|`append` 패밀리 / `prepend` 패밀리|
|[Drop](./Drop.md)|`dropFirst(_:)`|
|[PrefixUntilOutput](./PrefixUntilOutput.md)|`prefix(untilOutputFrom:)`|
|[PrefixWhile](./PrefixWhile.md)|`prefix(while:)`|
|[First](./First.md)|`first()`|
|[Last](./Last.md)|`last()`|
|TryFirstWhere|`tryFirst(where:)`|
|TryLastWhere|`tryLast(where:)`|
|TryPrefixWhile|`tryPrefix(while:)`|
|[Output](./Output.md)|`output` 패밀리|

#### Combining Elements from Multiple Publishers

|Publisher|관련 Operator|
|---|---|
|[CombineLatest](./CombineLatest.md)|`combineLatest(_:)` / `combineLatest(_:_:)`|
|CombineLatest3|`combineLatest(_:_:)` / `combineLatest(_:_:_:)`|
|CombineLatest4|`combineLatest(_:_:_:)` / `combineLatest(_:_:_:_:)`|
|[Merge](./Merge.md)|`merge(with:)`|
|Merge3|`merge(with:_:)`|
|Merge4|`merge(with:_:_:)`|
|Merge5|`merge(with:_:_:_:)`|
|Merge6|`merge(with:_:_:_:_:)`|
|Merge7|`merge(with:_:_:_:_:_:)`|
|Merge8|`merge(with:_:_:_:_:_:_:)`|
|MergeMany|`merge(with:)`|
|[Zip](./Zip.md)|`zip(_:)` / `zip(_:_:)`|
|Zip3|`zip(_:_:)` / `zip(_:_:_:)`|
|Zip4|`zip(_:_:_:)` / `zip(_:_:_:_:)`|

#### Handling Errors

|Publisher|관련 Operator|
|---|---|
|AssertNoFailure|`assertNoFailure(_:file:line:)`|
|Catch|`catch(_:)`|
|TryCatch|`tryCatch(_:)`|
|Retry|`retry(_:)`|

#### Adapting Publisher Types

|Publisher|관련 Operator|
|---|---|
|SwitchToLatest|`switchToLatest()`|

#### Controlling Timing

|Publisher|관련 Operator|
|---|---|
|Debounce|`debounce(for:scheduler:options:)`|
|Delay|`delay(for:tolerance:scheduler:options:)`|
|MeasureInterval|`measureInterval(using:options:)`|
|Throttle|`throttle(for:scheduler:latest:)`|
|Timeout|`timeout(_:scheduler:options:customError:)`|

#### Creating Reference-type Publishers

|Publisher|관련 Operator|
|---|---|
|Share|`share()`|

#### Encoding and Decoding

|Publisher|관련 Operator|
|---|---|
|Encode|`encode(encoder:)`|
|Decode|`decode(type:decoder:)`|

#### Identifying Properties with Key Paths

|Publisher|관련 Operator|
|---|---|
|MapKeyPath|`map(_:)`|
|MapKeyPath2|`map(_:_:)`|
|MapKeyPath3|`map(_:_:_:)`|

#### Using Explicit Publisher Connections

|Publisher|관련 Operator|
|---|---|
|Autoconnect|`autoconnect()`|

#### Working with Multiple Subscribers

|Publisher|관련 Operator|
|---|---|
|Multicast|`multicast` 패밀리|

#### Buffering Elements

|Publisher|관련 Operator|
|---|---|
|Buffer|`buffer(size:prefetch:whenFull:)`|

#### Adding Explicit Connectability

|Publisher|관련 Operator|
|---|---|
|MakeConnectable|`makeConnectable()`|

#### Debugging

|Publisher|관련 Operator|
|---|---|
|Breakpoint|`breakpoint(receiveSubscription:receiveOutput:receiveCompletion:)`<br />`breakpointOnError()`|
|[HandleEvents](./HandleEvents.md)|`handleEvents(receiveSubscription:receiveOutput:receiveCompletion:receiveCancel:receiveRequest:)`|
|[Print](./Print.md)|`print(_:to:)`|

### Misc.

| Publisher | 관련 Operator |
| --------- | ------------- |
|[AnyPublisher](./AnyPublisher.md)|`eraseToAnyPublisher()`|
|NotificationCenter.Publisher|X|
|[Optional.Publisher](./Optional.Publisher.md)|`first()` / `last()` / `first(where:)` / `last(where:)` / `dropFirst()` / `output(at:)`|
|[Result.Publisher](./Result.Publisher.md)|`count()` / `contains(_:)` / `contains(where:)`|
|[URLSession.DataTaskPublisher](./URLSession.DataTaskPublisher.md)|X|