-> 쉽게 말해 어떤 '무언가' 변수처럼 다룰 수 있다면 1급 객체이다

(1) 값처럼 변수에 담을 수 있거나

(2) 인자로 넘길 수 있어야 하며

(3) 리턴값으로 변환할 수 있어야

1급 객체 이다.


자 그럼 3가지 조건을 설명해 보면

1. 변수에 할당

-> 함수 자체를 변수에 넣을 수 있어야 함
``` swift
// 1. 변수에 저장

func sayHello() {
    print("Hello")
}

let helloFunc = sayHello  // 함수 자체를 변수에 담음
helloFunc()  // Hello

// 2. 함수의 인자로 전달

func runTask(task: () -> Void) {
    print("작업 시작")
    task()
    print("작업 종료")
}

runTask {
    print("✅ 내가 넘긴 작업이 실행됨")
}
작업시작
✅ 내가 넘긴 작업이 실행됨
작업종료

// 3. 함수에서 리턴
func makeGreeter() -> () -> Void {
    return {
        print("Hi from returned function!")
    }
}

let greeter = makeGreeter()
greeter()
// Hi from returned function!
```

위  처럼 간단한 예시로 설명이 가능함.


클로저와 연관 시키면

Swift에서는 함수도 일종의 클로저이고, 클로저는 곧 익명함수이기 때문에
"함수와 클로저는 서로 변환 가능한 개념" 으로 보면 됨

1. 
``` swift

func sayHello() {
    print("Hello")
}

let helloFunc = sayHello
helloFunc()
```

여기서 sayHello는 이름있는 함수이지만 
- let helloFunc = sayHello → 이 시점에서 helloFunc는 **함수를 가리키는 변수**가 됨
- helloFunc()는 변수처럼 저장된 클로저를 호출한 것과 같음


2. 인자로 전달 -> 함수를 값처럼 넘김 = 클로저

``` swift

func runTask(task: () -> Void) {
    print("작업 시작")
    task()
    print("작업 종료")
}

runTask {
    print("✅ 내가 넘긴 작업이 실행됨")
}
```

- runTask(task:) 함수는 () -> Void 형태의 **함수(=클로저)를 인자로 받음**
- 호출할 때 { print(...) }는 **익명 클로저**를 직접 전달
- 이 클로저는 task() 실행 시 호출됨

3. 함수에서 리턴 -> 클로저를 리턴값으로


``` swift
func makeGreeter() -> () -> Void {
    return {
        print("Hi from returned function!")
    }
}

let greeter = makeGreeter()
greeter()
```

- makeGreeter() 함수는 () -> Void 타입의 **클로저**를 리턴함
- 즉, 함수가 **클로저(익명 함수)를 생성하고 반환**
- greeter()는 리턴된 클로저를 호출하는 것


> 이 3가지 조건이 다 가능하다는 건 = Swift에서 함수(=클로저)가 **1급 객체**라는 증거



















간단하게 클로저랑 엮어서 이야기를 해보면

** 클로저(Closure) 는 이름이 없는 함수 **
-> 다시말해 함수를 변수처럼 다루는 방식을 제공하는 핵심 도구임

``` swift

let sayHello = {
    print("Hello!")
}

// 여기서 sayHello는 변수처럼 생겼지만, 함수로 사용 됩니다 (= 클로저)
sayHello()  // 출력: Hello!

```

자 그렇다면 예시로!

``` swift

func doSomething(action: () -> Void) {
    print("Start")
    action()    // <- 여기서 함수(클로저) 실행
    print("End")
}

doSomething {
    print("🔥 인자로 넘긴 클로저 실행됨")
}

// 여기서 action이라는 인자는 함수(클로저) 자체야!
//이게 가능한 이유는 Swift에서 함수도 '값처럼' 다룰 수 있기 때문
```

``` swift

Button(action: {
    print("버튼 눌림")
}) {
    Text("클릭")
}
// 여기서도 action은 인자로 클로저를 받는것!


```

