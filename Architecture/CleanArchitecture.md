# Clean 아키텍처

# Clean Architecture

Robert C Martin(Uncle Bob)가 제안한 아키텍처로, 아래사진처럼 앱 아키텍쳐를 구성한 것을 Clean Architecture라고 한다.

![Untitled](Clean%20%E1%84%8B%E1%85%A1%E1%84%8F%E1%85%B5%E1%84%90%E1%85%A6%E1%86%A8%E1%84%8E%E1%85%A5%202f81406ab00b420fb567a79612ceb369/Untitled.png)

### 1) 원들의 관계

- 가장 **바깥 쪽의 원**은 **저수준의 구체적인 상세정보**를 담는다.
- 소스 코드는 **안쪽을 향해서만 의존할 수 있다**.
- **안쪽의 원은 바깥 쪽의 원에 대해 전혀 알지 못한다**. 특히, 바깥쪽의 원에서 선언된 어떠한 이름을 안쪽 원에서 참조해서는 안 된다.
- 바깥쪽 원의 어떠한 것도 안쪽의 원에 영향을 줘선 안 된다.

### 2) 원들에 대한 설명

안쪽 원부터 설명

1. **Entities (- Enterprise Business Rules)**
    - **비즈니스 모델**
    - 엔티티는 메서드를 가지는 객체일 수도 있고, **데이터 구조와 함수의 집합일 수도 있다**.
2. **Use Cases (= Application Business Rules)**
    - 애플리케이션 고유 비즈니스 규칙을 포함하며 **시스템의 모든 유스케이스를 캡슐화하고 구현**
    - **엔티티로부터의 데이터 흐름을 조합**
    - 이 계층의 변경이 엔티티에 영향을 주지 않을 것을 기대하며 데이터베이스, UI 등의 변경으로부터 영향받지 않을 것도 기대한다.
    - **비즈니스 로직 실행하는 레이어**
3. **Controllers, Gateway, Presenters(= Interface Adapter)**
    - 유스케이스와 엔티티에 있어 용이한 형식으로부터 데이터베이스나 웹 등 **외부의 기능에 용이한 형식으로 데이터를 변환**한다.
    - Presentation 레이어에 **Coordinator, ViewModel, ViewController** 등이 있다.
4. **Devices, Web, UI, DB, External Interfaces (= Frameworks & Drivers)**
    - 데이터베이스나 웹 프레임워크 등 **일반적으로 프레임워크나 도구로 구성**
    - 대개 이 계층에는 **안쪽의 원과 통신할 연결 코드** 이외에 별다른 코드를 작성하지 않는다.
    - 이 레이어에는 **Network, CoreData** 등이 있다.
    

# MVVM-Clean Architecture

![Untitled](Clean%20%E1%84%8B%E1%85%A1%E1%84%8F%E1%85%B5%E1%84%90%E1%85%A6%E1%86%A8%E1%84%8E%E1%85%A5%202f81406ab00b420fb567a79612ceb369/Untitled%201.png)

![Untitled](Clean%20%E1%84%8B%E1%85%A1%E1%84%8F%E1%85%B5%E1%84%90%E1%85%A6%E1%86%A8%E1%84%8E%E1%85%A5%202f81406ab00b420fb567a79612ceb369/Untitled%202.png)

![Untitled](Clean%20%E1%84%8B%E1%85%A1%E1%84%8F%E1%85%B5%E1%84%90%E1%85%A6%E1%86%A8%E1%84%8E%E1%85%A5%202f81406ab00b420fb567a79612ceb369/Untitled%203.png)

1. **Domain Layer**
    - 클린 아키텍처 원에서 **Entities와 Use Case를 묶어서** 도메인 레이어이다.
    - 이 레이어는 **다른 레이어들에게 어떠한 영향도 받지 않는**다.
    - 다른 프로젝트에 의해 **재사용될 수 있다**.
    - **Entites(비즈니스 모델), UseCases, Repository Interfaces**들이 이 레이어에 들어가 있다.
