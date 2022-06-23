# WWDC22 - Async/Await

# Concurrency in Swift

- 가독성
- 안정성

## Functions: Synchronous and asynchronous

**Synchronous: Thread is blocked, waiting for function to finish**

**Asynchronous**: Frees thread to do other things, completionHandler is used to notify end of function.

# Example: Fetching a Thumbnail

![Screen Shot 2022-06-23 at 4.42.08 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d8733034-b2db-47fe-a04b-e322fd6772b0/Screen_Shot_2022-06-23_at_4.42.08_PM.png)

여기서 각 메서드는 직전 메서드 결과에 의존한다. 즉, 직전의 결과값을 이용해서 호출된다.

![Screen Shot 2022-06-23 at 4.43.46 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5937c5c1-2cb3-4632-8334-91e405fa93eb/Screen_Shot_2022-06-23_at_4.43.46_PM.png)

얘네들은 synchronous 해도 된다. 

![Screen Shot 2022-06-23 at 4.44.23 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/344a894c-ba73-4bcf-b172-059a1746acbc/Screen_Shot_2022-06-23_at_4.44.23_PM.png)

얘네들은 asynchronous.(시간이 걸리기 때문에)

# Before Async/await

![Screen Shot 2022-06-23 at 4.47.08 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4460b8d9-82b2-4727-b88e-d49f3b9597b0/Screen_Shot_2022-06-23_at_4.47.08_PM.png)

![Screen Shot 2022-06-23 at 4.47.35 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/264b18d8-b6d1-48ea-978f-1006a0ce725e/Screen_Shot_2022-06-23_at_4.47.35_PM.png)

![Screen Shot 2022-06-23 at 4.48.44 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3e706414-3d5f-40d7-9583-8a891d00cba8/Screen_Shot_2022-06-23_at_4.48.44_PM.png)

여기서 첫번째 문제?

`return` 만 하고 `error` 를 전달하지않았다

이대로라면 `UI` 에 `spinner` 만 계속 돌게될거다

이걸 수정하면 다음과 같다

![Screen Shot 2022-06-23 at 4.59.35 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5be7d394-3007-4652-b3d5-8f1d619877f9/Screen_Shot_2022-06-23_at_4.59.35_PM.png)

근데 이건 스위프트가 잘못됐다고 알려주질 않는다, 즉 **컴파일 에러** 가 발생 하지않는다

대안으로는 `Result` 를 사용하는 것

![Screen Shot 2022-06-23 at 5.03.32 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d700aa75-d50d-4e69-824e-82b95cbf3d17/Screen_Shot_2022-06-23_at_5.03.32_PM.png)

- 코드가 근데 길어지고 지저분해진다

# After Async/await

위 코드를 `Async/await` 이용해서 바꾸면 다음과 같다

```swift
// completionHandler 대신 그냥 async 사용
// throws 는 그대로 에러를 던질 수 있다는 의미
func fetchThumbnail(for id: String) async throws -> UIImage {
	let request = thumbnailURLRequest(for: id)
	// 위에 throws로 표시되어있기 때문에 try 도 있다
	// 이전 코드 처럼 오류 체킹 후에 completionHandler 를 통해 전달하는 코드가 try 로 간소화 됨
  // 마찬가지로 위에 async가 있기 때문에 여기 await 도 있다
	let (data, response) = try await URLSesesion.shared.data(for: request)

	guard (response as? HTTPURLResponse)?.statusCode == 200 else {
		throw FetchError.badID
	}
	let maybeImage = UIIMage(data: data)
	guard let thumbnail = await maybeImage?.thumbnail else {
		throw FetchError.badImage
	}
	return thumbnail
}
```

이렇게 작성하는 장점은, 위에서는 없었던 컴파일 에러가 발생할 수 있다는 점이다.

`throws` 랑 `UIImage` 중에 하나는 무조건 리턴 되도록 스위프트가 강제할 것이다.

위 코드에서 조금 특이했던 `await maybeImage?.thumbnail` 은 다음과 같은 `extension` 으로 구현됐다.

![Screen Shot 2022-06-23 at 6.10.18 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/84e4d2eb-9ce4-4b6e-9776-47833921ce16/Screen_Shot_2022-06-23_at_6.10.18_PM.png)

