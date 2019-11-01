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

각 오퍼레이터는 `Publisher` 프로토콜을 구현하는 타입을 반환한다.

기본적으로 `Publisher` 프로토콜은 오퍼레이터의 이름과 그것이 반환하는 Publisher의 이름을 비슷하게 만들어 두었으나, 이 프로토콜을 채택하는 개별 Publisher는 오퍼레이터가 다른 타입의 Publisher를 반환하게 구현해둘 수 있다. 

```swift
protocol Publisher { }

extension Publisher {
  func count() -> Publishers.Count<Self>
}

struct Just: Publisher {
  func count() -> Just<Int>
}
```

`Publisher` 프로토콜의 익스텐션으로 여러 오퍼레이터가 구현되어 있으나, `Just`와 같은 개별 Publisher가 오퍼레이터를 오버로딩하여 다른 타입을 반환하도록 구현한 경우를 어렵지 않게 찾아볼 수 있다.

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
|[Future](./Future.md)|-|
|[Just](./Just.md)|-|
|[Deferred](./Deferred.md)|-|
|[Empty](./Empty.md)|-|
|[Fail](./Fail.md)|-|
|Record|-|

### Publisher in Publishers Enum

#### Convenience Publishers

|Publisher|관련 Operator|
|---|---|
|[Sequence](./Sequence.md)|-|
|[Catch](./Catch.md)|`catch`|

#### Working with Subscribers

|Publisher|관련 Operator|
|---|---|
|[ReceiveOn](./ReceiveOn.md)|`receive`|
|[SubscribeOn](./SubscribeOn.md)|`subscribe`|

#### Mapping Elements

|Publisher|관련 Operator|
|---|---|
|[TryScan](./TryScan.md)|`tryScan`|
|[TryMap](./TryMap.md)|`tryMap`|
|FlatMap|`flatMap`|
|[Map](./Map.md)|`map` / `replaceNil` / `combineLatest` / `zip`|
|[MapError](./MapError.md)|`mapError`|
|[Scan](./Scan.md)|`scan`|
|[SetFailureType](./SetFailureType.md)|`setFailureType`|

#### Filtering Elements

|Publisher|관련 Operator|
|---|---|
|[CompactMap](./CompactMap.md)|`compactMap`|
|[Filter](./Filter.md)|`filter`|
|[RemoveDuplicates](./RemoveDuplicates.md)|`removeDuplicates`|
|[ReplaceEmpty](./ReplaceEmpty.md)|`replaceEmpty`|
|[ReplaceError](./ReplaceError.md)|`replaceError`|
|[TryCompactMap](./TryCompactMap.md)|`tryCompactMap`|
|[TryFilter](./TryFilter.md)|`tryFilter`|
|[TryRemoveDuplicates](./TryRemoveDuplicates.md)|`tryRemoveDuplicates`|

#### Reducing Elements

|Publisher|관련 Operator|
|---|---|
|[Collect](./Collect.md)|`collect`|
|[CollectByCount](./CollectByCount.md)|`collect`|
|[CollectByTime](./CollectByTime.md)|`collect`|
|[IgnoreOutput](./IgnoreOutput.md)|`ignoreOutput`|
|[Reduce](./Reduce)|`reduce`|
|[TryReduce](./TryReduce.md)|`tryReduce`|

#### Applying Mathematical Operations on Elements

|Publisher|관련 Operator|
|---|---|
|[Comparison](./Comparison.md)|`max` / `min`|
|[TryComparison](./TryComparison.md)|`tryMax` / `tryMin`|
|[Count](./Count.md)|`count`|

#### Applying Matching Criteria to Elements

|Publisher|관련 Operator|
|---|---|
|[TryAllSatisfy](./TryAllSatisfy.md)|`tryAllSatisfy`|
|[AllSatisfy](./AllSatisfy.md)|`allSatisfy`|
|[Contains](./Contains.md)|`contains`|
|[ContainsWhere](./ContainsWhere.md)|`contains`|
|[TryContainsWhere](./TryContainsWhere.md)|`tryContains`|

#### Applying Sequence Operations to Elements

