# Protocol

## 프로토콜 Protocol

**특정역할을 하기 위한 메서드, 프로퍼티, 기타 요구사항 등의 청사진**

<br/>


### 프로토콜의 사용

- **구조체, 클래스, 열거형**은 **프로토콜을 채택**해서 특정 기능을 실행하기 위한 프로토콜의 요구사항을 실제로 구현할 수 있다.
- 프로토콜은 **정의를 하고 제시를 할 뿐** 스스로 **기능을 구현하지는 않는다.** (**조건만 정의**)
- **하나의 타입으로 사용**되기 때문에 아래와 같이 타입 사용이 허용되는 모든 곳에 프로토콜을 사용할 수 있다.
    - 함수, 메서드, 이니셜라이저의 파라미터 타입 혹은 리턴 타입
    - 상수, 변수, 프로퍼티의 타입
    - 배열, 딕셔너리의 원소타입
    

<br/>

### Property Requirements

- 프로토콜에서는 프로퍼티가 저장프로퍼티인지 연산프로퍼티인지 명시하지 않는다.
- **이름과 타입 그리고 gettable, settable한지 명시**한다. (set만 사용은 불가능)
- 프로퍼티는 **항상 var로 선언**해야 한다.

```
protocol Student {
  var height: Double { get set }
  var name: String { get }
  static var schoolNumber: Int { get set }
}
```

### **Method Requirements**

: 프로토콜에서는 인스턴스 메소드와 타입 메소드를 정의할 수 있다. 하지만 **메소드 파라미터의 기본 값은 프로토콜 안에서 사용할 수 없다.**

- 메소드를 정의할 때 **함수명과 반환값을 지정**할 수 있고, **{}는 적지 않는다.**
- **mutating 키워드를 사용해 인스턴스에서 변경 가능하다는 것을 표시할 수 있다. (값 타입에서만 사용 가능)**

```
protocol Person {
  static func breathing()
  func sleeping(time: Int) -> Bool
  mutating func running()
}
```

<br/>

### **Initializer Requirements**

: 프로토콜에서는 이니셜라이저도 정의할 수 있다.

- **실패가능한 이니셜라이저도 선언할 수 있다.**
- 프로토콜에서 특정 이니셜라이저가 필요하다고 명시했기 때문에 구현할때 해당 이니셜라이저에 **required** 키워드를 붙여줘야 한다.

```
class SomeClass: SomeProtocol {
  required init(someParameter: Int) {
    // 구현부
  }
}
```

- 특정 프로토콜의 required 이니셜라이저를 구현하고, SuperClass의 이니셜라이저를 SubClass에 상속하는 경우 **SubClass의 이니셜라이저 앞에 required 키워드와 override 키워드를 붙여줘야 한다.**
- 구조체의 경우는 required가 필요 없다.

```
protocol SomeProtocol {
  init()
}
class SomeSuperClass {
  init() {
    // 구현부
  }
}
class SomeSubClass: SomeSuperClass, SomeProtocol {
  required override init() {
    // 구현부
  }
}
```

<br/>

### **Delegation(위임)**

: Delegation이란 클래스나 구조체의 **인스턴스에 특정 행위에 대한 책임을 넘기는** 디자인 패턴 중 하나이다.

- Delegation된 기능을 제공할 수 있도록 **Delegation된 책임을 캡슐화하는 프로토콜을 정의하는것으로 구현**
- Delegation은 특정 액션에 응답하거나 해당 소스의 기본 타입을 알 필요 없이 외부 소스에서 데이터를 검색하는 데 사용할 수 있다.

<br/>

### **Extension**

: **타입에 기능을 추가하기 위해 사용하는 문법**으로 기존의 타입에 **새로운 프로토콜을 채택하기 위해 Extension을 사용할 수도 있다.**

- 상속 역시 기존 타입에서 확장할 수 있지만, 새로운 클래스를 만들면서 기능을 확장한다는 차이점이 있다.
- Extensions은 그냥 기능을 덛붙힌다고 보면 된다. (**수평적 확장**)
- 기능들을 모음으로써 가독성을 높힐 수 있다.
- **프로퍼티는 연산프로퍼티만 사용할 수 있다.**

<br/>

### **프로토콜 타입 확인**

: 일반적인 타입 확인과 마찬가지로 is, as를 사용한다.

- **is :** 앞에 있는 타입이 뒤에 있는 프로토콜을 채택하고 있는지 확인 (반환타입 Bool)
- **as? :** 앞에 있는 타입이 뒤에 있는 프로토콜을 채택하고 있는 경우 해당 타입을 프로토콜 타입으로 다운케스트, 그렇지 않은 경우는 nil 반환
- **as! :** 앞에 있는 타입을 뒤에 있는 프로토콜 타입으로 다운캐스트 실패시 런타임 에러 발생

<br/>

### 클래스 전용 프로토콜의 정의

- 해당 프로토콜이 클래스타입에만 채택될 수 있도록 `class` 키워드를 추가해서 제한할 수 있다. 프로토콜의 상속 리스트의 맨 처음에 class 키워드가 위치해야한다.
- class 대신 **AnyObject** 키워드를 사용해도 된다.

<br/>

### **Optional Protocol Requirements**

: 프로토콜을 선언하면서 필수 구현이 아닌 **선택적 구현 조건을 정의**할 수 있다.

**@objc를 사용한 방법**

- **Objective-C를 사용한 방법**이다.
- **@objc** 키워드를 프로토콜 앞에 붙이고, 메소드나 프로퍼티에는 **@objc와 optional** 키워드를 붙인다.
- **@objc 프로토콜은 클래스만 채택할 수 있다.** (구조체와 열거형은 채택 불가)
- *@objc의 단점은 사용자가 정의한 타입을 사용할 수 없다는 것이다.*