- 스위프트 5.5 이후로는 프로퍼티 `getter` 도 `throw` 할 수 있다.

![Screen Shot 2022-06-23 at 6.12.20 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/24ac91a1-c67a-4de9-80b5-14ba844c1db9/Screen_Shot_2022-06-23_at_6.12.20_PM.png)

- 반복문으로도 가능

## `async` 함수가 `suspend` 되다?

![Screen Shot 2022-06-23 at 6.15.10 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8f47b81a-0700-4085-807d-9a04afe68404/Screen_Shot_2022-06-23_at_6.15.10_PM.png)

- `thumbnailURLRequest` 가 호출되면 끝날때까지 스레드를 계속 차지한다.
- 끝나면 다시 `fetchThumbnail` 이 스레드를 차지

![Screen Shot 2022-06-23 at 6.21.02 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/995742ff-a995-4012-b103-183c43e41e7b/Screen_Shot_2022-06-23_at_6.21.02_PM.png)

- 위랑 마찬가지로 함수 호출이 끝나면 `fetchThumbnail` 한테 쓰레드를 돌려주지만, `suspend` 를 통해 조금 다르게 동작할 수 있다.
- `fetchThumbnail` 에서 `data(for: request)` 를 호출하면, `suspend` 라는것을 할 수 있다. 이때, 쓰레드를 `fetchThumbnail` 을 돌려주는 것이 아니라, `system` 한테 준다. (이때 `fetchThumbnail` 도 `suspend` 된다)
- `suspend` 라는 것은 `system` 한테 제어를 넘겨줘서 어떤 작업들을 할지 결정할 수 있게 해준다.(스케쥴링?)
- 그래서 `system` 이 `data(for: request)`` 를 다시 `resume` 할 수도 있고, `suspend` 는 이후에 몇번이고 다시 될 수도 있다.
- 무조건 `async` 로 표시되면 `suspend` 되는 것은 아님

```swift
func fetchThumbnail(for id: String) async throws -> UIImage {
	let request = thumbnailURLRequest(for: id)
	let (data, response) = try await URLSesesion.shared.data(for: request) // 1.

	guard (response as? HTTPURLResponse)?.statusCode == 200 else {
		throw FetchError.badID
	}
	let maybeImage = UIIMage(data: data)
	guard let thumbnail = await maybeImage?.thumbnail else {
		throw FetchError.badImage
	}
	return thumbnail
}
```

1. `[URLSession.shared.data](http://URLSession.shared.data)` 가 호출되면, `suspend` (“ `async` 만의 특별한 방법”) 를 통해 해당 쓰레드에서의 작업을 중단한다.
    1. 즉, 쓰레드의 제어권을 시스템한테 넘겨서 해당 메서드(`data()` ) 의 작업을 스케쥴링 시킨다.
    2. 시스템이 판단해서 해당 스레드로 다른 더 중요한 일을 할 수도 있다.
    
    예시 시나리오:
    
    ![Screen Shot 2022-06-23 at 7.51.12 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a168e171-43e3-4f6a-97c2-dade25d6b899/Screen_Shot_2022-06-23_at_7.51.12_PM.png)
    
    - `data()` 가 호출된 후에 사용자 입력이 발생
    
    ![Screen Shot 2022-06-23 at 7.52.27 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3fee9825-63e2-452a-9a1c-2007cea5831b/Screen_Shot_2022-06-23_at_7.52.27_PM.png)
    
    - 그럼 일단 그게 더 중요하기 때문에 `data` 의 일은 잠시 중단
    - 그다음에 순차적으로 작업들이 완료되면 `fetchThumbnail` 메서드도 반환된다.

# `async/await` 정리

![Screen Shot 2022-06-23 at 7.56.18 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6c77b8e0-a487-4f7d-b2a4-5b36bd28d423/Screen_Shot_2022-06-23_at_7.56.18_PM.png)

# Testing async code

### Before

![Screen Shot 2022-06-23 at 7.58.02 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d542db1d-5f21-4038-9bed-f04be8595912/Screen_Shot_2022-06-23_at_7.58.02_PM.png)

### After

![Screen Shot 2022-06-23 at 7.59.33 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f289bf98-ade2-4cac-85de-0f45370a844d/Screen_Shot_2022-06-23_at_7.59.33_PM.png)

# Bridging from sync to async

### Before

![Screen Shot 2022-06-23 at 8.01.05 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/df7b500f-ee8c-4ed1-ae8f-4b18209d62f2/Screen_Shot_2022-06-23_at_8.01.05_PM.png)

# After

![Screen Shot 2022-06-23 at 9.36.50 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/dde27c8e-221f-408c-8534-40bb4114a38d/Screen_Shot_2022-06-23_at_9.36.50_PM.png)

이때 `.onAppear` 는 `UI` 관련된거라 메인 스레드..즉, 동기로 작동됨

이런 `sync` 와 `async` 를 연결해주는게 `Task` 함수

### `Task`

클로져 안에 있는 작업을 시스템으로 보내서 다음으로 사용이 가능한 스레드에서 바로 실행한다.

`DispatchQueue.global.async` 와 같은 일을 한다고 보면 된다.

이걸 사용하는 이점은, `sync` 컨텍스트 안에서 `async` 를 사용할 수 있다는 것이다.

![Screen Shot 2022-06-23 at 9.53.52 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/be1f5936-8882-4bbc-b538-b87ab2d5af4f/Screen_Shot_2022-06-23_at_9.53.52_PM.png)

# Async APIs in the SDK

## Before

![Screen Shot 2022-06-23 at 9.56.16 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/20b73700-478c-4bac-9f98-a2934e440e1a/Screen_Shot_2022-06-23_at_9.56.16_PM.png)

## After

스위프트 5.5 부터

![Screen Shot 2022-06-23 at 9.56.45 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6f4d7037-6031-4128-a0d9-d80febd4a460/Screen_Shot_2022-06-23_at_9.56.45_PM.png)

## Before

![Screen Shot 2022-06-23 at 9.58.11 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5b75f07f-67a7-4d54-95fd-c87429b9b856/Screen_Shot_2022-06-23_at_9.58.11_PM.png)

## After

### 네이밍 팁

`async` 를 사용하는 함수에서는 앞에 `get` 를 빼라 (왜냐하면 바로 `get` 하지를 못하니까)

![Screen Shot 2022-06-23 at 9.59.54 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6eb931c8-0676-4eee-825f-5b4fdacd2e53/Screen_Shot_2022-06-23_at_9.59.54_PM.png)

# Async alternatives and continuations

![Screen Shot 2022-06-23 at 10.01.06 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/12da81cb-01fd-4d14-801b-77978932f2e1/Screen_Shot_2022-06-23_at_10.01.06_PM.png)

![Screen Shot 2022-06-23 at 10.02.25 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/18101132-2eb2-4942-9dce-9a8af12a45d5/Screen_Shot_2022-06-23_at_10.02.25_PM.png)

- `getPresistentPosts` 의 결과를 기다리고있는 `persistentPosts` 한테 반환할 방법을 생각해야된다
- `getPersistentPosts` 가 호출될때 `persistentPosts` 는 현재 `suspended` 돼있다.
- 올바른 데이터를 올바른 타이밍에 `resume` 해야된다

![Screen Shot 2022-06-23 at 10.06.27 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f5a0646b-85ac-4848-b981-8f0af20272b7/Screen_Shot_2022-06-23_at_10.06.27_PM.png)

- `await` 다음에 콜이 끝나면 `resume` 이 된다.

![Screen Shot 2022-06-23 at 10.08.49 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/66920fbb-503a-4f1c-985f-4f9dfa8effc6/Screen_Shot_2022-06-23_at_10.08.49_PM.png)

![Screen Shot 2022-06-23 at 10.09.06 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c581dfc3-baf3-44d1-88c6-0ca5645fba2f/Screen_Shot_2022-06-23_at_10.09.06_PM.png)

![Screen Shot 2022-06-23 at 10.09.32 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4862682d-6764-41c4-a499-65d1020c92ab/Screen_Shot_2022-06-23_at_10.09.32_PM.png)

런타임 오류가 발생시켜준다

# Summary

- `Async/await` code is simple easy and safe
- Hundreds of new `async` APIs
- automatic async legacy API imports

# 참고할만한 링크

[https://github.com/apple/swift-evolution/blob/main/proposals/0296-async-await.md](https://github.com/apple/swift-evolution/blob/main/proposals/0296-async-await.md)
