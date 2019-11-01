# Publishers.Print

**제네릭 구조체** | 발행하는 모든 이벤트에 대하여, 선택적으로 접두사를 붙일 수 있는 로그 메세지를 출력하는 Publisher

이니셜라이저는 세 개의 인자를 받는다.

- `upstream` : 상위에 흐르는 Publisher
- `prefix` : 로그 메세지의 앞에 붙일 문자열
- `to` : 텍스트를 출력할 곳. nil이면 표준 출력으로 출력한다.

이벤트에 대하여 로그 메세지를 출력한다.

다음의 이벤트를 전달받으면 로그 메세지를 출력한다.

- 구독*Subscription*
- 값*Value*
- 정상 종료*Normal Completion*
- 실패*Failure*
- 취소*Cancellation*

매 오퍼레이터 실행 후 Publisher의 상태를 확인하는 등 디버깅 과정에서 유용하게 사용할 수 있다.

`print` 오퍼레이터와 관련이 있다.

```swift
// Publishers.Print Publisher
Publishers
  .Print(upstream: Just(Void()), prefix: "")
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Print Error")
    case .finished:
      print("Combine Print Finish")
    }
  }, receiveValue: {
    print("Combine Print")
  })
  .store(in: &cancellables)

// print Operator
Just(Void())
  .print()
  .sink(receiveCompletion: { completion in
    switch completion {
    case .failure:
      print("Combine Print Error")
    case .finished:
      print("Combine Print Finish")
    }
  }, receiveValue: {
    print("Combine Print")
  })
  .store(in: &cancellables)

// receive subscription: (Just)
// request unlimited
// receive value: (())
// Combine Print
// receive finished
// Combine Print Finish
```

`prefix`에 실행 시각 / 파일, 라인, 함수 이름 정보 등을 담으면 더욱 디버깅하기 좋을 것이다.

## RxSwift

`debug` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
Observable.just(Void())
  .debug()
  .subscribe(onNext: {
    print("RxSwift Print")
  }, onError: { _ in
    print("RxSwift Print Error")
  }, onCompleted: {
    print("RxSwift Print Finish")
  })
  .disposed(by: disposeBag)

// 2019-10-29 22:11:20.753: ContentViewModel.swift:57 (rxSwift()) -> subscribed
// 2019-10-29 22:11:20.755: ContentViewModel.swift:57 (rxSwift()) -> Event next(())
// RxSwift Print
// 2019-10-29 22:11:20.755: ContentViewModel.swift:57 (rxSwift()) -> Event completed
// RxSwift Print Finish
// 2019-10-29 22:11:20.755: ContentViewModel.swift:57 (rxSwift()) -> isDisposed
```

## ReactiveSwift

`logEvents` 오퍼레이터를 사용하여 구현할 수 있다.

```swift
SignalProducer(value: Void())
  .logEvents()
  .start { event in
    switch event {
    case .value:
      print("ReactiveSwift Print")
    case .failed:
      print("ReactiveSwift Print Error")
    case .completed:
      print("ReactiveSwift Print Finish")
    default:
      break
    }
  }

// [] starting fileName: /Users/presto/Developer/Xcode/ReactivePractice/ReactivePractice/ContentViewModel.swift, functionName: reactiveSwift(), lineNumber: 71
// [] value fileName: /Users/presto/Developer/Xcode/ReactivePractice/ReactivePractice/ContentViewModel.swift, functionName: reactiveSwift(), lineNumber: 71
// ReactiveSwift Print
// [] completed fileName: /Users/presto/Developer/Xcode/ReactivePractice/ReactivePractice/ContentViewModel.swift, functionName: reactiveSwift(), lineNumber: 71
// [] terminated fileName: /Users/presto/Developer/Xcode/ReactivePractice/ReactivePractice/ContentViewModel.swift, functionName: reactiveSwift(), lineNumber: 71
// ReactiveSwift Print Finish
// [] disposed fileName: /Users/presto/Developer/Xcode/ReactivePractice/ReactivePractice/ContentViewModel.swift, functionName: reactiveSwift(), lineNumber: 71
// [] started fileName: /Users/presto/Developer/Xcode/ReactivePractice/ReactivePractice/ContentViewModel.swift, functionName: reactiveSwift(), lineNumber: 71
```

