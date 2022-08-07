# subscribe() 에서의 순환참조

`closure`에서 순환참조를 예방해야되듯이 `subscribe`에서도 마찬가지로 예방해야된다.

## 막는 방법
1. `weak` 사용

아래와 같이 `subscribe` 클로져 내부에서 일반적인 클로져를 사용할때랑 똑같이
캡쳐 리스트를 이용해서 순환참조를 예방할 수 있다.

```swift

downloadJSON(from: url)
    .subscribe { [weak self] event in
        guard let self = self else {
            return
        }
        switch event {
        case .next(let json):
            self.editView.text = json
            self.setVisibleWithAnimation(self.activityIndicator, false)
        case .completed:
            break
        case .error:
            break
        }
```


2. `onCompleted()`를 호출하면 된다.

`completed`, 또는 `error` 가 발생하면 그때 클로져는 끝나게된다.
이때, 참조 카운트도 감소된다.
그렇기 때문에 `onCompleted()` 를 호출하면 순환참조는 발생하지않는다.

```swift
do {
    let data = try Data(contentsOf: url)
    let json = String(data: data, encoding: .utf8)
    DispatchQueue.main.async {
        f.onNext(json)
        f.onCompleted()
    }
} catch {
    print(error)
}
```

3. `Observable`을 `dispose` 시킨다

`Observable`의 생명주기는 다음과 같다

1. Create

2. Subscribe -> 이거 되었을 때 동작한다. (실행된다는 의미)

3. onNext

---- 끝 ---- 옵저버블 재사용 불가하다. 새로운 subscribe 필요

4. onCompleted  / onError

5. Disposed

즉, `Disposed`가 되어 `Observable`의 생명이 끝나면 메모리가 해제된다.

```swift
@IBAction func onLoad() {
    editView.text = ""
    setVisibleWithAnimation(activityIndicator, true)
    
		let disposable = downloadJSON(from: MEMBER_LIST_URL)
    disposable.subscribe { event in
            switch event {
            case .next(let json):
                self.editView.text = json
                self.setVisibleWithAnimation(self.activityIndicator, false)
            case .completed:
                break
            case .error:
                break
            }
            
        }
		disposable.dispose()
}
```

4. `withunretained()` 

아직 많이 못봐서 잘 모르는데 사용 방법은 다음과 같은걸로 알고있다.

```swift
viewModel.data
    .subscribe{ [weak self] info in 
        guard let self = self else {
	  return
	}
        self.doSomething(with: info)
    }
    .disposed(on: disposeBag)
```
위 코드를
```swift
viewModel.data
    .withunretained(self)
    .subscribe{
        self.doSomething(with: info)
    }
    .disposed(on: disposeBag)
```
이렇게 바꿀 수 있다.

원리는 추후에 다시..
