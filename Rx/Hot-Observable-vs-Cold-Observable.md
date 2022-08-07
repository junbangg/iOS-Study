# Hot Observable vs Cold Observable
- 둘의 차이는 `Observable`이 이벤트를 방출을 하는 시점에 대한 이야기이다.

## Hot Observable

- 생성과 동시에 방출한다.
    - `Observable.create {}`
- 해당 `Observable` 에 구독을하는 `Observer`는 이벤트 스트림 중간부터 관찰할 수 있다.
- 스트림을 분기시키는 성질을 가지고 있기 때문에 스트림의 분기가 필요한 경우 `Hot Observable`을 사용해야된다.
- 구독되어있는 구독자의 유무와 상관없이 sequence는 방출된다.
- 마우스 이벤트, 키보드 이벤트, 시스템 이벤트에 주로 사용된다.
- 멀티태스킹 포함
- ~N 개의 원소로 구성
- stateful

## Cold Observable

- 구독하는 시점부터 이벤트를 생성하여 방출
- 해당 `Observable`에 구독하는 `Observer`는 모든 이벤트를 관찰할 수 있다.
- 일반적인 웹 요청, 데이터베이스 쿼리 등이 사용되며 내가 요청하면 결과를 받는 과정을 거친다.
- 스트림을 분기시키는 성질을 가지고 있지 않다. 따라서, `Cold Observable` 을 여러번 `subscribe`하는 경우, 각각 별도의 스트림이 생성되고 할당되게 한다.
- 보통 한개의 이벤트로 구성
- stateless


# 참고
- https://github.com/ReactiveX/RxSwift/blob/main/Documentation/HotAndColdObservables.md
- https://velog.io/@okstring/Hot-vs-Cold-Observable-%EA%B0%84%EB%9E%B5%ED%95%98%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0
- https://jcsoohwancho.github.io/2019-10-20-RxSwift%EA%B8%B0%EC%B4%88-Hot-vs-Cold-Observable/