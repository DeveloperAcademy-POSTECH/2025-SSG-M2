**정의** : 익명함수, 함수인데 함수명을 쓰지 않는 함수

**함수 선언**

```swift
func funcExample() {
	print("Hello World")
}
// 기본적으로 함수를 선언하기 위해서는 func 를 써줘야함

```

**클로저 선언**

```swift
{(parameters)->Return Type in
	print("Hello World")
}

```

(parameters)->Return Type : closure head print("Hello World") : closure body in : closure head와 closure body를 구분

1. parameter 와 Return Type이 둘 다 없는 클로저

```swift
let closure = {() -> () in
	print("Hello World")
}

```

1. parameter 와 Return Type이 둘 다 있는 클로저

```swift
let closure = {(name: String) -> String in
	return "Hello, \\\\(name)"
}
// 여기서 name은 Argument Label 아니고 Parameter Name

```

1. 호출할때

```swift
closure("Hello")
closure(name: "Hello") // error

```

1. 변수나 상수에 대입 가능

```swift
let closure = { () -> () in
	print("Closure")
}
let closure2 = closure

```

1. 파라미터 타입으로 클로저 전달 가능

```swift
func doing(closure: () -> ()) {
	closure()
}
// 함수를 파라미터로 전달받는 doing이라는 함수가 있을때,
// 파라미터로 함수를 넘겨줘도 되지만

doing(closure: { () -> () in
	print("Hello")
})
// 이렇게 클로저로 넘겨줘됨

```

1. 함수 반환 타입으로 클로저 사용 가능

```swift
func doing() -> () -> () {
}

// -> 실제 값을 return할 때 함수가 아닌

func doing() -> () -> () {
	return { () -> () in
		print("Hello World")
	}
}

//이렇게 클로저를 리턴 가능함!

let closure = doing()
closure()

//그리고 호출하는 곳에서 클로저를 받아 실행 가능

```

**클로저 실행**

1. 클로저가 대입된 변수나 상수로 호출

```swift
let closure = { () -> String in
	return "Hello World!"
}
closure()

```

1. 클로저를 직접 실행

```swift
// 클로저를 () 소괄호로 감싸고, 마지막에 호출 구문인 ()를 추가하기

({ () -> () in
	print("Hello World!")
})()

```

0522

1. 캡쳐(Capture) 클로저는 자신이 정의된 주변의 변수를 캡쳐해서 나중에 사용할 수 있다

```swift
func makeCounter() -> () -> Int {
    var count = 0
    return {
        count += 1
        return count
    }
}

let counter = makeCounter()
print(counter()) // 1
print(counter()) // 2

```

→ 여기서 클로저는 count 라는 지역 변수를 기억(capture)하고 유지함

1. escaping / non-escaping 클로저 비동기 함수에서 클로저가 함수 종료 후에도 실행될 수 있음

```swift
func fetch(completion: @escaping () -> Void) {
    DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
        completion()
    }
}
// completion 이라는 클로저를 파라미터로 받음
// () -> Void → 입력 값이 없고, 반환값도 없는 함수라는 뜻
// 클로저가 DispatchQueue.main.asyncAfter(...) 안에서 1초 후에 실행되기 때문에,
// fetch() 함수는 이미 종료되었을 수도 있음.
// 그래서 클로저가 함수 바깥까지 살아남을 수 있도록 @escaping을 붙여야함!

// DispatchQueue.main -> 메인 스레드에서 작업을 하겠다는 뜻
// UI 업데이트나 사용자 인터페이스 관련 작업은 반드시 메인 스레드에서 실행해야함

```

`fetch()` 함수가 호출됨 → 함수는 클로저(`completion`)를 매개변수로 받음 → 1초 뒤, 메인 스레드에서 `completion()`을 실행하도록 예약함 → `fetch()` 함수는 이미 리턴되었을 수 있지만, 클로저는 살아있다가 1초 후에 실행됨 → 그래서 `@escaping`이 필요함

- - 동기 / 비동기 예시

동기 - 실시간 대화, 음성/영상 통화, 채팅 등 즉각적인 상호 작용이 가능한 서비스 비동기 - 게시판, 블로그, 이메일 등 사용자 간의 상호 작용이 느린 서비스