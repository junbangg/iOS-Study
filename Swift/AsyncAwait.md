# Async Await

### 문제

1. Swift 개발에서 Closure 및 completion handlers를 사용하는 비동기 프로그래밍을 많이 한다.
2. 많은 비동기 작업 / 오류 처리 /  비동기 호출간의 제어 흐름이 복잡할 때 문제가 된다.

**[1] 많은 비동기 작업**

비동기 작업에는 deeply-nested closure가 필요하다.

```swift
func processImageData1(completionBlock: (_ result: Image) -> Void) {
    loadWebResource("dataprofile.txt") { dataResource in
        loadWebResource("imagedata.dat") { imageResource in
            decodeImage(dataResource, imageResource) { imageTmp in
                dewarpAndCleanupImage(imageTmp) { imageResult in
                    completionBlock(imageResult)
                }
            }
        }
    }
}

processImageData1 { image in
    display(image)
}
```

**[2] 오류처리**

콜백은 오류처리를 어렵고 매우 장황하게 만든다.

```swift
// (2c) Using a `switch` statement for each callback:
func processImageData2c(completionBlock: (Result<Image, Error>) -> Void) {
    loadWebResource("dataprofile.txt") { dataResourceResult in
        switch dataResourceResult {
        case .success(let dataResource):
            loadWebResource("imagedata.dat") { imageResourceResult in
                switch imageResourceResult {
                case .success(let imageResource):
                    decodeImage(dataResource, imageResource) { imageTmpResult in
                        switch imageTmpResult {
                        case .success(let imageTmp):
                            dewarpAndCleanupImage(imageTmp) { imageResult in
                                completionBlock(imageResult)
                            }
                        case .failure(let error):
                            completionBlock(.failure(error))
                        }
                    }
                case .failure(let error):
                    completionBlock(.failure(error))
                }
            }
        case .failure(let error):
            completionBlock(.failure(error))
        }
    }
}

processImageData2c { result in
    switch result {
    case .success(let image):
        display(image)
    case .failure(let error):
        display("No image today", error)
    }
}
```

### 해결

위와 같은 문제를 해결하기 위해 async-await proposal은 Swift에 코류틴 모델을 도입했다.

→ 비동기 함수의 semantics 정의, 그러나 동시성을 제공하지는 않는다.

**async-await을 사용하면?**

- 비동기 코드를 마치 동기 코드인 것처럼 작성할 수 있다. → 프로그래머가 동기 코드에서 사용할 수 있는 동일한 언어 구조를 최대한 활용 가능하다.
- 자연스럽게 코드의 의미 구조를 보존한다. → 언어에 대한 최소한 3가지 교차 개선에 필요한 정보를 제공한다.
    - 1) 비동기 코드의 성능 향상
    - 2) 코드를 디버깅, 프로파일링 및 탐색하는 동안 보다 일관된 경험을 제공하기 위한 더 나은 도구
    - 3) 작업 우선 순위 및 취소와 같은 동시성 기능을 위한 기반

### 예제

```swift
func processImageData1(completionBlock: (_ result: Image) -> Void) {
    loadWebResource("dataprofile.txt") { dataResource in
        loadWebResource("imagedata.dat") { imageResource in
            decodeImage(dataResource, imageResource) { imageTmp in
                dewarpAndCleanupImage(imageTmp) { imageResult in
                    completionBlock(imageResult)
                }
            }
        }
    }
}

processImageData1 { image in
    display(image)
}
```

→ async/await을 사용하여 리팩토링하면,

```swift
func loadWebResource(_ path: String) async throws -> Resource
func decodeImage(_ r1: Resource, _ r2: Resource) async throws -> Image
func dewarpAndCleanupImage(_ i : Image) async throws -> Image

func processImageData() async throws -> Image {
  let dataResource  = try await loadWebResource("dataprofile.txt")
  let imageResource = try await loadWebResource("imagedata.dat")
  let imageTmp      = try await decodeImage(dataResource, imageResource)
  let imageResult   = try await dewarpAndCleanupImage(imageTmp)
  return imageResult
}
```

이렇게 마치 동기적으로 일어나느 것처럼 코드를 작성할 수 있게 된다.

### 사용해보기

- completion을 return으로 바꿈

```swift
func getData() -> Data {
    let url = URL(string: "https://zeddios.tistory.com")!
    let data = try! Data(contentsOf: url)
    return data
}

func decode(data: Data) -> String {
    let contents = String(data: data, encoding: .utf8)!
    return contents
}
```

- 메소드에 async 키워드 추가

```swift
func getData() ✅ async ✅ -> Data {
    let url = URL(string: "https://zeddios.tistory.com")!
    let data = try! Data(contentsOf: url)
    return data
}

func decode(data: Data) ✅ async ✅ -> String {
    let contents = String(data: data, encoding: .utf8)!
    return contents
}
```

- 사용하는 쪽에서 await과 함께 호출

```swift
func process() async {
   let data = ✅ await ✅ self.getData()
   let contents = ✅ await ✅ self.decode(data: data)
   print(contents)
}
```

- process 호출

```swift
override func viewDidLoad() {
    super.viewDidLoad()

    Task {
        await self.process() // <!doctype html>~~~~
     }
}
```

### async

함수를 비동기 함수르 만든다.

**특징**

- 프로토콜에서도 요구 가능하다.

```swift
protocol SomeDelegate {
    func process() async
}
```

- async와 throw를 같이 쓸 수 있다.

```swift
func getData() ✅ async throws ✅ -> Data {
    let url = URL(string: "https://zeddios.tistory.com")!
    let data = try Data(contentsOf: url)
    return data
}
```

반드시 async 키워드가 throws키워드보다 먼저 선언되어야 한다.

throw awync 이런 것은 안 된다.

### await

비동기 함수 호출시 잠재적인 일시 중단 지점 지정

```swift
func process() async {
   let data = await self.getData()
   let contents = await self.decode(data: data)
   print(contents)
}
```

예를 들어 getData(), decode()를 호출하는 동안 작업이 일시 중단되어야 한다. 데이터를 다 가져올 때까지 작업을 더 진행시키면 되지 않는 상황이기 때문이다.

따라서 각 비동기 함수 호출 지점에는 잠재적인 일시 중단 지점이 있어야 하고 이것을 await으로 할 수 있다.

### 참고

- [https://zeddios.tistory.com/1230](https://zeddios.tistory.com/1230)