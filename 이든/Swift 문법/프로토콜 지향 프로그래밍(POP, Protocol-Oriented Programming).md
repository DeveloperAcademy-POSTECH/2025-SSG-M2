> [!question] 
>GQ1. POP는 무엇이고 OOP와 뭐가 다를까?
GQ2. Swift에서 protocol이 왜 중요한가?
GQ3. protocol에 extension을 붙이면 뭐가 좋은가?
 GQ4.실무에서 POP는 언제 쓰면 좋을까?



## **1. POP란?**

POP는 **Protocol-Oriented Programming**의 줄임말로, Swift 언어에서 특히 강조되는 프로그래밍 패러다임입니다.  
이 패러다임은 객체지향(OOP)처럼 클래스와 상속에 의존하기보다는, **프로토콜** 과 **익스텐션(Extension)** 을 중심으로 코드의 구조와 재사용성을 설계합니다

[POP 관련 문서](https://kentakodashima.medium.com/swift-understanding-protocol-oriented-programming-bbef282ae922)

> 왜 POP가 등장했을까 ? 
- OOP(객체지향 프로그래밍)는 클래스의 상속을 통해 공통 기능을 모듈화 합니다.
- 하지만 클래스는==참조 타입==이기 때문에, 다중 스레드 환경에서 ==데이터 변경 위험==이 있고, ==다중 상속이 불가능해 유연성이 떨어집니다.==
- Swift는 다들 알고 계시듯 ==구조체(struct) & 열거형(enum) 같은 값 타입==을 적극적으로 활용하며, 상속이 불가한 값 타입에서도 공통 기능을 ==쉽게 재사용하기 위해 ==POP가 등장했습니다.

> POP의 핵심 개념은 무엇일까? 무엇이 이 친구를 중요하게 만들었는가 ?

- 프로토콜 (Protocol) : 타입이 가져야 할 속성, 메서드 등의 요구사항을 정의합니다.
- 익스텐션(Extension) :  닉스가 잘 조사하셨으니 간단히 !  **프로토콜에 기본 구현을 추가할 수 있습니다!**
- 구조체, 클래스, 열거형 모두 프로토콜 채택 가능 : 상속과 달리 여러 프로토콜을 동시에 채택할 수 있어 기능을 자유롭게 조합할 수 있습니다.
- ==유연한 코드 재사용 : 프로토콜 & 익스텐션 조합 = 코드 중복 없이 다양한 타입에 공 통 기 능 쉽 게 부 여 ! 
  개 사 기==
  
>POP 코드 예시를 알아봅시다.

```swift
protocol Talkable {
	var language: String { get set }
	func getLanguage()
}
// 해당 프로토콜을 채택하는 타입은 반드시 다음을 구현해야겠죠 ? 

extension Talkable {
	func getLanguage() {
	print(" ---> I Speak \(language)")
	}
}
// 프로토콜을 확장(extension)해서 기본 구현 제공 
// func getLanguage()를 프로토콜 채택자들이 직접 구현하지 않아도 사용 가능함
// 이 부분이 핵심 ! ! 기본 동작을 정의하고, 필요할 때만 오버라이드 하면 됩니다 !! 

// 구조체에서 프로토콜 채택
struct Korean: Talkable {
	 var language: String
}
struct American: Talkable {
	var language: String
}
let korean = Korean(language: "korean")
korean.getLanguage() // ---> I speak korean

let american = American(language: "english")
american.getLanguage() // ---> I speak english`

// 각각의 구조체 인스턴스를 만들고, getLanguage()  호출 
// 실제로는 프로토콜 확장에서 구현된 함수 친구가 실행됨 !! 

```

> [!note] 이 코드의 장점 !!!
> - 같은 인터페이스(Talkable)를 따르므로 **일관성 있는 API** 제공
> - 기본 동작은 공통 구현(extension)으로 처리하고, 필요 시 **override도 가능**
> - Swift에서 **클래스가 아닌 구조체도 프로토콜 기반으로 유연하게 설계** 가능


> 제네릭과 연관 타입 활용 ! ! ! 


```swift
protocol Stackable { //스택처럼 뭔가를 쌓는 기능을 가진 타입을 정의하는 프로토콜 ! 
    associatedtype Item
    var items: [Item] { get set }
    mutating func add(item: Item)
}

extension Stackable {
    mutating func add(item: Item) { //구조체가 이걸 채택하면,값을 바꾸는 함수임을 명시해야 하므로 mutating 사용 
        items.append(item)
    }
}
//mutating은 구조체나 열거형에서 값을 변경할 때 꼭 필요한 키워드라고 합니다 ! 다만 class 에서는 안쓴데요 ! Class 는 참조 타입이라, 함수에서 프로퍼티를 바꿔도 mutating 없이 가능 

struct Stack<T>: Stackable { // 제네릭 구조체 ! T는 타입을 유연하게 받을 수 있음 어떤 타입이든 담을 수 있는 스택 ! 
    var items: [T]
    typealias Item = T
}

var intStack = Stack<Int>(items: [1, 2, 3])
intStack.add(item: 4)
print(intStack.items) // [1, 2, 3, 4]

```

> OOP와 POP의 차이점 ! 

>[!note] 차이점 리스트
>
| 구분      | OOP (객체지향)        | POP (프로토콜지향)         |
| ------- | ----------------- | -------------------- |
| 코드 재사용  | ==클래스 상속==        | ==프로토콜+익스텐션==        |
| 다중 상속   | 불가                | 프로토콜 다중 채택 가능        |
| 타입      | ==주로 클래스==        | ==구조체, 열거형, 클래스 모두== |
| 데이터 안전성 | ==참조 타입(동시성 위험)== | ==값 타입(안전) 강조==      |
| 확장성     | 수직적(상속 계층)        | 수평적(조합, 모듈화)         |

> 마지막 ! 실무에서 POP를 쓰면 좋은 상황 ! ! ! 

- 공통 기능을 여러 타입에 공유하고 싶을 때 
- 다중 기능을 조합하고 싶을 때 
- 기본 동작은 주고, 필요할 때만 재정의하고 싶을 때 
- 테스트 코드 작성할 때 편하게 만드려고할 때 
- SwiftUI, Combine, Swift Concurrency 등과 잘 어울림  

>[!note] POP를 이번에 조사한 이유 
> 요즘 ios 앱 개발자 공고를 보면 꽤 많은 회사에서 POP에 대해 잘 이해하고 있으신 분 ! 
> 이라는 말들이 있더라구요 ? 나중에 취업에 조금이라도 도움이 되고자 가져왔슴다 !! 

![[Pasted image 20250626010211.png]] 토스뱅크
![[스크린샷 2025-06-26 오전 1.02.38.png]]
페이민트 (카카오페이 자회사)
