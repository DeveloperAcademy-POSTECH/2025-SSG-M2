>[!question]
>GQ1. 고차 함수란?
>GQ2. 종류들은?

## Description
- ## 다른 함수를 인자로 받거나, 함수실행의 결과를 함수로 반환하는 함수
   = 함수를 값처럼 다룰 수 있다..! 함수를 다루는 함수

+ 고차함수 종류
	+ **map**
	+ **filter**
	+ **reduce**
	+ compactMap
	+ forEach
	+ flatMap

## 주요 기능
+ 함수를 인자로 받는 함수
``` swift
func greet(action: (String) -> Void) { 
	action("세연") // -> "세연"이라는 문자열을 클로저에 전달
} 

// 함수 전달하기 
greet { name in 
	print("안녕하세요, \(name)님!") 
}
```
		greet 함수는 action이라는 클로저(함수)를 인자로 받음
		action: (String) -> Void
		action은 String을 받아서 아무것도 반환하지 않는 함수 (-> Void)

+ 함수를 결과로 반환하는 함수
``` swift
func makeMultiplier(factor: Int) -> (Int) -> Int {
    return { number in
        return number * factor
    }
}

let multiplyBy2 = makeMultiplier(factor: 2)
print(multiplyBy2(5))
// multiplyBy2(5) → 5 * 2 → 10
// 결과: 10
```
		makeMultiplier(factor:) 함수는 어떤 숫자를 받아서,
		곱하기 factor를 해주는 함수를 만들어서 반환하는 함수
		(Int) -> Int 형태의 함수를 반환
		매개변수로 number를 받아서
		number * factor를 리턴하는 클로저를 생성하고 돌려줌
```swift
	func makeAdder(base: Int) -> (Int) -> Int {
	    return { value in
	        return base + value
	    }
	}
	
	let add5 = makeAdder(base: 5)
	print(add5(3))  
	// add5(3) → 5 + 3 = 8
	// 8
```

+ 컨테이너란? - 여러 값을 담을 수 있는 자료 구조
	+ Array - 배열
	+ Set - 집합
	+ Dictionary - 사전
	+ Optional - 하나의 값이 있을 수도 있고 없을 수도 있는 타입

+ map (데이터를 변형할 때 사용하는 함수 - 변형)
	+ 기존 컨테이너 요소를 매개변수로 받아, 변환한 값을 새로운 컨테이너에 담아 반환(각 요소에 변형을 가해 새로운 배열로 반환(만들어주는 함수))
	``` swift
	func map<T>(
		_ transform: (Element) throws -> T
	) rethrows -> [T]
	```
	+ Transform(변형): 기존 배열
	+ rethrows를 통해 변환된 클로저를 새로운 컨테이너에 담아 반환
	+ 변형할 방법은 함수나 클로저로 전달
	+ **원본 배열은 변하지 않고,** 새로운 배열을 반환한다.
	
	```swift
	let numbers = [1, 2, 3, 4]
	let squared = numbers.map { $0 * $0 }  
	// [1, 4, 9, 16]
	```
	- $0은 클로저의 첫 번째 매개변수, 즉 배열의 각 요소.
	- 각 요소를 제곱해서 새 배열을 만든다 (즈, 새 배열이 squared)
	
	```swift
	let names = ["seyeon", "yuna", "sojung"]
	let uppercased = names.map { $0.uppercased() } 
	print(uppercased)
	//  ["SEYEON", "YUNA", "SOJUNG"]
	```
	
	+ for-in과의 차이
		+ 공통점: 둘 다컬렉션의 각 요소에 접근해서 무언가를 함
		+ for-in: 작업 **실행**이 목적, 반환 값 없음 (void), 반복하면서 뭔가 ‘하기 위해’ 씀, 직접 배열을 만들어야 함
		+ map: 값 변형 후 **새로운 배열** 반환, 변형된 새 값을 ‘얻기 위해’ 씀
		+ map이 코드가 더 간결하고 선언적으로 작성할 수 있음.
	```swift
	// for-in
	
	let numbers = [1, 2, 3]
	var doubled: [Int] = []
	
	for num in numbers {
	    doubled.append(num * 2)
	}
	
	print(doubled)  
	// [2, 4, 6]
	```
	
	```swift
	// map
	
	let numbers: [Int] = [1, 2, 3]
	let doubled: [Int] = numbers.map { (number: Int) -> Int in
		return number * 2 
	}
	
	print(doubled)  
	// [2, 4, 6]
	```
		한번더 축약 하면?(클로저)
	```swift
	let numbers = [1, 2, 3]
	let doubled = numbers.map { $0 * 2 }
	
	print(doubled)  
	// [2, 4, 6]
	```
	

+ filter (데이터를 필터링할 때 사용하는 함수 - 조건 필터링)
	+ 기존의 컨테이너의 전체 요소를 매개변수로 받아, 필터링 한 값을 새로운 컨테이너에 담아 반환 (조건에 맞는 요소만 걸러 새로운 배열로 반환)
	```swift
	func filter(
	_ isIncluded: (Self.Element) throws -> Bool
	) rethrows -> [Self.Element]
	```
	+ isIncluded: 각 요소를 받아 true 일 때만 결과 배열에 포함되게 하는 조건 함수
	+ returns: 조건을 통과한 요소들만 모은 새로운 [Element] 배열
	
	+ map 과 차이점?
		+ 조건을 통해 기존 컨테이너를 재활용한다는 점에서 활용방안에 대한 차이점
		+ 변형이 아닌 **선별** 을 함.
	
	```swift
	let numbers = [1, 2, 3, 4, 5]
	let evens = numbers.filter { $0 % 2 == 0 }
	// 결과: [2, 4]
	```


+ reduce(데이터를 결합할 때 사용하는 함수 - 누적 계산)
	+ 컨테이너 내부의 요소를 매개변수로 받아, 하나로 통합하여 반환 (모든 요소를 하나의 값으로 합침)
	```swift
	func reduce<Result>(
	_ initialResult: Result, _ nextPartialResult: (Result, Element) throws -> Result
	) rethrows -> Result
	
	// reduce(초기값) { 누적값, 현재값 in ... }
	```
	+ initialResult: 초기값, 누적 시간 값
	+ nextPartialResutl: 누적 계산을 실행할 함수, 매 반복마다 호출됨
	
	+ map, filter와의 차이점
		reduce는 **결함** 이라는 특징
	```swift
	let numbers = [1, 2, 3, 4]
	let sum = numbers.reduce(0) { $0 + $1 }
	print(sum)
	// 10
	```
	+ 실행 해석
		1. 초기값: 0
		2. 첫 반복: 0 + 1 = 1
		3.  첫 반복: 0 + 1 = 1
		4. 두 번째: 1 + 2 = 3
		5.  세 번째: 3 + 3 = 6
		6. 네 번째: 6 + 4 = 10
		7. 최종 결과: 10
	
	```swift
	let words = ["Swift", "는", "재미있다"]
	let sentence = words.reduce("") { $0 + " " + $1 }
	print(sentence)
	// " Swift 는 재미있다"

	```   
## 코드 예시
+ 실제 코드 예시를 작성

## Keywords
+ 파생된 키워드들을 작성

## References
- 참고한 레퍼런스를 작성 (예 : Apple의 공식 문서)