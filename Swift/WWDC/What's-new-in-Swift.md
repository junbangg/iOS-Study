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

