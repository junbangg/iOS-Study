# 클로져
주변 값들을 capture 할 수 있는 이름이 없는 클로져

## 특징들

- 1급 객체
    - 컴퓨터 프로그래밍 언어 디자인에서, 일급 객체(영어: first-class object)란 다른 객체들에 일반적으로 적용 가능한 연산을 모두 지원하는 객체를 가리킨다. 보통 함수에 인자로 넘기기, 수정하기, 변수에 대입하기와 같은 연산을 지원할 때 일급 객체라고 한다.
- 참조 타입


## 클로져 예시
```swift
func backward(_ s1: String, _ s2: String) -> Bool {
    return s1 > s2
}
var reversedNames = names.sorted(by: backward)
```
```swift
reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in
    return s1 > s2
})
```
여기서 
- 클로져의 파라미터는 `inout` 일 수는 있지만, 기본값은 갖을 수가 없다.
- variadic parameter(가변 인자) 를 사용할 수 있다.(이름을 정해야한다)
- `in` 키워드는 클로져의 선언부는 끝나고 body가 시작한다는 의미
```swift
reversedNames = names.sorted(by: { s1, s2 in return s1 > s2 } )
```
메서드의 파라미터로 클로져가 들어가기 때문에 타입은 생략이 가능하다.(스위프트가 타입 추론을 해주기 때문에)
```swift
reversedNames = names.sorted(by: { s1, s2 in s1 > s2 } )
```

```swift
reversedNames = names.sorted(by: { $0 > $1 } )
// 후행 클로져
reversedNames = names.sorted() { $0 > $1 }
// () 도 생략 가능
reversedNames = names.sorted { $0 > $1 }
```
```swift
reversedNames = names.sorted(by: >)
```
## Capturing Values
- 클로저는 특정 문맥의 상수나 변수의 값을 캡쳐할 수 있다.
- 원본 값이 사라져도 클로저의 body 안 에서 그 값을 활용할 수 있다.

```swift
func makeIncrementer(forIncrement amount: Int) -> () -> Int {
    var runningTotal = 0
    
    func incrementer() -> Int {
        runningTotal += amount
        return runningTotal
    }
    return incrementer
}

incrementByTen()
// returns a value of 10
incrementByTen()
// returns a value of 20
incrementByTen()
// returns a value of 30

let incrementBySeven = makeIncrementer(forIncrement: 7)
incrementBySeven()
// returns a value of 7

incrementByTen()
// returns a value of 40
```
호출될때 자신만의 `runningTotal` 변수를 가지고 카운트하게 된다.

이는 함수 각각 자신만의 `runningTotal` 참조를 `capture`했기 때문이다. 

## 탈출 클로져

클로져가 함수의 인자로 전달되어 함수가 끝난 후에 호출될때 **탈출**한다고 한다.
`비동기` 작업을 수행하는 함수들이 보통 클로져를 인자로 받아서, 함수를 탈출하여 나중에 함수가 실행된 후에 호출 된다.

### 주의점
- 탈출 클로져에서 `self`를 사용할때 주의.
- 탈출 클로져는 `구조체`의 `mutable self` 에 대한 참조를 캡쳐할 수 없다.
```swift
struct SomeStruct {
    var x = 10
    mutating func doSomething() {
        someFunctionWithNonescapingClosure { x = 200 }  // Ok
        someFunctionWithEscapingClosure { x = 100 }     // Error
    }
}
```

#### 탈출 클로져에서 `self`를 사용할때 주의해야되는 이유
클래스 인스턴스가 프로퍼티로 클로져를 갖고있으면 클로져가 인스턴스를 캡쳐하면서 클로져와 인스턴스간의 강한 참조가 생긴다.
그래서 스위프트는 `Capture List`를 이용해서 강한 참조를 해결할 수 있다.

# 궁금한 점
- 스위프트는 타입 추측을 어떻게 할까?

# 참고 자료
https://docs.swift.org/swift-book/LanguageGuide/Closures.html
https://ko.wikipedia.org/wiki/%EC%9D%BC%EA%B8%89_%EA%B0%9D%EC%B2%B4
https://seolhee2750.tistory.com/117
