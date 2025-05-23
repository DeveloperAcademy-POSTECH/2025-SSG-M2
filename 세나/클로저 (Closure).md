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
let addition = { (a, b) in a + b }
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
+ ??

## 코드 예시
+ 실제 코드 예시를 작성

## Keywords
+ 파생된 키워드들을 작성

## References
- 참고한 레퍼런스를 작성 (예 : Apple의 공식 문서)