2. **Presentation Layer**
    - 클린 아키텍처 원에서 **Presenters와 UI**를 묶어서 Presentation Layer라고 한다.
    - **UI와 ViewModel**이 이 레이어에 속한다.
    - **뷰모델은 하나 이상의 유스케이스를 실행**하기 때문에 **프레젠테이션 레이어는 도메인 레이어를 의존**한다.
3. **Data Layer**
    - 클린 아키텍처 원에서 **DB와 API**를 묶어서 데이터 레이어라고 한다.
    - **레포지토리 프로토콜에 대한 구현과 Data Sources들**이 이 레이어에 들어있다.
    - **레포지토리**는 **다른 데이터 소스(로컬 DB 또는 API)로부터 데이터를 처리**하는 책임이 있다.
    - Data 레이어는 **API 응답으로 받은 JSON 데이터를 도메인 레이어에 있는 모델로 변환하는 작업**이 들어있다. 그래서 **Data 레이어는 도메인 레이어를 의존**한다.

**총집합**

![Untitled](Clean%20%E1%84%8B%E1%85%A1%E1%84%8F%E1%85%B5%E1%84%90%E1%85%A6%E1%86%A8%E1%84%8E%E1%85%A5%202f81406ab00b420fb567a79612ceb369/Untitled%204.png)

## MVVM-Clean 아키텍처의 데이터 플로우

**Data Flow**

1. **View(UI)는 ViewModel(Presenter)의 메서드를 콜**한다.
2. **ViewModel은 UseCase를 실행**한다.
3. **UseCase는 User와 Repository로부터 데이터를 조합**한다.
4. 각각의 **Repository는 Remote Data(Network) 또는 Persistent DB Storage Source 또는 In-memory Data(Remote or Cached)로부터 데이터를 가져온다**.
5. 정보는 **다시 View(UI)로 흘러서 (Information flows back to the View(UI)) 우리는 새로운 화면**을 보게 된다.

**→ 구체적인 예시**

1. 뷰컨트롤러가 뷰코델을 가지고 있고, 뷰모델의 메소드를 콜한다.
2. 뷰모델은 유스케이스를 가지고 있고, 유스케이스의 메소드를 부른다.
3. 유스케이스는 함수 안에서 레포지토리에게 데이터 가져오라고 요청한다.
4. 레포지토리는 서버로부터 데이터를 가져와서 넘겨준다.
5. 뷰컨트롤러의 바인드 함수로 뷰모델의 데이터를 옵저빙하여 화면을 갱신한다.

### 클린 아키텍처의 핵심

클린 아키텍처의 핵심은 **내부에 있는 계층이 외부에 있는 계층을 알지 못하게 하는 것**에 있다. 즉, Domain 레이어는 Presentaion 레이어나 Data 레이어를 절대 직접 참조하지 않는다. 그래서 각 계층 간 의존성의 방향은 다음과 같다.

![Untitled](Clean%20%E1%84%8B%E1%85%A1%E1%84%8F%E1%85%B5%E1%84%90%E1%85%A6%E1%86%A8%E1%84%8E%E1%85%A5%202f81406ab00b420fb567a79612ceb369/Untitled%205.png)

### 계층을 나누어서 개발하면 좋은 이유

아키텍처는 섬세하게 계층을 분리하여 분량이 많은 앱이더라도 소스코드 전반을 쉽게 장악할 수 있고 금방 이해하고 수정할 수 있다. 무엇보다 좋은 점은, 특정 계층에 대한 수정이 다른 계층에 거의 영향을 주지 않는다는 점이다. 이 아키텍처의 핵심은 내부의 계층(도메인)이 외부의 계층(프레젠테이션, 데이터)를 알지 못하게 하는 것이다.

### 참고

- [https://eunjin3786.tistory.com/207](https://eunjin3786.tistory.com/207)
- [https://blog.coderifleman.com/2017/12/18/the-clean-architecture/](https://blog.coderifleman.com/2017/12/18/the-clean-architecture/)