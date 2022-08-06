# Observable이란?

## Observable

여러 이벤트들을 방출(=”emit”)할 수 있는 대상

emit : 이벤트를 계속해서 비동기적으로 생성하는 기능

**sequence란?**

Observable = Observable sequence = sequence

Observable 이벤트들은 sequence라고 할 수 있다.

### **이벤트의 종류**

- next
    - 구성 요소를 계속해서 방출할 수 있는 기능 (= observable 구독자에게 데이터 전달)
    
    ![Untitled](Observable%E1%84%8B%E1%85%B5%E1%84%85%E1%85%A1%E1%86%AB%2029755755cc8d4c23bac27bfa6afe7a06/Untitled.png)
    
- completed
    - 성공적으로 next이벤트가 완료되었을 때 발생하며, 이벤트를 종료시킨다. (= observable 구독자에게 완료되었음을 알림)
    
    ![Untitled](Observable%E1%84%8B%E1%85%B5%E1%84%85%E1%85%A1%E1%86%AB%2029755755cc8d4c23bac27bfa6afe7a06/Untitled%201.png)
    
- error
    - 이벤트에 오류가 있음을 알고 중간에 스트림을 종료시킬 수 있는 기능 (observable 구독자에게 오류를 알린다.)
    
    ![Untitled](Observable%E1%84%8B%E1%85%B5%E1%84%85%E1%85%A1%E1%86%AB%2029755755cc8d4c23bac27bfa6afe7a06/Untitled%202.png)
    

```swift
API.download(file: "http://www...")
  .subscribe(onNext: { data in
    ... append data to temporary file
  },
  onError: { error in
    ... display error to user
  },
  onCompleted: {
    ... use downloaded file
  })
```

- error, complete가 발생한 상황에 모두 Dispose가 호출되고 스트림 종료된다.
- **onDisposed**: 시컨스가 버려진 경우 호출된다.
- Observable 타입들은 모두 Disposable 타입을 반환한다. 이 값을 이용해 스트림을 종료시키고 작업을 종료한다.
- **구독자는 방출하는 이벤트를 전달받고 각 상황에 따라 액션을 취한다.**

### Subscribe

- 이벤트에 대한 처리
- 필요한 이벤트에 대해서만 골라서 처리할 수 있다.
- 이벤트에 대한 처리를 클로저 내부에서 진행한다. 따라서 약한 참조를 꼭 해서 메모리 누수를 방지해야 한다. (클로저 내부에서 self를 참조하는 경우에만 해당)
- .subscribe는 정수의 이벤트 객체를 파라미터로 하는 escaping 클로저 형식 메소드, 반환값은 Disposable이다.

### dispose

- subscribe를 취소하는 것
    - 구독을 취소하고 종료한다는 의미의 메서드
- Observable의 이벤트가 dispose()에서 종료된다.
- disposeBag : dispose에 대한 리턴 값을 담는 객체.
    - 각각의 구독에 대해 일일이 관리하는 것은 호율적이지 못하기 때문에 Disposable들을 모아놨다가 한 번에 처분하는 방식
- 모든 순서에 대한 사용 완료 후에는 dispose를 호출하여야 정상적으로 구독 취소와 동시에 메모리가 해제된다.

```swift
example(of: "DisposeBag") {
    
    let disposeBag = DisposeBag()
    
    Observable.of("A", "B", "C")
        .subscribe{
            print($0)
        }
        .disposed(by: disposeBag) // subscribe로부터 방출된 리턴 값을 disposeBag에 추가
    
    print(disposeBag)
    // prints : RxSwift.DisposeBag
}
```

### 참고

