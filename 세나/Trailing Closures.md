## Description
+ 함수의 마지막 인수로 함수에 클로저 표현식을 전달해야하고
  클로저의 표현식이 긴 경우 후행 클로저로 작성하는 것이 유용함
+ 
## 주요 기능
+ 
## 코드 예시
+ 인자로 클로저 하나를 받는 함수
```swift
func someFunctionThatTakesAClosure(closure: () -> Void) {
    // function body goes here
}
```

+ 일반적인 호출 방식 (클로저를 인자 자리에 명시적으로 전달)
```swift
someFunctionThatTakesAClosure(closure: {
    // closure's body goes here
})
``` 

+ 후행 클로저
```swift
someFunctionThatTakesAClosure() {
    // trailing closure's body goes here
}
```

+ Ex (인자가 클로저 하나뿐이라면?)
```swift
func sayHello(_ action: () -> Void) {
    action()
}

sayHello {
    print("Hello!")
}
```

+ Inline Closure VS Trailing Closure
```swift
// inline
func greet(completion: (String) -> Void) {
    completion("Sena")
}

greet(completion: { name in
    print("안녕하세요, \(name)님!")
})
```

```swift title:후행 error:1 hl:2-4
// trailing
greet { name in
    print("안녕하세요, \(name)님!")

}

```
## Keywords
+ 

## References
- 