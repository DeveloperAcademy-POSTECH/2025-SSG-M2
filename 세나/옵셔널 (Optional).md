
## Description
- 정의
  Optional은 nil을 사용할 수 있는 타입과 없는 타입을 구분하기 위함이며,
  nil을 사용할 수 있는 Type을 Optional Type이라 부른다
  (option 선택적인 - 값이 있을 수도 있고 없을 수도 있다)

  -> 'nil' 이라는 값을 가질 수 있으면 Optional Type이고,
  이 Optional Type을 선언할 땐 타입 옆에 ?(물음표)를 붙인다
  
  + nil 이란?
    : 값이 없음

  + 왜 쓸까?
    - 값이 없을 수 있는 상황을 명시적으로 처리
    - 런타임 에러(nil 때문에 앱이 뻗는 것) 줄이려는 목적
      -> 실행 중에 에러가 발생해서 앱이 멈추고 꺼지는 현상
      (실행 중 오류가 나서 앱이 강제 종료되는 것 (Crash))
      ```swift
	      var name: String? = nil
	      print(name!) // 여기서 앱이 뻗음
	      // 아무것도 없는데 꺼내려고 하면 에러 발생
		```

```swift
var name: String? = "Sena"
```
- `name` 은 문자열이거나 nil일 수도 있는 상태
- `String?` 는 "Optional String"이라 읽음

- 선언 방법
  ```swift
  var nickname: String? 
  var age: Optional<Int> = 25
  var age: Int? = 25 // 초기값 있음
	```

+ 강제 언래핑
  ```swift
  let name: String? = "Sena"
  print(name!) // "Sena"
	```
	`!`로 값을 강제로 꺼냄
	만약 nil이면 앱이 크래시 발생

+ 안전한 언래핑(옵셔널 바인딩:Optional 값이 nil인지 아닌지 확인하고, 값이 있다면 안전하게 꺼내서 사용하는 방)
	+ if let
	  ```swift
		let name: String? = "Sena"
		
	    if let unwrapped = name {
		    print("이름은 \(unwrapped)")
		} else {
		    print("이름이 없음")
		}
		```
		+ if let은 내부적으로 name != nil을 검사해주고 값을 꺼내줌
		+ if문과의 차이점
		  ```swift
		  if name != nil {
			    print("값 있음")
			} else {
			    print("값 없음 (nil)")
			}
			```
			-> 이 경우 값을 꺼낼 수 없고, 값이 있는지만 확인만 가능
	
	+ guard let
	  ```swift
		  func printName(_ name: String?) {
		    guard let unwrapped = name else {
		        print("이름이 없음")
		        return
		    }
		    print("이름은 \(unwrapped)")
		}
		```


+ Nil 병합 연산자 ??
  ```swift
  let realName - name ?? "이름 없음" // name이 nil이면 "이름없음"을 대신 사용
	```


+ 옵셔널 체이닝 (`?.`)
	```swift
	let length: name?.count // name이 nil이면 전체 결과도 nil
	```
	+ 옵셔널 값이 nil일 수 있는 상황에서 안전하게 속성이나 메서드에 접근하는 방법
	
	+ 옵셔널 체이닝으로 프로퍼티 접근
		```swift
		struct Student {
		    var name: String
		}
		
		let student: Student? = Student(name: "세나")
		print(student?.name)   // Optional("세나")
		
		let noOne: Student? = nil
		print(noOne?.name)     // nil
		```
		
	+ 메서드 호출
	  ```swift
	  class Dog {
	    func bark() {
		        print("멍멍!")
		    }
		}
		
		let myDog: Dog? = Dog()
		myDog?.bark()  // "멍멍!" 출력
		
		let ghostDog: Dog? = nil
		ghostDog?.bark()  // 아무 일도 일어나지 않음 (앱도 안 뻗음!)
		```
	
	+ 다단계 체이닝
		```swift
		struct Owner {
		    var pet: Dog?
		}
		
		let owner = Owner(pet: Dog())
		owner.pet?.bark() // "멍멍!"
		```  
	
	+ 언제 사용?
	  1. 값이 nil일 수 있는 경우
	  2. nil이면 그냥 무시하고, 아니면 이어서 무언가 하고 싶을 때
	  3. 중첩된 객체 속성에 접근할 때 (ex. user → profile → email 등)

+ 선택적 체이닝
  + 구조체에서 체이닝
  ```swift
	  struct Person {
	    var residence: Residence?
	}
	
	struct Residence {
	    var numberOfRooms = 1
	}
	
	let person = Person(residence: nil)
	
	let roomCount = person.residence?.numberOfRooms
	print(roomCount)  // nil
	```
		-> residence가 nil이라서 → .numberOfRooms에 접근하지 않고 nil 반환됨.
		
	+ 메서드에서 호출
	  ```swift
	  class Dog {
	    func bark() {
	        print("멍멍!")
	    }
		}
		
		let dog: Dog? = Dog()
		dog?.bark() // 멍멍!
		
		let noDog: Dog? = nil
		noDog?.bark() // 아무 일도 일어나지 않음
		```
		-> 메서드도 ?.로 호출 가능 → 안전하게 무시됨
		
	+ 체인 연속 사용
	  ```swift
	  class Company {
	    var ceo: Person?
		}
		
		let apple = Company()
		let ceoName = apple.ceo?.residence?.numberOfRooms
		// 중간에 ceo가 nil이면, 전체 결과는 nil
		```
		-> 여러 옵셔널 프로퍼티를 안전하게 타고 내려감
		-> 중간에서 하나라도 nil이면 전체가 nil
