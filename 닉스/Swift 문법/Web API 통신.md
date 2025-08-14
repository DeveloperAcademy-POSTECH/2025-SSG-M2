> 서버(백엔드)와 데이터를 주고받는 과정 주로 **HTTP 요청(REST, GraphQL 등)** 을 보내고 JSON, XML 같은 형식의 응답을 받음 Swift에서는 **`URLSession`** 이 기본 네트워크 라이브러리

## Swift의 Web API 통신 기본 흐름
다른 언어에서도 개념은 비슷한데 Swift는 **비동기 처리 방식**과 **타입 안전성**이 강조됨

1. **URL 만들기**
    ```swift
    guard let url = URL(string: "<https://api.example.com/data>") else { return }
    ```
    
2. **요청 객체 만들기** (옵션)
    ```swift
    var request = URLRequest(url: url)
    request.httpMethod = "GET" // POST, PUT, DELETE 가능
    request.addValue("application/json", forHTTPHeaderField: "Content-Type")
    ```
    
3. **네트워크 요청 보내기**
    ```swift
    URLSession.shared.dataTask(with: request) { data, response, error in
        if let error = error {
            print("Error: \\(error)")
            return
        }
    
        guard let data = data else { return }
    
        // JSON 파싱
        do {
            let json = try JSONSerialization.jsonObject(with: data, options: [])
            print("Response JSON:", json)
        } catch {
            print("JSON parse error:", error)
        }
    }.resume()
    ```
    

## Swift와 다른 언어의 차이점

|특징|Swift|JavaScript|Python|
|---|---|---|---|
|**네트워크 라이브러리**|`URLSession` 내장 (Foundation)|`fetch`, `axios` 등|`requests`, `httpx` 등|
|**비동기 처리**|`completion handler`, `async/await` (iOS 15+)|`async/await` or `.then()`|`async/await` or 동기|
|**타입 안전성**|응답 데이터는 꼭 `Codable` 구조체로 변환 가능|동적 타입이라 자유롭게 파싱|동적 타입, 데이터 구조 유연|
|**모바일 최적화**|iOS SDK와 밀접하게 통합|브라우저/Node.js 기반|플랫폼 독립적|
|**메모리 관리**|ARC(Automatic Reference Counting)로 자동|가비지 컬렉션|가비지 컬렉션|

** 가비지 컬렉션 : 컴퓨터 프로그래밍에서 동적으로 할당된 메모리 영역 중 더 이상 필요하지 않은 영역을 자동으로 해제하여 메모리 관리를 효율적으로 수행하는 기법

### 1. 네트워크 라이브러리

|언어|설명|
|---|---|
|**Swift**|iOS와 macOS에 포함된 **Foundation 프레임워크** 안에 `URLSession`이 기본 제공된다. 별도 설치 없이 바로 쓸 수 있고, Apple 환경에 최적화돼 있음|
|**JavaScript**|브라우저에는 **`fetch()`** API가 내장돼 있고, Node.js 같은 서버 환경에서는 `axios` 같은 라이브러리를 추가로 설치해서 사용|
|**Python**|표준 라이브러리에는 단순한 `urllib`가 있지만, 실제 개발에서는 **`requests`** 라이브러리를 많이 씀 (간단하고 직관적)|

** Swift는 네트워크 기능이 OS에 내장돼 있고, JS/Python은 환경에 따라 라이브러리를 골라 써야 함

### 2. 비동기 처리 방식
- **비동기 처리**: 요청을 보낸 뒤, 서버 응답을 기다리는 동안 다른 작업을 진행하는 방식.
- 네트워크 요청은 항상 비동기여야 앱이 멈추지 않음.

|**Swift**|전통적으로 **completion handler**(콜백 함수) 사용 → iOS 15부터 `async/await` 지원|
|---|---|
|**JavaScript**|`async/await`가 표준, 과거에는 `.then()`(Promise 체인) 사용|
|**Python**|원래는 동기 방식(`requests`)이 많았지만, **`asyncio`**나 **`httpx`**로 `async/await` 가능|