- [https://ios-development.tistory.com/97](https://ios-development.tistory.com/97)
- [https://jinshine.github.io/2019/01/02/RxSwift/2.Observable이란/](https://jinshine.github.io/2019/01/02/RxSwift/2.Observable%EC%9D%B4%EB%9E%80/)

## just

오직 **하나의 요소를 방출**시키고 **끝나는** ObservableType 프로토콜의 TypeMethod이다.

오직 하나의 **Element(요소를)** 방출 시키는 **Observable**이다.

```swift
let justStringObservable = Obse1rvable.just("hello world")
let justIntObservable = Observable.just(100)
```

## of

**여러 개의 요소들을 순차적으로 방출한다.**

![Untitled](Observable%E1%84%8B%E1%85%B5%E1%84%85%E1%85%A1%E1%86%AB%2029755755cc8d4c23bac27bfa6afe7a06/Untitled%203.png)

```swift
let ofObservable = Observable.of("hi", "my", "name", "is", "Avocado🥑")
```

of() 안에 들어가는 요소들은 **하나의 타입으로 통일**시켜야 한다.

## from

**배열로 요소를 받은 후에 하나하나 요소로서 방출해주는 연산자이다.**

![Untitled](Observable%E1%84%8B%E1%85%B5%E1%84%85%E1%85%A1%E1%86%AB%2029755755cc8d4c23bac27bfa6afe7a06/Untitled%204.png)

```swift
let fromObservable = Observable.from(["Avocado🥑", "is", "green"])
```

## range

**요소를 단순히 연속적으로 반복 방출 시킬 수 있도록 도와주는 연산자이다.**

```swift
let rangeObservable = Observable.range(start: 0, count: 10)
```

## empty

**빈 Observable을 선언(옵저버블 초기화)하거나 즉시 completed 되는 옵저버블을 리턴하고자 할 때 사용한다.**

```swift
let emptyObservable: Observable = Observable<Any>.empty()
```

## never

Observable이 **아무런 이벤트도 방출시키지 않도록** 해준다.

onDisposed 제외!

```swift
let neverObservable = Observable<Int>.never()
```

## create

create 연산자는 **내가 원하는 시점에서 이벤트를 방출 시킬 수 있게 도와주는 것**이다.

**create**으로 API를 호출하는 **Observable**을 생성하여 결과를 **onNext**를 통해서 구독자에게 결과값은 전달한다던지 응용할 수 있다.

```swift
func customObservable() **-> Observable<String>** {
    **return Observable<String>.create** { observer in
        observer.onNext("hi i'm Avocado🥑")
        observer.onCompleted()
        
        return Disposables.create()
    }
}
```

```swift
func getFriends() -> Observable<[Friend]> {
    return Observable.create { observer in
        Alamofire.request("http://friendservice.herokuapp.com/listFriends")
            .validate()
            .responseJSON { response in
                switch response.result {
                case .success:
                    guard let data = response.data else {
                        // if no error provided by alamofire return .notFound error instead.
                        // .notFound should never happen here?
                        observer.onError(response.error ?? GetFriendsFailureReason.notFound)
                        return
                    }
                    do {
                        let friends = try JSONDecoder().decode([Friend].self, from: data)
                        observer.onNext(friends)
                    } catch {
                        observer.onError(error)
                    }
                case .failure(let error):
                    if let statusCode = response.response?.statusCode,
                        let reason = GetFriendsFailureReason(rawValue: statusCode)
                    {
                        observer.onError(reason)
                    }
                    observer.onError(error)
                }
        }
 
        return Disposables.create()
    }
}
```

## deferred

**deferred**는 **Observable**이 **생성되는 시점을 구독자에 의해서 구독되기 전까지 미뤄주는 역할**을 한다.

```swift
let deferredOb = Observable<String>.deferred {
    return Observable.just("Avocado🥑")
}
```

> 무거운 작업의 **Observable**을 만들어 사용할 때에는 **deferred**를 이용해서 구독하는 시점과 동시에 작업을 시작할 수 있도록 해서 쓸데없는 낭비를 막자!
> 

> 또는 구독과 동시에 최신값이 필요한 경우 **Observable**을 **deferred**로 감싸서 사용하도록 하자!
>