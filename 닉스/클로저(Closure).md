**정의**
: 익명함수, 함수인데 함수명을 쓰지 않는 함수


**함수 선언**

```
func funcExample() {
	print("Hello World")
}
// 기본적으로 함수를 선언하기 위해서는 func 를 써줘야함

```


**클로저 선언**

```
{(parameters)->Return Type in
	print("Hello World")
}
```
(parameters)->Return Type : closure head
print("Hello World") : closure body
in : closure head와 closure body를 구분


1. parameter 와 Return Type이 둘 다 없는 클로저
```
let closure = {() -> () in
	print("Hello World")
}
```

2. parameter 와 Return Type이 둘 다 있는 클로저
```
let closure = {(name: String) -> String in
	return "Hello, \(name)"
}
// 여기서 name은 Argument Label 아니고 Parameter Name
```

3. 호출할때
```
closure("Hello")
closure(name: "Hello") // error
```

4. 변수나 상수에 대입 가능
```
let closure = { () -> () in
	print("Closure")
}
let closure2 = closure
```

5. 파라미터 타입으로 클로저 전달 가능
```
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

6. 함수 반환 타입으로 클로저 사용 가능
```
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
```
let closure = { () -> String in
	return "Hello World!"
}
closure()
```

2. 클로저를 직접 실행
```
// 클로저를 () 소괄호로 감싸고, 마지막에 호출 구문인 ()를 추가하기

({ () -> () in
	print("Hello World!")
})()
```