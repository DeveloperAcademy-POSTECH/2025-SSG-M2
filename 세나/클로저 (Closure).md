>[!question]
>GQ1. 클로저의 기본 형태는? 
>GQ2. 클로저는 언제 활용할까?
>GQ3. 클로저의 예시 코드는?

## Description
- 함수처럼 사용할 수 있는 코드 블록
{ (<매개변수 이름>: <매개변수 타입>, ... ) -> <반환타입> in  // head
    // 클로저 표현식 코드(실행될 코드). // 
}

- 이름이 있을 수 있고 없을 수 있다.
```swift
let addition = {(a: Int, b: Int) -> Int in
	return a + b
}
```
- 매개변수 타입 생략 가능 (타입 추론이 가능하기 때문)
```swift
let addition = { (a, b) in 
	return a + b
}
```
- 클로저가 단일 표현식인 경우, return 키워드를 생략 가능
```swift
let addition = { (a, b) in 
	a + b 
}
```
- 매개변수의 이름도 생략 가능
```swift
let addition: (Int, Int) -> Int = { $0 + $1 }
```
- Ex
```swift
let result = addition(10, 5)
print(result) // 출력: 15
```


## 주요 기능
+ 함수의 매개변수(??) 파라미터?? 로 클로저 사용 가능
+ 비동기 작업에서 클로저 활용 (비동기 작업이 끝날 때 실행할 코드를 정의)
	+ 언제 비동기 사용? 결과가 도착하기 전까지 기다리지 않고 다음 코드를 실행해야 할 때 많이 사용
```swift
// 비동기 작업에서 클로저 활용 예시
func fetchData(completion: (String) -> Void) {
    // 비동기적으로 데이터를 가져온 후 클로저를 호출하여 결과 전달
    let data = "데이터가 도착했습니다!"
    completion(data)
}

// fetchData 함수 호출
fetchData { (result) in
    print(result) // "데이터가 도착했습니다!" 출력
}
```
+ 클로저의 특징
	+ 변수 또는 상수 캡처
	  클로저가 생성된 시점에서 주변 범위에 있는 변수나 상수의 값을 캡쳐하며, 나중에 해당 값을 변경하거나 참조할 수 있다. -> 원본 값이 사라져도 클로저의 안에서 그 값을 활용할 수 있다.
	+ 일급 객체 (First-Class Object)로 취급
		+ 일급 객체 특징은 ?????
+ 배열을 처리하고 변환하는 map, reduce, filter와 같은 함수들은 클로저를 인자로 받는 함수다.
	+ 클로저를 인자로 받아서 해당 배열의 각 요소에 적용함 (trailing closure(후행클로저) 형태)
```swift
let numbers = [1, 2, 3, 4, 5] 
let squaredNumbers = numbers.map { number in 
	return number * number 
} 

let numbers = [1, 2, 3, 4, 5] 
let sum = numbers.reduce(0) { result, number in 
	return result + number 
} 

let numbers = [1, 2, 3, 4, 5] 
let evenNumbers = numbers.filter { number in 
	return number % 2 == 0 
}

```
## 코드 예시
+ 클로저를 변수로 정의해서 사용하는 예 (String 타입의 클로저)
``` swift
let greeting = { (name: String) -> String in 
	return "\(name)님 환영합니다." 
} 

print(greeting("세나")) // 세나님 환영합니다.
```

+ 함수에 클로저를 함수 이름으로 전달하기
``` swift
func greeting(name: String, completion: (String) -> Void) { 
	completion(name) 
} 

func printName(name: String) -> Void { 
	print("\(name)님 환영합니다.") 
} 

greeting(name:"세나", completion: printName) // 세나님 환영합니다.
```

+ 함수에 직접 클로저를 전달하는 예 (Inline Closure)
```swift
func greeting(name: String, completion: (String) -> Void) { 
	completion(name) 
} 

greeting(name: "세나"){ (name) in 
	print("\(name)님 환영합니다.") // 세나님 환영합니다. 
}
```

## Keywords
+ 파생된 키워드들을 작성

## References

- 참고한 레퍼런스를 작성 (예 : Apple의 공식 문서)
- 