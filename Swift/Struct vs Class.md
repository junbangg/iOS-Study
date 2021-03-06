# Struct vs Class
## 차이점
### Struct
- 값 타입
- Stack에 저장
- Thread-Safe 하다
    - 왜 Thread-Safe 할까요?
- 상속이 안된다
- Copy-on-Write
- memberwise 생성자

### Class
- 참조타입
- 힙에 저장
- Thread-Safe하지 않다
- 상속이 가능하다
- deinit 이 가능하다

## 공통점
- 둘다 속성을 이용해서 값을 저장 할 수 있다
- 둘다 subscript 를 사용 할 수 있다
- 둘다 생성자가 있다 
- 둘다 확장이 가능하다 (extension)
- 둘다 프로토콜(protocol)을 사용할 수 있다
- 둘다 제네릭(generic) 을 사용할 수 있다

## 언제 뭘 사용하는게 좋을까
### Struct
- Struct를 디폴트로 사용하는것을 원칙으로
- 애플 가이드라인에서 다음 조건 중 하나 이상에 해단되면 Struct를 사용하라고 한다.
  - 연관된 간단한 값의 집합을 갭슐화 하는 것만이 목적일때
  - 캡슐화된 값이 참조되는 것보다 복사되는 것이 합당할 때
  - 구조체에 저장된 프로퍼티가 값 타입이며 참조되는 것보다 복사되는 것이 합당할 때
  - 상속이 필요없을때

### Class
- 상속이 필요할때
- objective-c 와 함께 사용이 필요할때
- 인스턴스끼리 비교나 복사가 필요 없을때..즉, Window, UIViewController 처럼 한개만 필요하고 복사가 필요 없을때.
    - UIViewController를 보통 새로 만들지, 복사를 하지않는다
- 인스턴스의 lifetime 이 외부에 의존할때. 파일이나, 데이터베이스랑 연결이 되었을때는 그걸 복사 할 필요가 없다

