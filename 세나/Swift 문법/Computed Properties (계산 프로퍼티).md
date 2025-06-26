>[!question]
>GQ1. 어떻게 사용할까?
>GQ2. 언제 쓸까?

## Description
- 프로퍼티지만, 실제로 값을 저장하지 않고,
  get(읽기) 또는 set(쓰기)를 통해 계산된 결과를 반환하거나 설정하는 프로퍼티
- 값을 저장하지 않고, **계산 결과를 반환**

## 주요 기능
+ 기본 형태
	```swift
	var 프로퍼티이름: 타입 {
	    get {
	        // 계산한 값을 return
	    }
	    set(새이름) {
	        // 전달받은 값을 이용해서 내부 값 변경
	    }
	}
	```
	- get만 있으면 **읽기 전용 계산 프로퍼티**
		```swift
		struct Rectangle {
		    var width: Double
		    var height: Double
		
		    var area: Double {
		        return width * height
		    }
		    /*
		    var area: Double {
			    get { // 생략
			        return width * height
			    } // 생략
			}
		    */
		}
		
		let r = Rectangle(width: 5, height: 10)
		print(r.area) // 50.0
		```
		+ `area` 는 값을 저장하지 않음
		+ `width` `heigth` 를 가지고 계산해서 결과만 제공
	
	- get + set 있으면 **읽고 쓸 수 있는 계산**
		```swift
		struct Circle {
		    var radius: Double // 프로퍼티임
		    var diameter: Double {
		        get {
		            return radius * 2
		        }
		        set {
		            radius = newValue / 2
		        }
		    }
		}
		
		var c = Circle(radius: 5)
		print(c.diameter) // 10.0
		c.diameter = 20
		print(c.radius)   // 10.0
		```
		+ get은 값을 계산해서 반환
		+ set은 값을 받아서 다른 저장 프로퍼티에 반영

+ 왜 쓰는 걸까?
	+ 자동 동기화 (값이 바뀌면 자동으로 연동)
	+ 함수 대신 프로퍼티처럼 간단하게 표현 가능
	+ 값을 저장할 필요는 없고 계산만 필요할 때
		-> 메모리 절약 & 데이터 일관성 유지 가능

+ 주의
	+ 값이 변하면 항상 다시 계산됨 -> 성능이 중요한 경우 조심
	+ var만 가능 let은 안됨

+ 예시
	```swift
	struct Point {
    var x = 0.0, y = 0.0
	}
	
	struct Size {
	    var width = 0.0, height = 0.0
	}
	
	struct Rect {
	    var origin = Point()  // 꼭짓점 좌표
	    var size = Size()     // 사각형의 가로, 세로 크기
	
	    var center: Point {   // 중심점: 계산 프로퍼티
	        get {
	            let centerX = origin.x + (size.width / 2)
	            let centerY = origin.y + (size.height / 2)
	            return Point(x: centerX, y: centerY)
	        } // 중심 좌표를 계산해서 반환
	        set(newCenter) {
	            origin.x = newCenter.x - (size.width / 2)
	            origin.y = newCenter.y - (size.height / 2)
	        } // 중심 좌표를 새로 설정하면, origin을 자동 조정
	    }
	}
	```
	+ set: 원래 값을 바꾸는 게 아니라 **origin을 다시 계산**해서 옮김
	```swift
	var square = Rect(origin: Point(x: 0.0, y: 0.0),
                  size: Size(width: 10.0, height: 10.0))
	```
	+ 초기값
		+ origin = (0.0, 0.0)
		+ size = 10 x 10
	```swift
	center = (0 + 10/2, 0 + 10/2) → (5.0, 5.0)
	
	let initialSquareCenter = square.center
	// center의 get 호출 → Point(x: 5.0, y: 5.0)
	```
	
	+ 중심점을 바꾼다면
	```swift
	square.center = Point(x: 15.0, y: 15.0)
	```
	+ center의 set이 호출
	```swift
	origin.x = 15 - 10/2 = 10.0  
	origin.y = 15 - 10/2 = 10.0
	
	square.origin == Point(x: 10.0, y: 10.0)
	```
	
	+ center는 origin + size로 계산되기 때문에 따로 저장하지 않음

+ Shorthand 문법 (축약형)
	```swift
	struct Point {
	    var x = 0.0, y = 0.0
	}
	
	struct Size {
	    var width = 0.0, height = 0.0
	}
	
	struct Rect {
	    var origin = Point()  // 사각형의 왼쪽 위 꼭짓점 좌표
	    var size = Size()     // 사각형의 가로, 세로 크기
	
	    var center: Point {   // 중심점: 계산 프로퍼티
	        get {
	            let centerX = origin.x + (size.width / 2)
	            let centerY = origin.y + (size.height / 2)
	            return Point(x: centerX, y: centerY)
	        }
	        set(newCenter) {
	            origin.x = newCenter.x - (size.width / 2)
	            origin.y = newCenter.y - (size.height / 2)
	        }
	    }
	}
	```
	
	+ newValue 생략
	```swift
	set {
    origin.x = newValue.x - (size.width / 2)
    origin.y = newValue.y - (size.height / 2)
	}
	```
		set(newCenter)처럼 별도 이름 안 주면 자동으로 newValue 사용
	
	+ return 생략
	```swift
	get {
	    Point(x: origin.x + (size.width / 2),
	          y: origin.y + (size.height / 2))
	}
	```

+ Read-Only 계산 프로퍼티
	+ get만 있고 set은 없는 계산 프로퍼티 
	  ->읽기 전용

+ 실제 사용
	+ 화면 표시용 값 가공 / 계산 / 필터링
	+ 사용자 인터페이스에서 폼 데이터 연결
		+ ViewModel에 입력 필드를 관리할 때
		```swift
		class FormViewModel: ObservableObject {
		    @Published var age: Int = 20
		
		    var ageString: String {
		        get {
		            String(age)
		        }
		        set {
		            age = Int(newValue) ?? 0  // 사용자가 잘못 입력하면 0으로 처리
		        }
		    }
		}
		```
	+ SwiftUI 토글 + 내부 논리 연동
		```swift
		struct Settings {
		    var isDarkMode: Bool
		
		    var themeText: String {
		        get {
		            isDarkMode ? "Dark" : "Light"
		        }
		        set {
		            isDarkMode = (newValue == "Dark")
		        }
		    }
		}
		```

## References
- [swift.org](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties/#Computed-Properties)
