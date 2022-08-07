# [RxSwift] Scheduler

## Scheduler

: 프로세스가 있는 곳의 Context (= thread, dispatchQueue)

특정 코드가 실행되는 Context를 추상화한 것이다.

![Untitled](%5BRxSwift%5D%20Scheduler%20f6ffa4b2a1d8448d832debcf56674d3b/Untitled.png)

Cocoa에서는 Main Queue를 사용하지만 RxSwift에서는 MainScheduler를 사용한다.

**연산자**

- subscribeOn : Observable의 프로세싱이 어디서 일어나게 할 것인지 결정하는 연산자
- observeOn : Observer가 Observable을 어디서 observe할 것인지

일반적으로 subscribeOn(*:)은 Background Thread에서, observeOn(*:)은 Main Thread에서 사용

## observeOn vs subscribeOn

다른 스케줄러에서 작업하고자 한다면 observeOn을 사용한다.

sequence(subscribe method)를 생성하고, 특정 스케줄러에서 dispose를 호출하고 싶다면 subscribeOn(scheduler)를 사용한다.

```swift
sequence1
  .observeOn(backgroundScheduler)
  .map { n in
      print("This is performed on the background scheduler")
  }
  .observeOn(MainScheduler.instance)
  .map { n in
      print("This is performed on the main scheduler")
  }
```

첫번째 map은 백그라운드 스케줄러 위에서 작업하는 것이고, 두번째 map은 메인 스케줄러 위에서 작업하는 것이다.

Sequence의 생성은 subscribe가 호출될 때 시작된다!

구독되지 않으면 observable은 선언만 되어있는 상태이므로 어떤 이벤트도 일어나지 않는다. 따라서 subscribe가 호출되어야만 observable이 생성되는 것이다.

그래서 subscribeOn은 시퀀스가 생성될 때(=subscribe가 호출될 때)의 스케줄러를 지정하는 것이다.

```swift
// observeOn과 subscribeOn을 사용한 예제
Observable<Int>.create { observer in
    observer.onNext(1)
    observer.onNext(2)
												
    print("Hi \(Thread.isMainThread)")

    observer.onCompleted()
    return Disposables.create()
}
.observeOn(MainScheduler.instance)
.subscribeOn(ConcurrentDispatchQueueScheduler(qos: .background))
.subscribe(onNext: { el in	    
    print("onNext \(el) \(Thread.isMainThread)") 
}, onDisposed: {(
    print("dispose \(Thread.isMainThread)")
)})

/*
	onNext 1 true
	onNext 2 true
	Hi false
	dispose true
*/
```

observeOn을 메인 쓰레드로 지정하였으므로 구독하는 작업을 메인 쓰레드에서 진행한다.

subscribeOn을 백그라운드로 설정하였으므로 observable을 백그라운드에서 생성하게 된다.

observeOn : operator(map, filter, etc)와 subscribe 작업을 다른 스케줄러에서 사용하고 싶을 때 사용.

subscribeOn : observable을 특정 스케줄러에서 생성하고 싶을 때 사용.

## Scheduler의 종류

Serial Scheduler를 사용한다면 Rx는 직렬적으로 진행한다.

Concurrent Scheduler를 사용한다면, Rx는 동시에 진행한다.

![Untitled](%5BRxSwift%5D%20Scheduler%20f6ffa4b2a1d8448d832debcf56674d3b/Untitled%201.png)

### 1) MainScheduler

- 메인 쓰레드에서 가장 위에 존재한다.
- UI와 높은 우선순위 테스크를 진행한다. heavy task는 피해야 한다. (API request등)
- UI 갱신시 이 스케줄러로 변경하여 사용한다.

### 2) SerialDispatchQueueScheduler

- background에서 추출하는 일을 처리할 때 사용한다.
- 특정 dispatchQueue에서 수행해야하는 작업을 추상화한다.
- Serial하게 작업을 처리합니다. (사실 MainScheduler도 SerialDispatchQueueScheduler의 일종이다.)
- (Firebase를 사용할 때 서버의 endpoint에 너무 많은 pressured을 줄일 수 있음)

### 3) ConcurrentDispatchQueueScheduler

- SerialDispatchQueueScheduler와 같이 추출하는 일을 처리할 때 사용한다, 단 병렬적
- 특정 dispatchQueue에서 수행해야하는 작업을 추상화한다.
- Concurrent하게 작업을 처리한다.
- (작업량이 많은 일에 사용)

### 4) TestScheduler

- 테스트를 위한 것이며, production code에는 사용하지 않는다.
- RxTest 라이브러리에 존재

```swift
let scheduler = TestScheduler(initialClock: 0)
let xs = scheduler.createColdObservable([ next(50, 42),
	next(60, 43),
	completed(70)
])

let res = scheduler.start {
	xs.delaySubscription(30, scheduler: scheduler) 
}

// test
XCTAssertEqual(res.events, [ 
	next(280, 42),
	next(290, 43), 
    completed(300)
])
```

참고:

[https://sweepty.medium.com/rxswift-scheduler-제대로-알아보기-f2e26aeb829d](https://sweepty.medium.com/rxswift-scheduler-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0-f2e26aeb829d)

[https://ios-development.tistory.com/133](https://ios-development.tistory.com/133)

[https://velog.io/@hansangjin96/RxSwift-Scheduler-작성중](https://velog.io/@hansangjin96/RxSwift-Scheduler-%EC%9E%91%EC%84%B1%EC%A4%91)