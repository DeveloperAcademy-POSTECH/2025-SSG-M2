## Description
- 저장 프로퍼티는 클래스 또는 구조체의 인스턴스에 **값을 저장**하는 가장 기본적인 프로퍼티이다.
- 인스턴스가 생성될 때 메모리에 공간이 할당되고, 프로퍼티의 값을 유지함
	- 인스턴스 속성
		- 클래스, 구조체, 열거형의 **각 인스턴스(객체)에 속한 프로퍼티(속성)**
		-> 각 인스턴스마다 개별적으로 갖고 있는 변수 또는 상수
		```swift
		struct Dog {
		    var name: String      // 인스턴스 속성
		    var age: Int          // 인스턴스 속성
		}
		
		let dog1 = Dog(name: "뽀삐", age: 3)
		let dog2 = Dog(name: "초코", age: 5)
		
		print(dog1.name) // 뽀삐
		print(dog2.name) // 초코
		```
		- name과 age는 Dog 구조체에 정의된 인스턴스 속성
		- dog1과 dog2는 서로 다른 인스턴스이므로 name과 age 값도 독립적으로 유지됨
- var로 선언하면 값 변경 가능, let으로 선언하면 초기화 이후 값 변경 불가함

## 주요 기능
+ 구조체 또는 클래스의 인스턴스 내부에 상태를 저장하고 추적이 가능함
+ 객체의 속성(예: 사용자 이름, 점수, 위치 등)을 저장할 때 사용됨
+ SwiftUI의 @State, @ObservedObject 등이 내부적으로 저장 프로퍼티를 활용
+ **lazy** 키워드를 붙이면, **처음 사용할 때 초기화되는 저장 프로퍼티**가 됨
	  - lazy는 **리소스가 무겁거나, 나중에 필요한 값**을 늦게 초기화할 때 사용
	  - 반드시 var로 선언 (let은 안됨)
	```swift
	class DataImporter {
	    var filename = "data.txt"
	}
	
	class DataManager {
	    lazy var importer = DataImporter() // lazy 프로퍼티
	    var data: [String] = []
	}
	
	let manager = DataManager()
	print(manager.importer.filename) // 이 순간에 importer가 생성됨!
	```

- 초기화 방법
	1. 선언할 떄 기본값을 줄 수도 있고,
	2. 아니면 초기화(init) 시에 값을 설정할 수도 있다.
	3. let으로 선언하고, 초기화 시 값을 넣는 건 가능
	   단, 이후에는 변경 불가

## 코드 예시

```swift
struct FixedLengthRange {
    var firstValue: Int    // 변수 저장 프로퍼티
    let length: Int        // 상수 저장 프로퍼티
}
```

```swift
struct User {
    var name: String         // 변경 가능한 저장 프로퍼티
    let birthYear: Int       // 변경 불가능한 저장 프로퍼티
}

var user = User(name: "세연", birthYear: 1900)
user.name = "준호" // 가능
// user.birthYear = 2000 // 오류: let으로 선언된 저장 프로퍼티는 변경 불가
```

## Keywords
+ 저장 프로퍼티
+ 인스턴스 속성
+ 불변 속성(let)
+ 가변 속성(var)

## References
- [Swift 공식 문서](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties/#Stored-Properties-and-Instance-Variables)