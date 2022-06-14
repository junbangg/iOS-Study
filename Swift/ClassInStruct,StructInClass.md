# 구조체 안에 클래스, 클래스 안에 구조체

## 클래스 안에 구조체의 인스턴스가 있는 경우

```swift
import Foundation
 
struct ValueType {
    var number = 2
}
 
class ReferenceType {
    var number = 1
    var structInstance = ValueType()
}
 
let classInstance = ReferenceType()
let classInstanceCopy = classInstance
 
classInstanceCopy.number = 0
classInstanceCopy.structInstance.number = 0
 
print(classInstanceCopy.number) // 0
print(classInstanceCopy.structInstance.number) // 0
print()
print(classInstance.number) // 0
print(classInstance.structInstance.number) // 0
```

**참조 값이 복사된 클래스 인스턴스의 구조체 멤버 변수를 변경하면, 기존 클래스 인스턴스의 구조체 멤버 변수도 함께 변경된**다. 그리고 number 멤버 변수도 함께 변경되었다. 이렇게 동작하는 이유는 **struct는 값 타입이지만 RefereceType 인스턴스가 힙에 포함**되어서 **참조 타입인 ReferenceType 인스턴스가 해제될 때까지 메모리에 함께 남아있게** 되기 때문이다.

즉, **참조 타입 인스턴스를 다른 변수에 할당할 때는 참조 값만 전달되기 때문에 내부의 값 타입 인스턴스는 새로 복사되지 않고 기존 인스턴스**를 따라간다. (내부의 값 인스턴스도 참조된다고 생각하면 될 듯..) 참조 타입 인스턴스를 새로 어딘가에 할당한다고 해서 새로운 인스턴스가 생성되는 건 아니기 때문이다.

<br/>


## 구조체 안에 클래스 인스턴스가 있는 경우

```swift
import Foundation
 
struct ValueType {
    var number = 2
    var classInstance = ReferenceType()
}
 
class ReferenceType {
    var number = 1
}
 
var structInstance = ValueType()
var structInstanceCopy = structInstance
 
structInstanceCopy.number = 0
structInstanceCopy.classInstance.number = 0
 
print(structInstance.number) // 2
print(structInstance.classInstance.number) // 0
print()
print(structInstanceCopy.number) // 0 
print(structInstanceCopy.classInstance.number) // 0
```

먼저, 값 타입 할당이 일어났기 때문에 **구조체 인스턴스가 새로운 변수에 복사**됩니다. 이때, 내부에 있는 **값 타입 멤버 변수는 별개의 변수로 복사**되고, **참조 타입 클래스 인스턴스는** **참조 값이 복사**된다. 따라서 두 인스턴스의 멤버 변수인 **number는 복사본에만 업데이트**되고, **클래스 인스턴스는** 참조값이 복사되었기 때문에 **양쪽 인스턴스에 모두 업데이트됩니다.**

<br/>

### 정리

- **클래스 내부에 구조체 인스턴스**가 멤버로 있을 때 → 변수에 클래스 인스턴스 할당 시 **같은 구조체 인스턴스**가 따라간다.
- **구조체 내부에 클래스 인스턴스**가 멤버로 있을 때 → 변수에 구조체 인스턴스 할당 시 **클래스 인스턴스의 참조 값**이 따라간다.

→ 클래스 내부에 구조체 인스턴스가 멤버로 있는 경우, 구조체 인스턴스도 참조 타입으로 할당되어 힙 영역에 배치되는 것으로 생각..

→ 구조체 내부에 클래스 인스턴스가 멤버로 있을 때, 구조체는 스택 영역, 클래스 인스턴스는 힙영역에 할당되어 구조체에서 클래스 인스턴스의 주소 값을 가지고 있는 형식인 것 같습니다.

<br/>


### 참고

[https://jeonyeohun.tistory.com/179](https://jeonyeohun.tistory.com/179)