|Publisher|관련 Operator|
|---|---|
|[FirstWhere](./FirstWhere.md)|`first`|
|[LastWhere](./LastWhere.md)|`last`|
|[DropUntilOutput](./DropUntilOutput.md)|`drop`|
|[DropWhile](./DropWhile.md)|`drop`|
|[TryDropWhile](./TryDropWhile.md)|`tryDrop`|
|[Concatenate](./Concatenate.md)|`append` / `prepend`|
|[Drop](./Drop.md)|`dropFirst`|
|[PrefixUntilOutput](./PrefixUntilOutput.md)|`prefix`|
|[PrefixWhile](./PrefixWhile.md)|`prefix`|
|[First](./First.md)|`first`|
|[Last](./Last.md)|`last`|
|[TryFirstWhere](./TryFirstWhere.md)|`tryFirst`|
|[TryLastWhere](./TryLastWhere.md)|`tryLast`|
|[TryPrefixWhile](./TryPrefixWhere.md)|`tryPrefix`|
|[Output](./Output.md)|`output`|

#### Combining Elements from Multiple Publishers

|Publisher|관련 Operator|
|---|---|
|[CombineLatest](./CombineLatest.md)|`combineLatest`|
|[CombineLatest3](./CombineLatest3.md)|`combineLatest`|
|[CombineLatest4](./CombineLatest4.md)|`combineLatest`|
|[Merge](./Merge.md)|`merge`|
|[Merge3](./Merge3.md)|`merge`|
|[Merge4](./Merge4.md)|`merge`|
|[Merge5](./Merge5.md)|`merge`|
|[Merge6](./Merge6.md)|`merge`|
|[Merge7](./Merge7.md)|`merge`|
|[Merge8](./Merge8.md)|`merge`|
|[MergeMany](./MergeMany.md)|`merge`|
|[Zip](./Zip.md)|`zip`|
|[Zip3](./Zip3.md)|`zip`|
|[Zip4](./Zip4.md)|`zip`|

#### Handling Errors

|Publisher|관련 Operator|
|---|---|
|[AssertNoFailure](./AssertNoFailure.md)|`assertNoFailure`|
|[Catch](./Catch.md)|`catch`|
|[TryCatch](./TryCatch.md)|`tryCatch`|
|[Retry](./Retry.md)|`retry`|

#### Adapting Publisher Types

|Publisher|관련 Operator|
|---|---|
|[SwitchToLatest](./SwitchToLatest.md)|`switchToLatest`|

#### Controlling Timing

|Publisher|관련 Operator|
|---|---|
|Debounce|`debounce`|
|Delay|`delay`|
|MeasureInterval|`measureInterval`|
|Throttle|`throttle`|
|Timeout|`timeout`|

#### Creating Reference-type Publishers

|Publisher|관련 Operator|
|---|---|
|Share|`share`|

#### Encoding and Decoding

|Publisher|관련 Operator|
|---|---|
|Encode|`encode`|
|Decode|`decode`|

#### Identifying Properties with Key Paths

|Publisher|관련 Operator|
|---|---|
|MapKeyPath|`map`|
|MapKeyPath2|`map`|
|MapKeyPath3|`map`|

#### Using Explicit Publisher Connections

|Publisher|관련 Operator|
|---|---|
|Autoconnect|`autoconnect`|

#### Working with Multiple Subscribers

|Publisher|관련 Operator|
|---|---|
|Multicast|`multicast`|

#### Buffering Elements

|Publisher|관련 Operator|
|---|---|
|Buffer|`buffer`|

#### Adding Explicit Connectability

|Publisher|관련 Operator|
|---|---|
|MakeConnectable|`makeConnectable`|

#### Debugging

|Publisher|관련 Operator|
|---|---|
|Breakpoint|`breakpoint` / `breakpointOnError`|
|[HandleEvents](./HandleEvents.md)|`handleEvents`|
|[Print](./Print.md)|`print`|

### Misc.

| Publisher | 관련 Operator |
| --------- | ------------- |
|[AnyPublisher](./AnyPublisher.md)|`eraseToAnyPublisher`|
|NotificationCenter.Publisher|-|
|[Optional.Publisher](./Optional.Publisher.md)|-|
|[Result.Publisher](./Result.Publisher.md)|-|
|[URLSession.DataTaskPublisher](./URLSession.DataTaskPublisher.md)|-|