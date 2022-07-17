# Subject란?
`Observer` 이자 `Observable`로 사용할 수 있는 객체.
- `Observer`이기 때문에 1개 이상의 `Observable`한테 `subscribe`할 수 있고
- `Observable`이기 때문에, `observe`하고 있는 이벤트들을 다시 방출하여 다른 `Observer`한테 전달 할 수 있다.

# Subject 종류는?
## PublishSubject

![Screen Shot 2022-07-17 at 9 59 00 PM](https://user-images.githubusercontent.com/33091784/179399487-7a60a7f2-22b9-4938-b614-41d21a144545.png)

`Observable`(s) -> `PublishSubject` -> `Observer`(s)
`PublishSubject`는 `Observable`(s) 에 `subscribe`한 **이후**부터 방출된 이벤트들만 `Observer`(s) 한테 전달한다.

- 생성시에 바로 이벤트를 방출하기 시작할 수 있다.
    - 그래서 `subscribe` 하기 전에 방출되는 이벤트들을 놓칠 수도 있다.
    - 이를 방지하기위해 `Cold Observable` 를 사용하면 된다.
![Screen Shot 2022-07-17 at 9 59 54 PM](https://user-images.githubusercontent.com/33091784/179399512-4228542d-7a4c-41d9-82a2-525fa91297dc.png)

- `Observable`이 오류와 함께 죽으면, `Publish Subject`는 오류를 그대로 다른 `Observer` 들한테 전달한다.


## BehaviorSubject
![Screen Shot 2022-07-17 at 9 59 21 PM](https://user-images.githubusercontent.com/33091784/179399498-23b0345e-524e-4d9c-a7d0-c9497e5675ae.png)

`BehaviorSubject`는 관찰하고있던 `Observable`이 방출한 가장 최근 이벤트부터 방출한뒤에, 다음 이벤트들을 방출한다.

![Screen Shot 2022-07-17 at 9 59 33 PM](https://user-images.githubusercontent.com/33091784/179399503-eb5afa09-3cca-4af7-906f-7af024c82545.png)

`BehaviorSubject`도 관찰하고있던 `Observable`이 오류와 함께 죽으면 이벤트는 더이상 방출 안하고, 오류를 그대로 전달한다.

## ReplaySubject
![Screen Shot 2022-07-17 at 10 00 28 PM](https://user-images.githubusercontent.com/33091784/179399533-8b71eaec-aecb-4cb8-a4c6-0e8822bf0dd9.png)

`ReplaySubject`는 `subscribe`의 시점과 상관없이 관찰하고있던 `Observable`이 방출한 모든 이벤트를 그대로 `Observer`들한테 전달한다.

- `ReplaySubject`를 `Observer`로 사용하면 다중스레드 환경에서 `onNext`는 사용하지 않는게 좋다.
    - https://reactivex.io/documentation/contract.html 이걸 위배한다고 함
    - 순차적으로 이벤트들이 발생하지 않는 부작용

## AsyncSubject
![Screen Shot 2022-07-17 at 10 00 08 PM](https://user-images.githubusercontent.com/33091784/179399516-b73f4542-a448-4209-a4ac-3f2c104c8d93.png)

`AsyncSubject` 는 관찰하던 `Observable`이 `complete`된 후에 가장 마지막 이벤트를 방출한다.(만약에 `Observable`이 이벤트를 방출하지않으면 `AsyncSubject`도 그냥 죽는다.)

`AsyncSubject`도 마찬가지로 관찰하던 `Observable`이 오류와 함께 끝나면, 그대로 오류만 전달한다.

# Observable과 Subject의 차이는?
`Observable`은 사전에 정의 해놓은 이벤트들만 방출한다.

`Subject`는 `next`를 통해서 `Observable` 이 정의된 이후에도 값을 추가해 이벤트를 방출시킬 수 있다.


# 추가 학습할 내용

## 언제 뭘쓰는게 좋을까?
http://davesexton.com/blog/post/To-Use-Subject-Or-Not-To-Use-Subject.aspx



# 참고 자료
https://reactivex.io/documentation/subject.html
