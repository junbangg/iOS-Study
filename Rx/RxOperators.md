# Rx Operators(What i frequently used)

## map

**Observable**을 통해서 방출되는 모든 요소에 대해서 중간에 계산을 하거나 타입을 변경 시켜주고 싶을 때 간편하게 처리한다.

즉, 데이터 가공이 필요할 때 `map`을 사용한다.

![Untitled](Rx%20Operators(What%20i%20frequently%20used)%20d36cdb14f33d4a5096d4a95c244c416d/Untitled.png)

```swift
observable()
    **.map{ String($0) }**
    .subscribe(onNext: { grade in
        print("\(grade)")
    }).disposed(by: disposeBag)
```

## filter

주로 Collection타입에서 사용되는 **filter,** 이름 그대로 무언가를 걸러주는 역할을 한다.

**Observable**에서 방출되는 요소를 조건에 맞게 걸러서 구독자에게 전달해주고 싶을 때 사용한다.

![Untitled](Rx%20Operators(What%20i%20frequently%20used)%20d36cdb14f33d4a5096d4a95c244c416d/Untitled%201.png)

```swift
phoneNumberObservable()**.filter { phoneNumber in
    return phoneNumber.hasPrefix("010")
}**.subscribe(onNext: { filteredNumber in
    print(filteredNumber)
}).disposed(by: disposeBag)
```

## take

**Observable**에서 방출될 요소들에 대해서 몇 개까지만 가져오겠다 할 때 사용되는 연산자이다.

**Observable 시퀀스**에서 방출되는 요소들을 `n개`로 제한하고 싶은 경우 take(count: **n**) 이런식으로 제한해서 사용한다.

```swift
observable()**.take(3)**
    .subscribe(onNext: { element in
        print("\(element)를 받았습니다.")
    }).disposed(by: disposeBag)
```

## combineLatest

**combineLatest**는 각 **Observable**에서 방출되는 요소들 중 가장 최근 요소들 끼리 겹합해서 구독자에게 전달해주는 연산자이다.

현재 방출된 요소와 다른 **Observable**에서 방출된 최종값을 비교하고 싶은일이 있다면 **combineLatest**를 사용한다.

![Untitled](Rx%20Operators(What%20i%20frequently%20used)%20d36cdb14f33d4a5096d4a95c244c416d/Untitled%202.png)

```swift
Observable**.combineLatest(first, second)**
    .subscribe(onNext: { element in
        print(element)
    }).disposed(by: disposeBag)
```

![Untitled](Rx%20Operators(What%20i%20frequently%20used)%20d36cdb14f33d4a5096d4a95c244c416d/Untitled%203.png)

```swift
class ViewController: UIViewController {
    
    @IBOutlet weak var idTextField: UITextField!
    @IBOutlet weak var passwordTextField: UITextField!
    @IBOutlet weak var loginButton: UIButton!
    
    private var disposeBag = DisposeBag()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        bind()
    }
    
    private func bind() {
        let idTextObservable = idTextField.rx.text
        let pwTextObservable = passwordTextField.rx.text
        
        Observable.combineLatest(idTextObservable, pwTextObservable, resultSelector: { !$0!.isEmpty && !$1!.isEmpty })
            .bind(onNext: { [weak self] isAllTextFieldFilled in
                self?.loginButton.isEnabled = isAllTextFieldFilled
            }).disposed(by: disposeBag)
    }
}
```

## withLatestFrom

**withLatestFrom**은 다른 Observable에서 이벤트가 방출됨에 따라 이벤트를 방출시켜주는 연산자이다.

한 **Observable**에서 요소가 방출됨에 따라서 다른 **Observable**에서 가장 최근에 방출 되었던 요소를 구독자에게 전달하고 싶을 때 사용한다.

![Untitled](Rx%20Operators(What%20i%20frequently%20used)%20d36cdb14f33d4a5096d4a95c244c416d/Untitled%204.png)

**trigger** **Observable**에서 요소가 방출될 때마다 **target** **Observable**에서 가장 최근에 방출되었던 요소를 전달해주는 것을 확인할 수 있다.

```swift
trigger**.withLatestFrom(ballType)**
    .subscribe(onNext: { element in
        print("\(element) 발사됨")
    }).disposed(by: disposeBag)
```

![Untitled](Rx%20Operators(What%20i%20frequently%20used)%20d36cdb14f33d4a5096d4a95c244c416d/Untitled%205.png)

```swift
input.tapSignIn.withLatestFrom(Observable.combineLatest(input.email, input.password))
            .bind{ [weak self] (email, password) in
                guard let self = self else { return }
                if password.count < 6 {
                    self.output.errorMessage.accept("6자리 이상 비밀번호를 입력해주세요.")
                } else {
                    Auth.auth().signIn(withEmail: email, password: password) { (user, error) in
                        if user != nil{
                            DatabaseNetwork.shared.setUid()
                            self.output.goToMain.accept(())
                        }else{
                            self.output.errorMessage.accept("로그인에 실패했습니다.")
                        }
                    }
                }
            }.disposed(by: disposeBag)
```

## throttle

throttle 연산자는 방출되는 요소를 받는 간격을 조절해주는 연산자이다.

예를 들어서 요소를 한 번 받고 이후로 3초동안은 쉬었다가 다시 요소를 받고자할 때 사용된다.

![throttle.png](Rx%20Operators(What%20i%20frequently%20used)%20d36cdb14f33d4a5096d4a95c244c416d/throttle.png)

```swift
mypageButton.rx.tap
            .throttle(.seconds(2), scheduler: MainScheduler.instance)
            .subscribe(onNext:  { [weak self] in
                let myPageVC = MyPageVC()
                myPageVC.view.backgroundColor = .white
                self?.navigationController?.pushViewController(myPageVC, animated: true)
            }).disposed(by: disposeBag)
```