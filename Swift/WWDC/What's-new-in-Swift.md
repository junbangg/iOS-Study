---
title:  "WWDC 2022 What's new in Swift" 

categories:
  - wwdc
tags:
  - [iOS, wwdc]

toc: true
toc_sticky: true

date: 2022-06-19
last_modified_at: 2022-06-19
---

# 
- Community Update
- Swift packages
- Performance Improvements
- Concurrency Updates
- Expressive Swift

# Community Update

# Swift packages
Swift to be Scalable
- 변화들
- Swift Standard Library smaller for standalone statically linked binaries.
    - Faster native implementation

TOFU
Trust on First Use
- Security Protocol
- fingerprint of a packet is recorded

Command Plugins
- Generate Documentation
- Reformat Source Code
- Generate Test Reports
- Open SOurce Tools <-> SPM

Module Disambiguation
- Allows you to rename modules from outside packages that define them
- 

# Performance Improvements

작년에 Swift Driver(program that coordinates the compilation of Swift source code)
- Integrated Compiler
- Eager Compilation
- Eager Linking

Parallelization enabled
"Demystified Parellelization in XCode Builds" session

## Type Checking Speed 향상
Generics System의 주요 부분 수정해서 Type Checking 속도 향상
Protocol, Where clauses 계산하는 부분 수정
이전 구현에서는 프로퍼티가 많아질 수록 메모리, 속도 비용이 기하급수적으로 증가

아래 코드를 `Type Check` 하는데에 변화
<img width="1147" alt="Screen Shot 2022-06-20 at 4 39 36 PM" src="https://user-images.githubusercontent.com/33091784/174549844-df6baa41-8f70-41aa-8d2f-660285c3eb3b.png">
<img width="1147" alt="Screen Shot 2022-06-20 at 4 39 12 PM" src="https://user-images.githubusercontent.com/33091784/174549770-1bcd228f-e47f-475e-a121-8e532289b2a0.png">

## Runtime Improvements

스위프트 5.7 이전에는 Protocol Checking 하는 데 4 초나 걸림
앱 런칭할때마다 `Protocol` compute 해야됨.
그래서 `Protocol` 추가될때 마다 런칭 더 오래걸림.
지금은 `Cache` 됨. 그래서 런칭 시간이 절반으로 줄어들 수도 있다.

# Concurrency Updates

Actors + Async/Await
+
- data race safety
- Distributed Actors
- Async Algorithms

## Memory Safety by Default

value 를 수정하면서 읽기가 안됨

<img width="693" alt="Screen Shot 2022-06-20 at 4 47 07 PM" src="https://user-images.githubusercontent.com/33091784/174551214-1e8ad6c7-90b0-40c1-91d1-b2bcfb9f8d96.png">

<img width="693" alt="Screen Shot 2022-06-20 at 4 47 27 PM" src="https://user-images.githubusercontent.com/33091784/174551285-9dd77420-2bba-477b-a3ae-c1093d9b7b01.png">

수정을 하면서 `count` 를
접근할 수 없게 막는다

이와 비슷한 기능을 스레드에서 하고싶다
이런 `Data Race` 를 기본으로 해결하고 싶다
즉, 동시성 버그를 막고싶다
<img width="693" alt="Screen Shot 2022-06-20 at 4 51 40 PM" src="https://user-images.githubusercontent.com/33091784/174552076-bfc76d20-cab0-4f82-b36a-3521517027b1.png">
스위프트에서 위와 같은 상황을 막는다. 동기화 없이 값 수정 못하게 막는다.
이런 문제를 `Actor` 로 해결했었음.

**distributed actor**
다른 machine 에 있는 `Actor`

"Eliminate data races using Swift Concurrency" 세션 보기
<img width="693" alt="Screen Shot 2022-06-20 at 4 53 21 PM" src="https://user-images.githubusercontent.com/33091784/174552383-396ad296-1932-49c5-bf98-83a20dafefe3.png">

### Async Algorithms Package

<img width="693" alt="Screen Shot 2022-06-20 at 4 55 34 PM" src="https://user-images.githubusercontent.com/33091784/174552840-62218d04-6ebd-4661-b5cc-45b1ede4a001.png">
<img width="693" alt="Screen Shot 2022-06-20 at 4 55 46 PM" src="https://user-images.githubusercontent.com/33091784/174552875-2825c2e1-677d-4172-94e2-3ea08e448469.png">
<img width="693" alt="Screen Shot 2022-06-20 at 4 56 12 PM" src="https://user-images.githubusercontent.com/33091784/174552951-6b275e4f-bc7e-4bb1-b019-86e01be8c020.png">
- Actor Prioritization: Actor의 우선순위 부여ㅑ
- Priority-inversion avoidation: 덜 중요한게 더 중요한 일을 막지 못하는 기능
- 
## Instruments에 Swift Concurrency 툴 추가
<img width="693" alt="Screen Shot 2022-06-20 at 4 57 02 PM" src="https://user-images.githubusercontent.com/33091784/174553083-f00ef4f3-2b6f-4eb9-a55d-5cbf93084279.png">

"Visualize and optimize Swift Concurrency"

