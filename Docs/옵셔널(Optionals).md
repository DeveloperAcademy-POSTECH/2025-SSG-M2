>[!question]
>GQ1. GQ를 쓰세요
>GQ2. GQ를 쓰세요

## Description
옵셔널 타입 추출 4가지 
- 강제추출 : nil 이 아닌 값이 있다는 것을 확신하고 강제로 값을 추출 ( 값이 없는 경우 에러)
```swift
num!
```
- nil 아닌지 확인후, 강제추출 : if 문을 통해 nil이 아님을 먼저 확인 후, 강제 추출(에러 가능성이 없어짐)
```swift
if num != nil {
	print(num!)
}
```
- 옵셔널 바인딩 : 바인딩(상수 나 변수에 대입)이 되는 경우만 특정 작업을 하겠다는 의미 (많이씀, 중요)
```swift
if let name = optionalName {
	print(name)
}
```
- Nil-Coalescing : 옵셔널 표현식 뒤에 기본값을 제시해서, 옵셔널의 가능성을 없앰(디폴트 값 제시 가능한 경우),옵셔널이 nil일 경우, 기본값으로 대체해주는 역할
```swift
optionalName ?? "홍길동" // 앞의 옵셔널 표현식이 nil이라면, 기본값을 제시 
```
! -> 강제 추출 연산자 
옵셔널 타입 강제로 추출, 값이 없으면 에러

**nil이 아님을 확인 후 ( 값이 있음을 확인 후), 강제로 추출해서 사용**

### 옵셔널 체이닝 
- 옵셔널 타입으로 선언된 값에 접근해서, 속성, 메서드를 사용할 때 접근연산자(.) 앞에 ?(물음표) 붙여야 함 
  1) 결과는 항상 옵셔널 타입으로 리턴
  2) 옵셔널 체이닝 과정에서 그 값 중 하나라도 nil을 리턴한다면, 이어지는 표현식을 평가하지 않고 nil 리턴 
```swift
class Dog {
	var name: String?
	var weight: Int?
	func sit() {
		print("앉았습니다.)
	}
}


var choco:Dog? = Dog() //옵셔널 Dog 타입 선언 
choco?.name = "초코" // 옵셔널 타입 접근시 ? 를 붙여야된다.
print(choco?.name)
choco?.sit()
```
### 함수와 옵셔널 타입 (함수에서 옵셔널 타입 파라미터 사용)
```swift 
func doSomePrint(with label: String, name: String? = nil){ //기본값을 nil로 설정 함수 호출시 간편함 
	print("\(label): \(name)")
}
doSomePrint(with:"레이블")
```

## 주요 기능
+ 실제 활용을 작성

## 코드 예시
```swift
var number: Int? = 7 
var hello:String? = "안녕하세요"
var name:String? = "홍길동"
var newNum:Double? = 5.5

//print를 하기 위해선, 직접적으로 값을 꺼내서 사용해야됨 

if let num = number {
	print(num)
}

// if let num에 number 값이 들어간다면, 출력

if let hi = hello {
	print(hi)
}
// 값을 벗겨서 사용


//guard let 바인딩

func doPrint(num: Int?){
	guard let n = num else{
		return
	}
	print(n)
}
func doPrint2(say: String?){
	guard let hi = say else{
		return
	}
	print(hi)
}

doPrint(num:number)
doPrint2(say:hello)
```







## Keywords
+ 파생된 키워드들을 작성

## References
- 참고한 레퍼런스를 작성 (예 : Apple의 공식 문서)