
   Class는 참조타입이고 ARC로 메모리 관리를 한다. Struct는 값 타입이다

==이 차이를 알아야 하는 이유는??
-> 개발자가 가장 중요하게 고려해야 할 것 중 하나는 성능이며 Class, Struct의 차이점을 확실히 알고 있다면 성능개선을 할 수 있기 때문입니다!


![[스크린샷 2025-05-27 오후 4.11.35.png | 500]]

![[스크린샷 2025-05-27 오후 4.11.06.png| 500]]

# Class, Struct의 공통점

- 값을 저장할 ==프로퍼티==를 선언할 수 있습니다.
- 함수적 기능을 하는 메서드를 선언 할 수 있습니다.
- 내부 값 을 사용하여 접근할 수 있습니다.
- 생성자를 사용해 초기 상태를 설정할 수 있습니다.
- 등등


### **🔑 핵심 차이: Value Type vs Reference Type**

| **항목**         | **Struct (구조체)**                    | **Class (클래스)**                     |
| -------------- | ----------------------------------- | ----------------------------------- |
| **타입**         | ✅ Value Type (값 타입)                 | 🔁 Reference Type (참조 타입)           |
| **복사 방식**      | 데이터를 ==복사==해서 전달                    | 데이터를 ==참조==해서 전달                    |
| **동작 예시**      | 변수에 할당하거나 함수 인자로 넘기면 **새로운 복사본** 생성 | 변수에 할당하거나 함수 인자로 넘기면 **같은 인스턴스 참조** |
| **메모리 위치**     | 스택(Stack)                           | 힙(Heap)                             |
| **Mutability** | 구조체 내부 프로퍼티 변경 시 mutating 키워드 필요    | 클래스는 자유롭게 내부 변경 가능                  |
|                |                                     |                                     |
* ==mutating==
구조체는 **값 타입(Value Type)**이기 때문에, 기본적으로 메서드 안에서는 self의 속성을 바꿀 수 없음.\ 그래서 **직접 속성을 수정하려면 mutating 키워드로 명시**해줘야 함

``` swift
struct Counter {
    var count: Int = 0

    mutating func increment() {
        count += 1   // mutating 없으면 에러!
        
        //조체는 기본적으로 내부 값을 수정할 수 없기 때문에, 
        // 이렇게 mutating을 붙여줘야 인스턴스 값을 바꾸는 것이 허용
    }

    mutating func reset() {
        self.count = 0
    // - self.count라고 쓴 건 명확하게 이 구조체 인스턴스의 count를 말한다는 의미야. 
    // (self는 생략해도 되지만 명시적으로 쓴 거임)
    }
}

```



 ==인스턴스 (Instance)==
-> 클래스나 구조체를 이용해서 실제로 만들어낸 객체

