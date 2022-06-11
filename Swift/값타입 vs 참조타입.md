# 값타입 vs 참조타입

 <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FyFmc4%2Fbtq6BDkMW0Q%2F0nIwkqsZwdJgBNjLu1pjh0%2Fimg.png" width="700" height="500">


## 값 타입

- 변수를 할당하면 **실제 데이터가 스택(Stack) 영역에 값이 저장**된다.
- **값을 전달 시 실제 데이터가 복사**된다.
- 변수를 복사한 후 **복사본을 변경하더라도 원본에 영향을 주지 않는다.**
- 힙 영역을 사용하지 않고 참조 카운팅이 필요하지 않다.



**값타입을 가지는 자료구조**

구조체(Struct)는 값 타입이며, 상속이 불가능하고, 생성자를 구현하지 않아도 디폴트 이니셜라이저를 사용가능하다.

Swift의 Enum, Array, String, Dictionary, Set, Tuple, 기본 데이터타입들(Int, Double 등)도 Struct와 동일한 값 타입이다.

<br/>

## 참조타입

- **스택 영역에는 포인터(힙 영역 메모리 주소)만 저장되고, 실제 데이터는 힙 영역에서 저장**된다.
    
    → 값의 전달은 인스턴스가 위치한 실제 주소(= 힙영역의 주소의 복사)
    
- 변수를 복사하더라도 하나의 값을 가리키고 있기 때문에 **복사본과 원본이 모두 같은 값**을 가진다.
- 변수를 **복사하더라도 레퍼런스 카운트만 +1이 되고 실제 값이 복사되지는 않는다**.

<br/>

**참조타입을 가지는 자료구조**

클래스(Class)는 참조 타입이며, 생성자를 구현해야만 사용가능하다.

Swift의 함수, 클로저는 클래스와 동일한 참조 타입이다.

<br/>
<br/>


***let 키워드**

let 키워드로 선언된 인스턴스는 **인스턴스에 할당된 stack 영역의 메모리 공간을 변경하지 못하게 한다**. 따라서 **stack 영역에 실제 데이터가 저장되는 값 타입 인스턴스들은 속성을 수정하지 못 한다**. 하지만 **stack 영역에 heap 영역의 메모리 주소가 저장되는 참조 타입 인스턴스들은 가리키는 대상만 수정하지 못할 뿐 속성을 수정할 수 있다.**

<br/>

***메모리 구조**

<img src="https://devmjun.github.io/img/posts/ClassVSStruct-5.jpg" width="700" height="500"> 

<img src="https://devmjun.github.io/img/posts/ClassVSStruct-8.jpg" >


***어떤 데이터를 사용해야 되나요?**

많은 Swift의 API들은 class에 기반하고 있기 때문에 **custom 데이터 타입을 만들 때 class를 사용**해야 하는 경우가 빈번하다. 하지만, 그 이외의 상황, **특히 multi threads 환경과 같은 곳에서는 mutable한 속성이 잘못된 결과를 쉽게 야기할 수 있기 때문에 struct를 사용할 것을 권장**한다.(기본 데이터 타입인 Array, String, Dictionary 등도 모두 value type에 속한다.) Struct를 통해 동일한 값을 **복사**하는 것은 constant time 수준만을 필요로 하고, **안전하게 데이터를 전달할 수 있다**.

---

### 참고

- [https://local-dev.tistory.com/entry/Swift-Class-Struct클래스와-구조체](https://local-dev.tistory.com/entry/Swift-Class-Struct%ED%81%B4%EB%9E%98%EC%8A%A4%EC%99%80-%EA%B5%AC%EC%A1%B0%EC%B2%B4)
- [https://woozzang.tistory.com/29](https://woozzang.tistory.com/29)
- [https://devmjun.github.io/archive/Swift-StructVSClass](https://devmjun.github.io/archive/Swift-StructVSClass)
- [https://hcn1519.github.io/articles/2017-02/swift_structAndClass](https://hcn1519.github.io/articles/2017-02/swift_structAndClass)
