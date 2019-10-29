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

각 오퍼레이터는 `Publisher` 프로토콜을 구현하는 타입을 반환하며, 대부분은 `Publishers` 열거형의 익스텐션에 구현된 중첩 타입이다. 예를 들어 `map(_:)` 오퍼레이터는 `Publishers.Map` 구조체를 반환한다. 오퍼레이터의 이름과 Publisher의 이름이 항상 동일하지는 않다.

## RxSwift

일반적으로 `Publisher` 프로토콜을 구현하는 타입은 RxSwift의 `Observable`과 비교 가능하다.

에러 연관 타입이 `Never`로 에러 값을 전달하지 않는다는 것이 보장되는 것은 RxCocoa의 `Driver`와 비교 가능하다.

하나의 값을 전달하고 종료하거나, 에러를 내고 종료하는 `Single`의 동작은 Combine의 `Future` Publisher를 사용하여 구현할 수 있다.

`Completable`, `Maybe`와 같은 RxSwift의 Traits는 Combine에 별도의 타입으로 구현되어 있지 않다.

## ReactiveSwift

일반적으로 `Publisher` 프로토콜을 구현하는 타입은 ReactiveSwift의 `SignalProducer`와 `Signal`과 비교 가능하다.

`SignalProducer`는 'Cold Observable', `Signal`은 'Hot Observable'을 의미하는데, Combine에서는 이 동작을 별도의 타입으로 구분하지 않는다.

## 문서화된 항목

- [AnyPublisher](./AnyPublisher.md)
- NotificationCenter.Publisher
- [Optional.Publisher](./Optional.Publisher.md)
- [Result.Publisher](./Result.Publisher.md)
- [URLSession.DataTaskPublisher](./URLSession.DataTaskPublisher.md)

### Convenience Publishers

- [Future](./Future.md)
- [Just](./Just.md)
- [Deferred](./Deferred.md)
- [Empty](./Empty.md)
- [Fail](./Fail.md)
- Record

### Publisher in Publishers Enum

#### Convenience Publishers

- [Sequence](./Sequence.md)
- [Catch](./Catch.md)

#### Working with Subscribers

- [ReceiveOn](./ReceiveOn.md)
- [SubscribeOn](./SubscribeOn.md)

#### Mapping Elements

- [TryScan](./TryScan.md)
- [TryMap](./TryMap.md)
- FlatMap
- [Map](./Map.md)
- [MapError](./MapError.md)
- [Scan](./Scan.md)
- [SetFailureType](./SetFailureType.md)

#### Filtering Elements

- [CompactMap](./CompactMap.md)
- [Filter](./Filter.md)
- [RemoveDuplicates](./RemoveDuplicates.md)
- [ReplaceEmpty](./ReplaceEmpty.md)
- [ReplaceError](./ReplaceError.md)
- [TryCompactMap](./TryCompactMap.md)
- [TryFilter](./TryFilter.md)
- [TryRemoveDuplicates](./TryRemoveDuplicates.md)

#### Reducing Elements

- [Collect](./Collect.md)
- CollectByCount
- CollectByTime
- [IgnoreOutput](./IgnoreOutput.md)
- [Reduce](./Reduce)
- [TryReduce](./TryReduce.md)

#### Applying Mathematical Operations on Elements

- [Comparison](./Comparison.md)
- TryComparison
- [Count](./Count.md)

#### Applying Matching Criteria to Elements

- TryAllSatisfy
- [AllSatisfy](./AllSatisfy.md)
- [Contains](./Contains.md)
- [ContainsWhere](./ContainsWhere.md)
- TryContainsWhere

#### Applying Sequence Operations to Elements

- [FirstWhere](./FirstWhere.md)
- [LastWhere](./LastWhere.md)
- [DropUntilOutput](./DropUntilOutput.md)
- [DropWhile](./DropWhile.md)
- TryDropWhile
- [Concatenate](./Concatenate.md)
- Drop
- PrefixUntilOutput
- PrefixWhile
- [First](./First.md)
- [Last](./Last.md)
- TryFirstWhere
- TryLastWhere
- TryPrefixWhile
- Output

#### Combining Elements from Multiple Publishers

- CombineLatest
- CombineLatest3
- CombineLatest4
- Merge
- Merge3
- Merge4
- Merge5
- Merge6
- Merge7
- Merge8
- MergeMany
- Zip
- Zip3
- Zip4

#### Handling Errors

- AssertNoFailure
- Catch
- TryCatch
- Retry

#### Adapting Publisher Types

- SwitchToLatest

#### Controlling Timing

- Debounce
- Delay
- MeasureInterval
- Throttle
- Timeout

#### Creating Reference-type Publishers

- Share

#### Encoding and Decoding

- Encode
- Decode

#### Identifying Properties with Key Paths

- MapKeyPath
- MapKeyPath2
- MapKeyPath3

#### Using Explicit Publisher Connections

- Autoconnect

#### Working with Multiple Subscribers

- Multicast

#### Buffering Elements

- Buffer

#### Adding Explicit Connectability

- MakeConnectable

#### Debugging

- Breakpoint
- [HandleEvents](./HandleEvents.md)
- [Print](./Print.md)