``` swift
class Dog {
    var name: String
    init(name: String) {
        self.name = name
    }
}

let myDog = Dog(name: "초코:)
// 여기서 myDog 가 바로 인스턴스임!!
// "초코" 라는 이름을 가진 실제 강아지 객체 하나가 메모리에 생김
** 클래스나 구조체는 그 자체로는 아무 것도 못하고 인스턴스 (행동)을 만들어야 실제 데이터를 담고,
함수도 쓸 수 있음

print(myDog.name)

더 쉽게 표현 하면 Swift에서 let,var로 만들면 대부분 인스턴스 임
```




 - 1) 구조체 (Struct)

    - 값형식(Value Type)
    - 인스턴스 데이터를 모두 스택(Stack)에 저장
    - 구조체 변수를 새로운 변수에 할당할 때마다 새로운 구조체가 할당됩니다.
   -> 즉 같은 구조체를 여러 개의 변수에 할당한 뒤 값을 변경시키더라도 다른 변수에 영향을 주지 않습니다. (값 자체를 복사)


 - 2) 클래스 (Class)

    - 참조형식(Reference Type)
    - 상속이 가능합니다.
    - 인스턴스 데이터는 힙(Heap)에 저장, 변수 자체는 스택에 저장되고, (스택에 저장된) 변수 안에 메모리 주소값이 힙(Heap)의 인스턴스를 가리킴
    - (복사시) 값을 전달하는 것이 아니고, 저장된 주소를 전달
    - 힙(Heap)의 공간에 저장, ARC시스템을 통해 메모리 관리(주의해야함) ==========================================================****/**

==그 외의 면에서는 클래스와 구조체는 거의 동일하므로,


## **구조체 예시 (값 타입: Value Type)**

``` swift
struct Cat {
    var name: String
}

func testStruct() {
    var cat1 = Cat(name: "Nabi")
    var cat2 = cat1  // 👉 복사 발생 (cat2는 cat1의 복사본)

    cat2.name = "Coco"

    print("cat1.name: \(cat1.name)")  // 출력: Nabi
    print("cat2.name: \(cat2.name)")  // 출력: Coco
}
```

### **🧠 메모리 구조 설명**

- cat1 → 스택에 저장됨 (name: “Nabi”)
- cat2 → cat1의 **복사본**이므로, 또다른 스택 공간에 저장 (name: “Coco”)
- 서로 다른 메모리 공간 → 값 변경 시 ==독립적==



## **클래스 예시 (참조 타입: Reference Type)**

``` swift
class Dog {
    var name: String

    init(name: String) {
        self.name = name
        // 생성자 함수 (Initializer) 생성자 함수
        // 외부에서 name이라는 문자열을 받아와서 쓰겠다는 의미
        
        // self는 이름이 겹칠때 헷갈리지 말라고 씀
        // self.name -> 이건 클래스안의 속성 name -> 이건 init의 파라미터
        // 속성쪽 name 이라고 알려 주는 것 , 지금 코드에선 상관 없지만 코드가 복잡해지면 써줘야 함
    }
}

func testClass() {
    var dog1 = Dog(name: "Happy")
    var dog2 = dog1  // 👉 복사 X, 같은 힙의 인스턴스를 참조

    dog2.name = "Mocha"

    print("dog1.name: \(dog1.name)")  // 출력: Mocha
    print("dog2.name: \(dog2.name)")  // 출력: Mocha
}
```

### **🧠 메모리 구조 설명**

- dog1 → 스택에 저장된 변수 (힙에 있는 Dog 인스턴스 주소를 가리킴)
- dog2 → 스택에 저장된 변수 (==같은 주소 가리킴==)
- Dog 인스턴스(name: “Happy”) → 힙에 존재 → name을 바꾸면 둘 다 영향을 받음
- 힙 메모리는 ARC(Automatic Reference Counting)에 의해 관리됨


## **차이 핵심 요약**

| **항목** | **구조체 (Struct)**    | **클래스 (Class)**    |
| ------ | ------------------- | ------------------ |
| 저장 위치  | 스택 (주로)             | 힙 (데이터) + 스택 (참조)  |
| 복사     | 값을 복사               | 참조를 복사             |
| 독립성    | 복사 후 독립             | 참조 공유              |
| 메모리 관리 | 자동 제거 (스택 프레임 종료 시) | ARC에 의한 관리 (주의 필요) |
|        |                     |                    |
![[스크린샷 2025-05-27 오후 4.13.10.png]]

![[스크린샷 2025-05-27 오후 4.12.46.png]]
# ARC = Automatic Reference Counting 

참조 타입은 하나의 인스턴스가 참조를 통해 여러 곳에서 접근하기 때문에 언제 메모리에서 해제되는가가 중요한 문제이다. 적절한 시점에 인스턴스가 해제되지 않으면 한정적인 메모리 자원을 낭비하게 되고, 이는 성능 저하로 이어질 수 있다.

Swift는 프로그램의 메모리 사용을 관리하기 위해 ==**메모리 관리 기법**인 **ARC(Automatic Reference Counting)을 사용**한다.==

> ARC가 관리해주는 Reference Counting (참조 횟수 계산)은 참조 타입인 클래스의 인스턴스에만 적용된다.  
> 구조체나 열거형은 값 타입으로 다른 곳에서 참조하지 않기 때문에 ARC로 관리할 필요가 없다.



## ARC란?

ARC는 자동으로 메모리를 관리해주는 방식이다. 대부분의 경우 메모리 관리는 Swift에서 그냥 작동하기 때문에 개발자는 메모리 관리에 대해 신경을 덜 쓸 수 있다.

_스택(Stack)_ 메모리에 저장된 데이터는 자동으로 제거되기 때문에 특별한 관리가 필요 없다.

하지만 _힙(Heap)_ 메모리에 저장된 데이터는 필요하지 않은 시점에 직접 제거해야만 한다. 따라서 메모리 관리 모델이 힙에 저장된 데이터를 관리한다.

값 타입(Value Type)은 _스택_ 메모리에, 참조 타입(Reference Type)은 _힙_ 메모리에 저장된다.

값 타입: Struct, Enum

참조 타입: Class, Closure

==**힙은 클래스, 클로저== 등의 참조형 자료들이 머무는 공간이자, ==개발자가 동적으로 할당하는 메모리 공간이므로 관리가 필요**하다.==

관리를 위해서 힙 영역에 참조형 자료들이 얼마나 참조되고 있는지 카운팅하고 이에 따라 메모리를 할당 및 제거하면 된다. 이것을 자동으로 해주는 것이 ARC이다.

#인스턴스생성

클래스의 인스턴스가 생성되면 해당 인스턴스의 정보는 **HeapObject**라는 struct로 관리가 된다.

ARC의 메커니즘은 _Swift Runtime_이라는 라이브러리에 구성되어 있는데, _Swift Runtime_은 동적 할당되는 모든 object를 HeapObject라는 struct로 표현한다. 또한 HeapObject에서는 Swift에서 객체를 구성하는 모든 데이터, 즉 **reference count와 type meta data**를 포함하고 있다.

#인스턴스해제

ARC는 인스턴스가 더 이상 필요하지 않다면 해당 인스턴스를 메모리에서 해제시킨다.

알고보니 인스턴스가 필요했다면 인스턴스의 프로퍼티와 함수에 접근이 불가하고, 강제 접근시 앱이 죽을 수 있다.

따라서 ARC는 해당 인스턴스를 참조하는 다른 인스턴스의 프로퍼티나 변수, 상수 등의 개수를 세고, 해당 인스턴스에 대한 활성 참조가 1개 이상 존재하는 한 인스턴스를 메모리에서 없애지 않는다. heapObject의 reference count가 활성 참조이다.



# 코드 영역 (Code Segment/Text Segment)

1) 기계어로 번역된 실제 실행 코드가 저장되는 영역
2) 내가 작성한 함수, 메서드 같은 코드가 들어감
``` swift

func sayHi() {
	print("Hi)
}

```


# 데이터 영역 (Data Segment)

1) 전역 변수 , static 변수가 저장됨
2) 초기화된 데이터 -> Data 영역
3) 초기화되지 않은 데이터 -> BSS 영역이라고 함
4) 프로그래밍 모든 과정에서 존재
ex)

``` swift
var globalCount = 3  // 전역 변수 → 데이터 영역
```

# 힙 영역 (Heap)

1) 동적으로 생성되는 객체가 저장 되는 공간
2) 메모리 주소를 통해 접근
ex)

``` swift
class Dog {
    var name: String
}
let dog = Dog()  // ← dog 객체는 힙에 저장됨
```




# 스택 영역 (Stack)

1) 함수 호출 시 생기는 임시 데이터 저장소
 -> 지역 변수, 매개변수, 리턴 주소 등
 2) 위에서 아래로 쌓이고, 함수가 끝나면 자동 제거 (LIFO 구조)
 3) 속도 빠름, 자동 관리
ex) 
``` swift
func greet(name: String) {
    let message = "Hello, \(name)"  // name, message → 스택에 저장
}
```


Stack은 LIFO(Last In First Out) 형태의 자료구조로 가장 마지막에 들어간 객체가 가장 먼저 나오게 되는 자료구조인데요, 자료구조 특성상 하나의 명령어로 메모리를 할당, 해제할 수 있습니다. 또한 컴파일 단계에서 언제 생성되고 해제되는지 알 수 있는 구조체와 같은 값들이 스택에 저장되게 됩니다.

스레드들은 각각 독립적인 Stack 공간을 가지고 있기 때문에 상호 배제를 위한 자원이 필요하지 않습니다. 즉 스레드로부터 안전하다는 말이 됩니다. 

이러한 특징 때문에 Stack의 값을 사용하는 것이 Heap의 값을 사용하는 것보다 빠르다고 할 수 있습니다.


# Heap 할당

Heap에는 컴파일 단계에서 생성과 해제를 알 수 없는 참조 타입의 객체가 할당됩니다. 즉 Swift에서는 클래스 객체가 힙에 할당되게 됩니다. Heap은 Stack보다 관리하기가 어려운데요, 이는 메모리 할당과 해제가 하나의 명령어로 처리되지 않기 때문입니다. 아까 Stack에서는 pop, push라는 하나의 명령어로 할당, 해제가 이루어졌지만 Heap은 참조 계산도 해줘야 하므로 Stack보다 복잡합니다.

또한 Heap은 스레드가 공유하는 메모리 공간이기 때문에 스레드로부터 안전하지 않습니다. 즉 이를 관리해주기 위한 lock과 같은 자원도 필요하게 되고 이는 곧 오버 헤드로 이어지게 됩니다.




# 근데 왜 Xcode에서 Swift 프로젝트 만들면 Struct??

기본 뷰, 모델, 앱 구조가 전부 struct로 되어 있음!!

``` swift
struct ContentView: View {
    var body: some View {
        Text("Hello, world!")
    }
}

```

근데 Swift는 클래스도 있는데 왜 struct일까?

1) SwiftUI는 값 기반 (Value-Based) UI 프레임 워크
- - SwiftUI는 UI를 구성할 때 ==**값 타입** ==중심으로 작동!
- struct는 **복사 기반**이라서, 어떤 뷰가 변경되면 그냥 새로운 struct 값을 만들고 덮어쓰면 끝!!
-> 즉, 뷰의 상태가 바뀔 때마다 “다시 그리기”가 간편하고 안전해

만약 Class면 원본이 복사될 우려가 있음!

2) struct는 가볍고 안전함 
- struct는 복사이니깐 ==참조 공유 문제가 없음
- 멀티스레드에서도 안정적
- 값이 변경되더라도 원본은 건드리지 않음
-> Swift의 기본 철학이 "안정성과 예측 가능성" 이라서 struct를 우선시함

2) Swift언저 자체가 Struct 우선 철학을 가지고 있음
- 구조체를 통해 개발자가 불필요한 복잡성 (메모리 누수, ARC 관리 없이) 간단하고 안전하게
  코드를 작성하긴 유도

그렇 다면 class를 쓰는 대표적인 순간들

1. 참조 (Reference)를 공유해야 할 때
-> 어떤 객체를 여러 곳에서 동시에 보고, 하나의 변경이 모든 곳에 반영되어야 할 때

Ex) 로그인 상태, 세션정보, 전역 설정 등

2. ==ObservableObject와 @Published를 쓸 때 (SwiftUI 상태 관리)==
-> SwiftUI에서 뷰의 상태를 class로 관리할 때는 반드시 ObservableObject 프로토콜을 따르고,         @Published 속성으로 바뀌는 값을 추적

``` swift
class CounterModel: ObservableObject {
    @Published var count = 0
}
```

struct는 ObservableObject가 될 수 없어서, 뷰모델처럼 상태를 공유하고 추적해야 할 때는 class만 가능

3. 상속이 필요한 경우
struct는 상속이 안 되니까, 부모 클래스를 만들어서 공통 기능을 재사용 하려면 class가 필요

``` swift

class Animal {
    func speak() {
        print("...")
    }
}

class Dog: Animal {
    override func speak() {
        print("멍멍!")
    }
}

```

swift는 프로토콜 중심 언어지만, 복잡한 OOP 설계에는 class가 쓰임


즉 
- 뷰 상태를 계속 추적해야 하는 ViewModel
- 뷰 간 공유되는 전역 상태
- UIKit 컴포넌트 상속

> 기본은 struct,
> “참조가 필요하거나 ObservableObject일 때 class!”