# Expressive Swift
## Optional Unwrapping syntax 추가
<img width="693" alt="Screen Shot 2022-06-20 at 4 59 29 PM" src="https://user-images.githubusercontent.com/33091784/174553525-b0f96929-d170-4d75-9cb8-c9ee954fa7b1.png">

`if let`, `guard let`  할때 보통 같은 이름의 변수/상수 에 부여를 하는데, 이름이 너무 길어질때가 있다.
근데 이름을 수정하거나 줄이면 가독성이 저하되기 때문에 스위프트 5.7부터는 다음 syntax 제공
<img width="693" alt="Screen Shot 2022-06-20 at 5 02 26 PM" src="https://user-images.githubusercontent.com/33091784/174554099-1daa4d09-27ee-4a3a-942a-dd284cd7f843.png">

## 
`C` 와 다르게 다른 타입의 포인터 할당은 안됨

<img width="693" alt="Screen Shot 2022-06-20 at 5 03 49 PM" src="https://user-images.githubusercontent.com/33091784/174554373-450e0cb7-f473-4d1c-a080-2b3f8c63bf23.png">
<img width="693" alt="Screen Shot 2022-06-20 at 5 04 59 PM" src="https://user-images.githubusercontent.com/33091784/174554563-482b6f75-8543-4d8f-ab0c-5884d0b5628c.png">

그래서 `C` 랑 호환할때 문제가 발생할 수 있음

스위프트는 `Type Safety` 를 중요시하면서도 `C-Family Code` 접근도 중요시함

그래서 문제 해결하는데 `C++` `Interop` 추가
<img width="693" alt="Screen Shot 2022-06-20 at 5 06 44 PM" src="https://user-images.githubusercontent.com/33091784/174554919-2c5dc6a6-7a10-4c8b-a528-609e130b9dda.png">


## String Processing
<img width="693" alt="Screen Shot 2022-06-20 at 5 07 06 PM" src="https://user-images.githubusercontent.com/33091784/174554974-b6380a7a-f84d-4153-8181-b96b2abbea79.png">

문자열 조작하기 어려웠다(인정)
근데  `Regex` 도 굉장히 어려움

그래서 쉬운 `Regex` 만듬!
`SwiftUI` 처럼 가독성 좋고 선언형 `Regex` `Syntax` 제공

<img width="693" alt="Screen Shot 2022-06-20 at 5 10 21 PM" src="https://user-images.githubusercontent.com/33091784/174555600-e850fe84-2deb-476e-a798-35f87de89049.png">

<img width="454" alt="Screen Shot 2022-06-20 at 5 12 27 PM" src="https://user-images.githubusercontent.com/33091784/174556052-8b26ca61-3686-4d0c-aa22-9809f93b929c.png">

## Generic Code Clarity

<img width="454" alt="Screen Shot 2022-06-20 at 5 13 26 PM" src="https://user-images.githubusercontent.com/33091784/174556250-2b0d7f82-86b0-46fd-abc6-5bedbe10fef3.png">

<img width="673" alt="Screen Shot 2022-06-20 at 5 13 53 PM" src="https://user-images.githubusercontent.com/33091784/174556332-2990a449-4ad2-4873-83fe-b68922085ca0.png">

두가지 버전의 프로토콜 메서드인데 `Syntax` 가 같아서 `Mailmap` 의 차이를 알기 어려움



<img width="673" alt="Screen Shot 2022-06-20 at 5 15 49 PM" src="https://user-images.githubusercontent.com/33091784/174556690-e572f124-5c43-47bd-927d-fd4f9496d98e.png">

프로토콜을 다음과 같은 곳에서 사용하면
- Generic Parameter List
- Inheritance List
- Generic Conformance Constraint
- Opaque Result Type
-> **프로토콜을 채택한 인스턴스를 의미** ("Something that conforms to Mailmap)

반대로

프로토콜을 다음과 같은 곳에서 사용하면
- 변수
- Generic Argument
- Generic Same-type constraint
- Function Parameter/Result Type
-> **프로토콜에 채택하는 것들이 모인 곳** ("A box whose contents conform to Mailmap")

<img width="673" alt="Screen Shot 2022-06-20 at 5 28 47 PM" src="https://user-images.githubusercontent.com/33091784/174559229-8ec17ad2-2c94-4b8c-9d1c-a63a66c99a74.png">

스위프트 5.7부터는 `Box` 를 사용할때는 앞에 `any` 키워드를 사용해아됨

<img width="673" alt="Screen Shot 2022-06-20 at 5 29 11 PM" src="https://user-images.githubusercontent.com/33091784/174559313-1ad468f6-c55c-4e12-8ca4-6799b13213a8.png">



<img width="673" alt="Screen Shot 2022-06-20 at 5 29 42 PM" src="https://user-images.githubusercontent.com/33091784/174559431-e3d58b5f-5550-4647-aa8e-e448dc8fdf0e.png">
그럼 좀 구분이 됨

<img width="673" alt="Screen Shot 2022-06-20 at 5 36 13 PM" src="https://user-images.githubusercontent.com/33091784/174560708-16f4a64a-1af7-45e8-81de-33085f9b8d8d.png">