```swift
@objc protocol Person {
  @objc optional var name: String {get}
  @objc optional func speak()
}
```

→ 이를 해결하기 위한 방법은 사용자가 정의한 타입이 **NSObject를 상속**받아야 하는데 이 방법은 클래스만 가능하다.

```swift
// NSObject를 상속받아야 해서 클래스만 가능
class CustomType: NSObject {
    var property: String = "특수타입"
}
```

→ **Extension 활용** : @objc의 단점을 해결하는 또다른 방법으로, 프로토콜을 정의한 후 구현하고 싶은 기능만 확장해서 이를 채택하게 하는 방법이있다.

- 해당 프로토콜을 채택하는 타입들이 기능들을 따로 구현하지 않고, **기능이 구현된 상태로 그대로 받길 원할 때**도 사용
- 이 방법을 사용하면 사용자정의 타입을 사용할 수 있다.

<br/>

## 왜 프로토콜을 사용해야 하는가?

**1. 반복되는 coding 작업을 피하기 위한 다형성의 needs**

- 서로 다른 두 객체를 공통된 무엇인가(Type)으로 grouping을 하고 싶을 때가 있다. 혹은 공통된 속성을 지닌 두 개 이상의 객체들을 효율적으로 코딩하기 위해 공통된 속성들을 별도로 분리해 놓고 객체들이 이를 상속받아 같은 속성들을 여러번 코딩하는 것을 방지하고 싶을 때도 있다. 이 경우에 1차적으로 공통 속성을 정의하는 super class를 만들어 상속하는 방법을 생각할 수 있지만 이 방법은 매우 제한적이다.

**2. 상속의 한계**

- **class 객체는 기본적으로 하나의 super class만을 상속** 받을 수 있다. 따라서 연결하고자 하는 객체가 **이미 서로 다른 super class를 상속받았다면 두 객체를 상속으로 연결하는 방법은 더이상 사용할 수 없다.**
- 다음으로 **상속은 class 객체에만 적용 가능한 다형성 기법이**다. **struct나 enum 객체는 상속을 사용할 수 없다.**
- 자전거와 자동차를 예로 들어보면, 자전거와 자동차는 교통수단이라는 공통점을 가지고 있다. 하지만 이 두 교통수단을 하나의 super class로 묶기에는 목적에 따라 다르겠지만 공통점이 매우 적다. 또한 자전거와 자동차가 이미 각각 super class를 상속했을 가능성도 있다. 이런 경우에 두 객체를 어떻게 서로 연결지을 수 있을까?
    - 만약 자동차와 자전거가 class가 아닌 struct나 enum이라면 **오로지 protocol을 통해서만 두 객체를 하나의 타입으로 만들 수 있다**.

**3. Type으로서의 활용**

- **protocol을 변수의 type이나 함수의 parameter type**으로 사용할 수 있다.
    
    ```
    protocol Transportation: class {
       func ride()
    }
    class Car {
      var somethingToride: Transportation?
    
      func goHome(how: Transportation){
                  how.ride()
        }
    }
    ```
    
- Tip: protocol을 변수의 타입으로 사용하려면 프로토콜 선언시 ': class'를 붙여주어야 한다.
- 위 Car의 somethingToride변수와 goHome메서드의 parameter에는 **Transportation이라는 protocol을 채택하여 인스턴스화한 객체들만**이 들어올 수 있다. 이처럼 protocol을 Type으로 활용할 수 있다.

**상속** : 상속받을 클래스의 모든 기능을 물려받는다.(부모의 모든 기능을 자식 클래스가 물려받는다)

**프로토콜**: 프로토콜의 선언을 통해 이 프로토콜의 규약을 준수하겠다. 객체간의 소통 규약이다.

**상속보다 프로토콜이 가진 장점**

1. 여러개의 프로토콜을 준수할 수 있기 때문에 확장성과 재활용성이 좋다.
2. 프로토콜은 `class` 타입만 가능한 상속과는 다르게 `struct`, `enum`에서도 준수할 수 있다.

<br/>

### Protocol에서는 왜 var만 되는지 설명하시오.

- Protocol에서 `{ get set }` 의 조건을 충족하는 변수는 **저장 프로퍼티(stored variable) 혹은 읽기 쓰기가 모두 가능한 연산 프로퍼티**이다. 이 경우는 상수와 읽기전용 연산 프로퍼티를 구현할 수 없다.
- 또한, 읽기전용 연산 프로퍼티의 경우 해당 프로퍼티에 접근할 때 값이 연산되므로 var 키워드를 써야만 한다.
- 따라서 상수와 읽기 전용 연산 프로퍼티를 Protocol에서 지정하기 위해서는 `{ get }`과 함께 var 키워드를 써야 한다.

<br/>

### 출처

- [https://medium.com/@jgj455/오늘의-swift-상식-protocol-f18c82571dad](https://medium.com/@jgj455/%EC%98%A4%EB%8A%98%EC%9D%98-swift-%EC%83%81%EC%8B%9D-protocol-f18c82571dad)
- [https://zeddios.tistory.com/256](https://zeddios.tistory.com/256)
- [https://eunjin3786.tistory.com/27](https://eunjin3786.tistory.com/27)
- [https://velog.io/@yongchul/왜-protocol을-사용해야-하는가](https://velog.io/@yongchul/%EC%99%9C-protocol%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%B4%EC%95%BC-%ED%95%98%EB%8A%94%EA%B0%80)
- [https://baked-corn.tistory.com/26](https://baked-corn.tistory.com/26)
- [https://lyfeoncloudnine.bitbucket.io/blog/secret.html](https://lyfeoncloudnine.bitbucket.io/blog/secret.html)
