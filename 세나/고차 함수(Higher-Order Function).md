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

+ # map (데이터를 변형할 때 사용하는 함수 - 변형)
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
	+ array.append(새로운_값)
		+ array → var로 선언(값을 변경해야 하니까)
		+ 새로운_값 → 배열 요소와 같은 타입이어야 함
		```swift
		var numbers = [1, 2, 3]
		numbers.append(4)
		// [1, 2, 3, 4]


		var names = ["세연", "유나"]
		names.append("소정")
		// ["세연", "유나", "소정"]


		var doubled: [Int] = []
		let original = [1, 2, 3]
		
		for num in original {
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
	

+ # filter (데이터를 필터링할 때 사용하는 함수 - 조건 필터링)
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


+ # reduce(데이터를 결합할 때 사용하는 함수 - 누적 계산)
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
		3. 두 번째: 1 + 2 = 3
		4.  세 번째: 3 + 3 = 6
		5. 네 번째: 6 + 4 = 10
		6. 최종 결과: 10
	
	```swift
	let words = ["Swift", "는", "재미있다"]
	let sentence = words.reduce("") { $0 + " " + $1 }
	print(sentence)
	// " Swift 는 재미있다"

	```   


+ # compactMap(nil 제거 + 변형)
	+ 컨테이너의 각 요소를 조건을 지정하여 호출할 때, nil 이 아닌 배열을 반환
	  (주로 nill을 제거하거나, 변환 과정에서 nill이 나올 수 있는 경우 필터링)
	```swift
	func compactMap<ElementOfResult>(
		_ transform: (Self.Element) throws -> ElementOfResult?
	) rethrows -> [ElementOfResult]
	```
	+ transform은 컨테이너의 요소를 매개변수로 받아 선택적 값을 반환하는 클로저
	  
	Ex.
	 ```swift
		let string = ["1", "2", "three", "4"]
		
		let numbers = strings.compactMap { Int($0) }
		// 결과: [1, 2, 4]
		```
	+ "three"는 `Int( "three" )`가 `nill`이기 때문에 제거
	  
	+ map
		```swift
		let possibleNumbers = ["1", "2", "three", "///4///", "5"]
		
		let mapped = possibleNumbers.map { str in Int(str) }
		// [Optional(1), Optional(2), nil, nil, Optional(5)]
		```
	+ compactMap
		```swift
		let possibleNumbers = ["1", "2", "three", "///4///", "5"]
		let compactMapped = possibleNumbers.compactMap { str in Int(str) }
		// [1, 2, 5]
		```
	+ map과의 차이점
		+ map을 사용했을때는 Optional 이지만,
		  compactMap을 사용하니 Int에 해당하는 값만 들어감
		+ why 사용? → compactMap이 옵셔널 바인딩 기능을 가지고 있다
		  옵셔널 바인딩이 뭔데?
		+ map의 원래 기능에서 옵셔널 제거까지 가능,
		  map 보다 단순하고 가볍게 바꿀 수 있음.


+ # flatMap(이중 배열 펼치기)
  + 컨테이너의 각 요소를 사용하여 지정된 조건을 호출할 때, 순차적인 결과의 배열을 반환
    (중첩 배열 제거)
	```swift
	func flatMap<SegmentOfResult>(
		_ transform: (Self.Element) throws -> SegmentOfResult) 
		rethrows -> [SegmentOfResult.Element] 
		where SegmentOfResult : Sequence
	```
	-> 쉽게 말해 중첩된 배열을 제거하고 평평한 배열(flattened array)을 리턴
	
	1. 배열을 한 단계 평탄화할 때 (Flattening)
		+ 2차원 배열을 1차원 배열로 바꿈(평평한 배열(flattened array))
			```swift
			let numbers = [[1], [2, 3], [4, 5, 6], [7, 8, 9, 10]]
			
			let flatMapped = numbers.flatMap { $0 }
			print(flatMapped)
			// [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
			```
		
		+ 3차원 배열을 `flatMap` 2번 사용해서 1차원 배열로 바꿈(평평한 배열(flattened array)
			```swift
			let numbers = [[[1], [2, 3], [4, 5, 6], [7, 8, 9, 10]]]
			
			let flatMapped = numbers.flatMap { $0 }
			print(flatMapped)
			// [[1], [2, 3], [4, 5, 6], [7, 8, 9, 10]]
			
			let doubleFlatMapped = flatMapped.flatMap { $0 }
			print(doubleFlatMapped)
			// [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
			```
		- flatMap을 한 번에 두번 사용이 가능함
			```swift
			let numbers = [[[1], [2, 3], [4, 5, 6], [7, 8, 9, 10]]]
			
			let flatMapped = numbers.flatMap { $0 }.flatMap{ $0 }
			print(flatMapped)
			// [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
			```
	
	2. Optional, Result 같은 컨테이너 안의 컨테이너를 **펼쳐서 1단계로** 만들 때
	   + 1은 1번, 2는 2번, 3은 3번 반복됨 → 하나의 배열로 펼쳐짐
	     ```swift
		   let numbers = [1, 2, 3]
		   let mapped = numbers.flatMap { Array(repeating: $0, count: $0) }
		// 결과: [1, 2, 2, 3, 3, 3]
			```
		+ Optional 내의 값을 꺼내서 변환한 후 다시 Optional 감싸는 방식 (Optional 제거 x)


+ # forEach(반복 실행)
  + 각 요소에 대해 반복 실행 (반환값 없음)
    ```swift
		let numberWords = ["one", "two", "three"]
		
		numberWords.forEach { word in
		    print(word)
		}
		// Prints "one"
		// Prints "two"
		// Prints "three"
		```
	+ for-in과의 차이
	  ```swift
		let numberWords = ["one", "two", "three"]
		
		for word in numberWords {
		    print(word)
		}
		// Prints "one"
		// Prints "two"
		// Prints "three"
		```
		1. break/continue 사용: for-in 가능, forEach 불가능
		2. 클로저 내부: for-in은 return은 클로저만 종료, forEach return해도 반복 전체 종료 X
	+ break, continue 사용 불가
		```swift
		let nums = [1, 2, 3, 4, 5]
		nums.forEach { num in
		    if num == 3 {
		        return // 여기서 반복 종료되지 않음!
		    }
		    print(num)
		}
		// 출력: 1, 2, 4, 5
		```
			return은 클로저 내부만 빠져나올 뿐 반복을 종료하지 않음.
	+ 체이닝과 함께 쓰기 좋음
	  ```swift
		let names = ["seyeon", "hanna", "jake"]
		names.filter { $0.count > 5 )
		     .forEach { print("이름: \($0)") }
		```
		+ 체이닝: 함수를 이어서 줄줄이 연결해서 쓰는 것
		  (함수들을 이어붙여 데이터를 처리하는 깔끔한 방법)
		  ```swift
			let numbers = [1, 2, 3, 4, 5]
			numbers
				.filter { $0 % 2 == 0 }     // 짝수만 남김 → [2, 4]
				.map { $0 * 10 }            // 각각 10 곱하기 → [20, 40]
				.forEach { print($0) }      // 출력: 20, 40
				```
				-> forEach는 체이닝의 끝에만 사용해야 함.(return 값이 없기 때문)


+ # Map 삼형제
  + map: 변환만 함 (원본 구조 유지)
  + compactMap: 변환 + nill 제거 (Optional 처리)
  + flatMap: 변환 + 평탄화 (중첩된 배열 펼침)
	```swift
	let input = ["1", "2", "three"]
	
	let a = input.map { Int($0) }
	// [Optional(1), Optional(2), nil]
	// Optional이 있으면 그대로 남아있고, 배열이 중첩되어도 유지됨
	
	let b = input.compactMap { Int($0) }
	// [1, 2]
	// Optional 결과 중 nil을 제거하고, 언랩함
	
	let c = input.map { Array(repeating: $0, count: 2) }
	// [["1", "1"], ["2", "2"], ["three", "three"]]
	
	let d = input.flatMap { Array(repeating: $0, count: 2) }
	// ["1", "1", "2", "2", "three", "three"]
	```
	
	- ## Map
		```swift
		let a = input.map { Int($0) }
		// 결과: [Optional(1), Optional(2), nil]
		```
		- map은 각 요소에 대해 Int($0)을 수행
		- 결과는 모두 **Optional(Int)** (왜냐하면 Int 초기화는 실패할 수도 있기 때문)
	  
	+ ## CompactMap
	  ```swift
		let b = input.compactMap { Int($0) }
		// 결과: [1, 2]
			```
		- compactMap은 Optional을 반환한 후, **nil을 제거하고 언랩**까지 해줌
		- "three"는 숫자로 변환되지 않으므로 결과에서 제거
	  
	- ## FlatMap
	  ```swift
		let c = input.map { Array(repeating: $0, count: 2) }
		// 결과: [["1", "1"], ["2", "2"], ["three", "three"]]
		
		let d = input.flatMap { Array(repeating: $0, count: 2) }
		// 결과: ["1", "1", "2", "2", "three", "three"]
		```
		- 각 요소를 2번씩 반복한 배열로 변환 (Array(repeating:count:)) -> 2차 배열
		- flatMap은 결과 배열을 **1차원으로 평탄화** (즉, 2차원 → 1차원으로 합침)



## Keywords
+ 

## References
- Apple의 공식 문서
- GPT