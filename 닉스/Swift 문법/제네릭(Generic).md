### **Generic이란?**

**타입에 의존하지 않는 범용 코드를 작성할 때 사용제네릭을 사용하면 중복을 피하고, 코드를 유연하게 작성할 수 있다**

Swift 표준 라이브러리의 대다수는 제네릭으로 선언되어 있다 ex) Array, Dictoinary = 제네릭 타입

### **1-1. 제네릭 함수(Generic Function)**

만약 인자로 오는 두 Int 타입의 값을 swap하는 함수를 만든다면

```swift
func swapTwoInts(_ a: inout Int, _ b: inout Int) {
   let tempA = a
   a = b
   b = tempA
}

```

위 같은 경우, **파라미터 모두 Int형일 경우엔 문제없음!** 근데 만약 **파라미터 타입이 Double, String일 경우엔 사용할 수 없음**

만약 Double, String에 대해서 swap 함수를 사용하고 싶다면

```swift
func swapTwoDoubles(_ a: inout Double, _ b: inout Double) {
   let tempA = a
   a = b
   b = tempA
}

func swapTwoStrings(_ a: inout String, _ b: inout String) {
   let tempA = a
   a = b
   b = tempA
}

```

이렇게 하나하나 해당 형식에 맞게끔 함수를 오버로딩할 수도 있음

- 오버로딩(Overloading) : _**메서드의 이름은 같고 매개변수의 유형과 개수가 다르도록 하는 것**_

**이럴 때 사용하는 것이 바로 제네릭**

타입에 제한을 두지 않는 코드를 사용하고 싶을 때

```swift
func swapTwoValues<T>(_ a: inout T, _ b: inout T) {
   let tempA = a
   a = b
   b = tempA
}

```

꺽쇠< >를 이용해서 안에 타입처럼 사용할 이름(T)를 선언해주면, 그 뒤로 해당 이름(T)를 타입처럼 사용할 수 있음!!! 여기서 이 **T**를 **Type Parameter**라고 부르는데 **T라는 새로운 형식이 생성되는 것이 아니라**, **T는 실제 함수가 호출될 때 해당 매개변수의 타입으로 대체되는 Placeholder**

함수 이름 뒤에 꺽쇠(<>)로 T를 감싸는 이유 : T는 새로운 형식이 아니라 Placeholder이기 때문에, **Swift한테 T는 새로운 타입이 아니고, 실제 이 타입이 존재하는지 찾지말고 그냥 자리 타입임** ……라고 말해주기 위해

따라서 이렇게 `swapTwoValues`라는 함수를 제네릭으로 선언 해주면,

```swift
var someInt = 1
var aotherInt = 2
swapTwoValues(&someInt,  &aotherInt)          // 함수 호출 시 T는 Int 타입으로 결정됨

var someString = "Hi"
var aotherString = "Bye"
swapTwoValues(&someString, &aotherString)     // 함수 호출 시 T는 String 타입으로 결정됨

```

이렇게 **실제 함수를 호출할 때 Type Parameter인 T의 타입이 결정**됨

- `inout` 과 `&`
    
    `inout`
    
    : Swift에서 `inout`은 함수가 외부 변수의 값을 직접 변경할 수 있도록 해주는 기능
    
    보통 함수에 값을 넣으면 복사본이 전달되지만, `inout`을 사용하면 원본이 전달됨
    
    일반 파라미터(값복사)
    
    ```swift
    func addOne(_ number: Int) {
        var number = number
        number += 1
        print("함수 안: \\\\\\\\(number)")
    }
    
    var myNumber = 10
    addOne(myNumber)
    print("함수 밖: \\\\\\\\(myNumber)")  // 10
    
    ```
    
    - `myNumber`는 그대로 10
    - 함수는 복사본만 바꾼 것
    
    `inout` 사용
    
    ```swift
    func addOne(_ number: inout Int) {
        number += 1
    }
    
    var myNumber = 10
    addOne(&myNumber)
    print(myNumber)  // 11 ✅ 값이 바뀜!
    
    ```
    
    - `inout` 덕분에 함수가 **myNumber 자체를 바꿈**
    - `&myNumber`: 원본을 넘겨줌
    - *** 값 타입(`Int`, `Struct` 등)은 기본적으로 **복사**되기 때문에, 값 자체를 **함수 안에서 바꾸고 싶을 때** `inout`이 필요함
    
    `&` : Swift에서 `&`는 **inout 파라미터에 값을 전달할 때 붙이는 접두사**
    
    호출할 때 `&myNumber` 처럼 **&를 붙여서 원본을 넘김**
    

여기서 파라미터 a, b 모두 같은 타입 파라미터인 T로 선언되어 있기 때문에

```swift
swapTwoValues(&someInt, &aotherString)
// Cannot convert value of type 'String' to expected argument type 'Int'
// Swift에서 **정수(Int)**가 필요할 자리에 **문자열(String)**을 전달했을 때 발생하는 에러

```

만약 서로 다른 타입을 파라미터로 전달하면, 첫 번째 someInt를 통해 타입파라미터 T가 Int로 결정됐기 때문에, 두 번째 파라미터인 therStrings의 타입이 Int가 아니라며 에러가 남 똑같은 내용의 함수를 오버로딩 할 필요 없이 제네릭을 사용하면 됨

- **따라서 코드 중복을 피하고 유연하게 코드를 짤 수 있다**

+) 타입 파라미터 이름을 선언할 땐 보통 가독성을 위해 **T**나 **V** 같은 단일 문자, 혹은 **Upper Camel Case**를 사용함jj