프로그램의 에러 조건에서 응답하고 복구하는 프로세스

## 📌 Error 정의
Swift에서는 `Error` 프로토콜을 따르는 열거형(enum)을 사용해 에러 타입을 정의함!
```swift
enum FileError: Error {
    case fileNotFound
    case unreadable
    case unknown
}
```

## 📌 에러를 던지는 함수 (`throws`)
에러가 발생할 수 있는 함수나 메서드에 `throws` 키워드를 붙임
```swift
func readFile(named filename: String) throws -> String {
    if filename == "missing.txt" {
        throw FileError.fileNotFound
    }
    return "File content"
}
```
- `throws` : 함수가 오류를 던질 수 있음을 나타냄
- 오류가 발생하면 `throw` 키워드로 에러를 던짐
** 호출하는 쪽에서는 **`try`**를 사용해서 호출해야 함!!

## 📌 에러 처리 (`do`, `try`, `catch`)
`try`를 통해 오류 발생 가능성이 있는 코드를 실행하고 `do-catch` 블록으로 처리함
```swift
do {
    let content = try readFile(named: "missing.txt")
    print(content)
} catch FileError.fileNotFound {
    print("File not found.")
} catch FileError.unreadable {
    print("File unreadable.")
} catch {
    print("An unknown error occurred: \\(error)")
}
```
- `do` 블록 안에서 `try`를 사용해야 함
- 각 `catch` 블록은 특정 에러를 구체적으로 처리하거나 마지막 `catch`는 모든 에러를 포괄함

## 📌 에러를 무시하거나 옵셔널로 처리 (`try?`, `try!`)
### ✅ `try?`
오류 발생 시 **nil**을 반환함. 실패를 무시하거나 간단히 처리할 때 사용
```swift
let content = try? readFile(named: "missing.txt")
print(content)  // nil 출력
```

### ✅ `try!`
오류가 절대 발생하지 않는다고 확신할 때 사용함. 에러 발생 시 앱이 crash남!
```swift
let content = try! readFile(named: "myfile.txt")  // 실패 시 앱 종료
```
** crash : 앱이 실행 중에 예기치 않은 오류나 예외로 인해 강제로 종료됨

## 📌 `rethrows` 키워드
전달받은 클로저가 에러를 던질 수 있는 경우에만 에러를 던지게 하는 함수에 사용
```swift
func perform(action: () throws -> Void) rethrows {
    try action()
}
```
`throws`를 쓰면 **무조건 try가 강제**되지만, `rethrows`는 "전달된 함수가 던질 경우에만 try가 필요하다"는 **더 유연한 설계**를 가능하게 함!
그러니까 `rethrows`는
- 직접 에러를 만들고 던지진 않음
- 하지만 매개변수로 받은 클로저가 에러를 던질 수 있음
- 그럴 경우엔 그 에러를 **그대로 다시 던짐**

## 📌 에러 처리 특징 요약

| 에러 타입 | `Error` 프로토콜을 채택한 `enum`으로 정의                                                                     |
| ----- | ------------------------------------------------------------------------------------------------- |
| 에러 발생 | `throw` 키워드 사용                                                                                    |
| 에러 처리 | `do-try-catch` 구문                                                                                 |
| 간단 처리 | `try?`, `try!`                                                                                    |
| 체크 예외 | Java는 예외를 명시적으로 처리하지 않으면 컴파일 에러가 나는데 Swift는 개발자가 `try`, `try?`, `try!`, `do-catch` 등으로 유연하게 선택 가능 |

## 📌 예제
```swift
enum LoginError: Error {
    case wrongPassword
    case userNotFound
}

func login(id: String, password: String) throws {
    if id != "nyx" {
        throw LoginError.userNotFound
    }
    if password != "1234" {
        throw LoginError.wrongPassword
    }
    print("로그인 성공")
}

do {
    try login(id: "nyx", password: "wrong")
} catch LoginError.userNotFound {
    print("유저를 찾을 수 없습니다.")
} catch LoginError.wrongPassword {
    print("비밀번호가 틀렸습니다.")
} catch {
    print("알 수 없는 오류: \\(error)")
}
```


그래서 에러 처리가 문법적으로 왜 중요할까?
## ✨ 다른 언어와의 에러 처리 비교
### 🔹 1. **Swift vs Java**

| Swift                      | Java                                         |
| -------------------------- | -------------------------------------------- |
| `throws` 함수는 `try`로 호출     | `throws` 함수는 `try-catch` 또는 `throws`로 반드시 처리 |
| **체크 예외 없음**               | **체크 예외 있음** (컴파일러가 강제)                      |
| `try?`, `try!` 제공 (옵셔널 기반) | 없음                                           |
| `rethrows` 있음              | 없음                                           |
| enum으로 구체적인 에러 정의          | 클래스 상속으로 에러 정의                               |
- Java는 컴파일 수준에서 예외 처리를 강제 → **엄격하지만 코드가 장황해짐**
- Swift는 개발자에게 **선택권을 주되, 안전한 문법을 제공** → 더 깔끔하고 유연한 코드 가능

### 🔹 2. **Swift vs JavaScript**

|Swift|JavaScript|
|---|---|
|정적 타입 기반 (`throws`)|대부분 런타임에 예외 발생|
|`try`, `catch`, `throws` 명확히 있음|`try-catch`는 있지만 덜 사용됨|
|옵셔널로 에러 처리 가능|보통 `undefined` 또는 `null`|
|에러 타입을 타입으로 정의 가능|에러는 그냥 객체 또는 문자열|
-  Swift는 컴파일 타임에 **안전하게 예측** 가능하지만,
- JavaScript는 **동적이고 에러 감지 어려움**

### 🔹 3. **Swift vs Rust**

|Swift|Rust|
|---|---|
|`throws` + `do-catch` + 옵셔널 변형 (`try?`)|`Result<T, E>` 타입 기반|
|예외와 값 반환이 분리됨|모든 실패는 값으로 반환 (에러도 값임)|
|예외 처리 문법이 간결|에러 체이닝 등은 Rust가 더 강력함|
- Rust는 아예 예외(exception) 자체가 없음 → 모든 오류를 값으로 처리
- Swift는 예외 기반 모델이지만 옵셔널/Result 타입도 유연하게 혼용 가능 → **중간 지점**


**** Swift는 안전성**, **명확성**, **유연성**에 중점을 둠

|명시적 오류 처리|오류를 `throws`, `try`, `catch`로 명시|암시적 오류가 많은 언어(JavaScript 등)와 다름|
|---|---|---|
|`rethrows` 지원|고차 함수에서 오류 전달 여부에 따라 유연하게 처리|다른 언어에서는 거의 없음 (고급 기능)|
|옵셔널과 결합|`try?`, `try!`로 에러를 옵셔널처럼 다룸|오류를 값처럼 다루는 방식 (Swift만의 문법)|
|`enum` 기반 에러 정의|에러를 `enum` + `Error` 프로토콜로 선언|오류 케이스를 type-safe하게 구분|
|체크 예외 없음|Java처럼 강제하지 않고, 개발자가 선택|개발자 책임 강조 & 더 유연함|
