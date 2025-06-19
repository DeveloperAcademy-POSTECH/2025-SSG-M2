>[!question]
>GQ1. GQ를 쓰세요
>GQ2. GQ를 쓰세요

## Description
- 정의
  Optional은 nil을 사용할 수 있는 타입과 없는 타입을 구분하기 위함이며,
  nil을 사용할 수 있는 Type을 Optional Type이라 부른다
  (option 선택적인 - 값이 있을 수도 있고 없을 수도 있다)

  -> 'nil' 이라는 값을 가질 수 있으면 Optional Type이고,
  이 Optional Type을 선언할 땐 타입 옆에 ?(물음표)를 붙인다
  
  + nill 이란?
    : 값이 없음

  + 왜 쓸까?
    - 값이 없을 수 있는 상황을 명시적으로 처리
    - 런타임 에러(nill 때문에 앱이 뻗는 것) 줄이려는 목적
      -> 실행 중에 에러가 발생해서 앱이 멈추고 꺼지는 현상
      (실행 중 오류가 나서 앱이 강제 종료되는 것 (Crash))
      ```swift
	      var name: String? = nill
	      print(name!) // 여기서 앱이 뻗음
	      // 아무것도 없는데 꺼내려고 하면 에러 발생
		```

```swift
var name: String? = "Sena"
```
- `name` 은 문자열이거나 nill일 수도 있는 상태
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
	만약 nill이면 앱이 크래시 발생

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

+ 옵셔널 체이닝 (`?.`)
	```swift
	let length: name?.count // name이 nill이면 전체 결과도 nill
	```
	+ 옵셔널 값이 nill일 수 있는 상황에서 안전하게 속성이나 메서드에 접근하는 방법


+ Nill 병합 연산자 ??
  ```swift
  let realName - name ?? "이름 없음" // name이 nill이면 "이름없음"을 대신 사용
	```



## Keywords
+ 파생된 키워드들을 작성

## References
- 참고한 레퍼런스를 작성 (예 : Apple의 공식 문서)