** Swift와 JS는 현대적인 비동기 패턴을 지원하지만 Swift는 iOS 15 이전엔 콜백 위주였음
** Python은 기본이 동기지만 최근 비동기도 지원

### 3. 타입 안전성
- **타입 안전성**: 변수나 함수의 데이터 타입을 컴파일 시점에 엄격히 검사하는 것.

|**Swift**|응답 데이터를 `Codable` 구조체에 담아야 함. 구조체 필드와 JSON 키가 다르면 컴파일/런타임에서 에러 발생.|
|---|---|
|**JavaScript**|동적 타입이라서 데이터 구조가 달라도 바로 에러 안 나고, 런타임에만 문제가 드러남.|
|**Python**|동적 타입. 구조가 달라도 실행은 되지만, 잘못된 키를 쓰면 런타임에서 KeyError 발생.|

** Swift는 데이터가 설계와 다르면 **빨리** 잡아줌
** JS/Python은 유연하지만 버그가 늦게 발견될 수 있음

### 4. 모바일 최적화

|**Swift**|iOS SDK와 완전히 통합돼 있어서 네트워크 요청 후 바로 UI 업데이트 가능 (`DispatchQueue.main.async`)|
|---|---|
|**JavaScript**|주로 웹 브라우저 UI와 연결 (DOM 조작), 모바일 앱에서는 React Native 같은 프레임워크를 거쳐야 함|
|**Python**|모바일 환경에서는 거의 안 쓰이고, 서버·데이터 처리 위주|

** Swift는 네이티브 앱에서 바로 화면 반영 가능, JS는 웹·하이브리드 앱에서 주로 사용, Python은 서버 측에 강점 있음

### 5. 메모리 관리

|**Swift**|**ARC(Automatic Reference Counting)**: 객체의 참조 횟수를 추적해 0이 되면 즉시 메모리 해제. 예측 가능하고 즉시 해제됨|
|---|---|
|**JavaScript**|**Garbage Collection**: 불필요한 객체를 찾아서 주기적으로 해제. 정확한 시점은 개발자가 제어 불가|
|**Python**|**Garbage Collection + 참조 카운팅 혼합**: 대부분 참조 카운팅으로 해제하지만, 순환 참조는 GC가 처리|

** Swift는 메모리 해제 시점이 명확하고 빠름, JS/Python은 해제 시점이 자동이지만 예측이 어렵고 약간 지연될 수 있음

💡 결론
- **Swift** → 타입 안정성, 성능, 모바일 연동에 강점.
- **JavaScript** → 웹 기반 통신에 최적, 유연하지만 타입 안정성 낮음.
- **Python** → 서버/데이터 분석 중심, 간단한 문법으로 빠르게 구현 가능.

## Swift의 장점
- **Codable**을 사용해 JSON → 구조체 자동 변환 가능
- **강타입**이라 응답 데이터 형태가 잘못되면 바로 에러로 잡힘
- iOS 앱 개발에 바로 연결 가능 (백그라운드 작업, UI 업데이트 등)

Codable 사용 예시
```swift
struct User: Codable {
    let id: Int
    let name: String
}

func fetchUser() async throws -> User {
    let url = URL(string: "<https://api.example.com/user>")!
    let (data, _) = try await URLSession.shared.data(from: url)
    return try JSONDecoder().decode(User.self, from: data)
}
```
여기서 `fetchUser()`는 JavaScript의 `async function`과 비슷하지만, Swift는 **응답 타입이 명확하게 지정됨** (`-> User`)

## 다른 언어 대비 차이
- Swift는 **모바일 앱 내부에서** API 호출과 UI 반영이 밀접하게 연결됨
- JavaScript/Python은 주로 웹 서버나 브라우저 환경에서 동작
- Swift는 URLSession + Codable 조합이 매우 일반적