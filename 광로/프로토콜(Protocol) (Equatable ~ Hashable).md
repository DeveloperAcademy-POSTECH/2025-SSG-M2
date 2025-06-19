
*프로토콜*
-> 특정 기능을 제공하기 위해 타입이 지켜야 하는 '규칙'의 집합
쉽게 말하면 이 타입은 반드시 이런 기능을 구현해야 한다 와 같은 약속!
즉, ==프로토콜 = 약속== 


``` swift
protocol MyProtocol {
    var name: String { get set }   // 프로퍼티 요구
    func sayHello()                // 메서드 요구
}

여기서 MyProtocol을 채택한 타입은 반드시 name이라는 프로퍼티와
sayHello() 메서드를 구현해야 함

따라서

struct Person: MyProtocol {
    var name: String

    func sayHello() {
        print("안녕하세요, \(name)입니다.")
    }
}

이렇게도 쓸 수 있음


```


그럼 이게 왜 필요할까?


1) **공통된 기능을 강제해서** 구조를 잡아줌
2) 다형성 (Polymorphism)을 가능하게 해줌 -> 다양한 타입을 같은 방식으로 다룰 수 있음
3) 코드 재사용성이 높아지고, 테스트도 쉬워짐

이렇게 이야기 하니깐 더 어려움 ...예시코드로 보자



``` swift
// 프로토콜 정의
protocol Drivable {
    var speed: Int { get set }
    func drive()
}

// 구조체가 프로토콜 채택
struct Car: Drivable {
    var speed: Int

    func drive() {
        print("Driving at \(speed) km/h")
    }
}

let myCar = Car(speed: 100)
myCar.drive() // 출력: Driving at 100 km/h
```

이렇게 되면 Car 는 Drivable이라는 "프로토콜 = 약속"을 지킨것이다.

자 그럼 위의 3가지 이유로 찾아 보자!


``` swift
protocol Drivable {
    var speed: Int { get set }
    func drive()
}
```

이 Drivable 프로토콜은 쉽게 말해서다 쉽게 말해서 ㅋㅋㅋ


"달릴 수 있는 타입은 반드시 speed와 drive()를 가져야 해!" 라는 명확한 규칙을 세워준다.
이걸 따르면 누가 만들었든, 어떤 구조체든 클래스든 다 speed와 drive()를 가지게 된다.


이렇게 되면 다른 '탈것' 들도 가져와서 프로토콜을 붙이면 된다.


``` swift
struct Bike: Drivable {
    var speed: Int
    func drive() {
        print("Pedaling at \(speed) km/h")
    }
}

struct Scooter: Drivable {
    var speed: Int
    func drive() {
        print("Scooting at \(speed) km/h")
    }
}
```

ㅋㅋㅋ 슛터, 바이크 다됨 ㅋㅋㅋ (서로 다른 타입이지만 일관된 구조로 동작하게 강제 가능)




2) 다형성
->  프로토콜을 타입처럼 사용할 수 있음! -> 다양한 타입들을 하나의 컬렉션으로 묶고, 동일한 방식으로 처리!


``` swift

let vehicles: [Drivable] = [
    Car(speed: 100),
    Bike(speed: 20),
    Scooter(speed: 30)
]

for vehicle in vehicles {
    vehicle.drive()
}

vehicles는 Drivable 프로토콜을 따르는 객체들의 배열임 따라서, Car, Bike, Scooter처럼
Drivalbe을 채택한 타입의 인스턴스들만 들어올 수 있음.

사실 이렇게만 보면 ..개어려움 좀더 자세하게 코드를 쓴다고 하면 아래와 같이 됨


protocol Drivable {
    var speed: Int { get set }
    func drive()
}

struct Car: Drivable {
    var speed: Int
    func drive() {
        print("Car driving at \(speed) km/h")
    }
}

struct Bike: Drivable {
    var speed: Int
    func drive() {
        print("Bike riding at \(speed) km/h")
    }
}

struct Scooter: Drivable {
    var speed: Int
    func drive() {
        print("Scooter scooting at \(speed) km/h")
    }
}

let vehicles: [Drivable] = [
    Car(speed: 100),
    Bike(speed: 20),
    Scooter(speed: 30)
]

for vehicle in vehicles {
    vehicle.drive()
}

- vehicle은 반복문을 돌면서 배열 안에 있는 각 객체를 하나씩 꺼냄
- 여기서 전부 Drivable이라는 타입으로 다뤄지므로, Drive()함수만 호출할 수 있음
- 내부적으로는 각각의 구조체(Car, Bike, Scooter)에서 구현한 drive()가 실행된다
  
실제로 출력은 아래처럼 나옴

Driving at 100 km/h
Bike cruising at 20 km/h
Scooter zooming at 30 km/h


```

정리를 해보면

- Car, Bike, Scooter는 각각 전혀 다른 타입
-  그런데 이 세 타입은 모두 Drivable 프로토콜을 채택하고 있음
	 (즉, drive() 라는 함수가 공통적으로 구현되어 있음)
- 그래서 Swift는 이들을 Drivable타입으로 묶어서 배열에 넣을 수 있음 
  
``` swift
	let vehicle: [Drivable] = [...]
```
요런 느낌임...

- 이 배열을 for vehicle in vehivles로 반복할 때,
  -> Swift는 vehicle이 정확히 어떤 타입인지는 몰라도,
	-> Drivable 프로토콜을 따른다는 건 알고 있으니,
		-> 그냥, drive()를 호출해도 알아서 각 타입의 구현이 실행됨

정리하면 프로콜의 다형성 성질 때문에

=="한 줄의 코드로 다 다룰 수 있게 되는 것==






3)  코드 재사용성과 테스트 용이성
-> 공통된 인터페이스를 기준으로 동작을 나눌 수 있기 때문에, 테스트나 기능 확장이 쉬워짐

``` swift
func testDrive(vehicle: Drivable) {
    print("Test driving...")
    vehicle.drive()
}

let car = Car(speed: 100)
let bike = Bike(speed: 25)

testDrive(vehicle: car)
testDrive(vehicle: bike)
```

어떤 부분에서 재사용성이 되고 대단한 건지 자세히 보자!

```swift
func testDrive(vehicle: Drivable) {
    print("Test driving...")
    vehicle.drive()
}

여기서 자세히 보면 

이 함수는 Drivable이라는 프로토콜 타입 하나만 받도록 정의 되어 있음.
다시 말해, Car, Bike, Scooter, 또는 나중에 추가될 어떤 타입이든,
Drivable만 채택하고 있으면 다 사용 가능

```

만약 새로운 타입 을 추가 한다면??

``` swift

struct Bus: Drivable {
    var speed: Int
    func drive() {
        print("Bus zooming at \(speed) km/h")
    }
}
let bus = Bus(speed: 80)
testDrive(vehicle: bus) // 아무 수정 없이 사용 가능!
```

WoW 함수를 한 번만 정의하면...어떤 타입이든 프로토콜만 채택하면 재사용 가능!!!

이게 대단한거임 왜?? 좀 더 드릴링을 해보면!!

스프트웨어 개발에서는 '아무 수정 없이 된다'는 사실 진짜 엄청난 일임!!

- 확장(Extension)이 가능하면서도 기존 코드에 영향을 받지 않는다는 거임!!!!!
-> textDrive() 는 Car나 Bike만 염두에 두고 만든 게 아니야...
그런데 Bus 라는 새로운 타입이 생겼는데도,
textDrive() 코드 한줄도 수정 안하고 그냥 작동함.


이런 행위를 OCP(Open-Closed-Principle . 개발-페쇄 원칙)이라고 함...
-> 확장에는 열려 있어야 하고, 수정에는 닫혀 있어야 한다.
==다시말해, 새로운 타입 추가는 가능하면서 기존 코드는 그대로임 이게 WOW 인거임==
- 수정 없이 확장 가능해야 한다는 것!

이걸 만약에 프로토콜 없이 만들면 ....

``` swift
func testDrive(car: Car) { car.drive() }
func testDrive(bike: Bike) { bike.drive() }
func testDrive(scooter: Scooter) { scooter.drive() }
func testDrive(bus: Bus) { bus.drive() }
```


진짜 ..마더.....xx이지 않음???

- 새로운 탈것이 생길 때마다 함수를 계속 추가하거나 수정해야 함!
- 코드는 계속 불어나고, 테스트도 복잡해지고, 버그 가능성도 증가함
- 의존성도 높고, 유지보수가 힘들어짐!!
  
  이걸 프로토콜로 하면..
  
``` swift
func testDrive(vehicle: Drivable) { vehicle.drive() }
```

이렇게 한줄로 느낌있게 정리가 가능






## iOS 에서 실제로 프로토콜이 사용 되는 예시

1. View (이건 사실 너무 익숙한데 잘 모름.....)

struct ContentView: View 이거임 ㅋㅋㅋ
-> 이 구조체는 View 프로토콜을 따른다!! 는 뜻임

``` swift
protocol View {
    associatedtype Body: View
    var body: Self.Body { get }
}
```
실제 View는 위와 같은 코드로 생겨 있음 (Swift UI 프레임워크 안에서 이미 선언되어 있는 프로토콜 임)

-  SwiftUI에서는 **모든 화면 요소**가 View여야 해.
- 그 View는 반드시 body라는 속성을 가져야 하고,
- 그 안에 또 다른 View를 리턴해야 함.

위 양식을 따르면 아래와 같음

```swift
struct MyView: View {
    var body: some View {
        Text("안녕")
    }
}
```

MyView는 View 프로토콜을 따르며, 반드시 body 속성을 구현해야 한다. 는 약속을 지킨 것임!

그럼 ..왜 처음에 View 로 이렇게 프로토콜 선언을 할까??

1. 통일된 방식으로 화면 구성
-> 모든 UI 요소가 View니까, 조립하듯 붙이기 쉽다

2. 자동 랜더링이 가능함
-> body를 Swift UI가 호출해서 자동으로 화면을 업데이트함

3. 간결함 & 가독성
-> Text, 를 쓰든 ..VStack, HStack를 쓰든 SwiftUi가 알아서 View 조합하고 동작 시킴 (개꿀....)






자 그럼 실제로 다른 어떤  프로토콜이 있는지 알아보자!!!


## 동등성 비교 (Equatable)

Equatable 프로토콜을 채택하면 2개의 인스턴가 같은지 (==) . 다른지 (=!)를 비교 할 수 있음

- Equatable 의 역할

→ == 연산자를 직접 정의할 수 있음

→ 두 개의 값이 같은지를 비교하는 기능을 제공

```swift
struct Person: Equatable {
    let name: String
    let age: Int

    static func == (lhs: Person, rhs: Person) -> Bool {
        return lhs.name == rhs.name && lhs.age == rhs.age
    }
}

let person1 = Person(name: "Chanuk", age: 25)
let person2 = Person(name: "Chanuk", age: 25)
let person3 = Person(name: "Donghee", age: 30)

print(person1 == person2) // true
print(person1 == person3) // false

1)
원래 이런 코드가 있다고 가정 한다면 ..
위에 Equatable을 채택하면 자동적으로 Equatable을 저장 채택 하게 되므로
위의 == 연산자 정의를 생략해도 된다. ""

2)
구조체, 열거형의 경우 프로토콜 채택시 모든 저장 속성 (열거형은 모든 연관값)이 
Equatable 프로토콜을 채택한 타입이라면 메서드 자동구현 (클래스는 직접 구현)

struct Person: Equatable {
    let name: String
    let age: Int
}

let p1 = Person(name: "A", age: 30)
let p2 = Person(name: "A", age: 30)
print(p1 == p2) // true (자동으로 동작)

요렇게 해도 된다.
```



- 실제 사용 예시
  
1) Set , Dictionary 내부 비교
   
``` swift
let person = Person(id: 1, name: "Chanuk")
if personSet.contains(person) {
    print("이미 존재하는 유저입니다")
}
```

Set.contains, Dictionary.keys 등에서 내부 요소 비교에 사용

2) 단순 비교 로직
   
``` swift
if user1 == user2 {
    print("같은 유저입니다")
}
```




## 대소 비교 (Comparable)

→Comparable 프로토콜을 채택하면, < , ≤ , > , >= 연산자를 사용 가능

- Comparable의 역할

→ 값의 크기 비교 기능 제공

→ sort() , min(), max() 같은 정렬 관련 메서드에서 사용 가능

```swift
struct Player: Comparable {
    let name: String
    let score: Int

    static func < (lhs: Player, rhs: Player) -> Bool {
        return lhs.score < rhs.score
    }
}

let player1 = Player(name: "Chanuk", score: 100)
let player2 = Player(name: "Donghee", score: 200)

print(player1 < player2)  // true (100 < 200)
print(player1 > player2)  // false (100 > 200)

let players = [player1, player2].sorted()
print(players)  // score 기준으로 정렬됨

1) Comparable 프로토콜 채택시 열거형 , 구조체, 클래스의 모든 저장 속성 (열거형은 모든 연관값)
이 Comparaable 프로토콜 채택한 타입이라도 메서드 직접 구현해야함

-> 
Comparable 프로토콜을 채택하면 반드시 < 연산자를 직접 구현해야 합니다.
왜냐하면, Swift는 < 연산자를 자동으로 제공하지 않기 때문입니다.

즉, Swift에서는 Comparable을 채택하기만 해서는 작동하지 않고, < 연산자를 직접 정의해야 합니다.
하지만 < 연산자 하나만 정의하면 >, <=, >=는 자동으로 제공됩니다.
(순서 비교가 필요하므로, 정렬 방법에 대한 구체적 구현이 필요)

[ Comparable을 채택하는 것만으로는 < 연산자가 자동으로 생성되지 않음! ] 

- static func < (lhs: Player, rhs: Player) -> Bool을 직접 구현

한번 < 연산자를 구현하면 , >, <= , >= 는 자동 제공됨.
-> Swift가 < 연산자를 기반으로 나머지 연산을 유추할 수 있기 때문.

ex) Swift는 타입의 크기 비교 방식을 알아서 결정 x
따라서, Player 구조체에 score라는 속성이 있다고 해도, Swift는 이 score를 기반으로 정렬해야
한다는걸 모름
그래서 우리가 < 연산자를 직접 구현해서 어떤 기준으로 크기 비교를 할지 정의 해야 함

```

- 자동으로 동작하는 Comparable 예외적인 경우

Swift 5.3 이후부터는 모든 저장 속성이 Comparable을 따르는 경우 자동으로 < 연산자를 제공해 줌

```swift
struct Point: Comparable {
    let x: Int
    let y: Int
}

let p1 = Point(x: 1, y: 2)
let p2 = Point(x: 2, y: 3)

print(p1 < p2)  // 자동으로 비교 가능!

위 코드를 볼떄 Point가 x, y 가 모두 Int 타입인데, Int는 이미 Comparable을 따르고 있기 떄문
에 < 연산자가 자동으로 제공
```


- 실제 사용 예시

1) 우선순위 비교
``` swift
if task1.priority > task2.priority {
    print("task1이 먼저 실행됨")
}
```
-> 게임 케릭터 능력치, 정리된 목록 등에서 사용 가능



2) SwiftUI Picker 등 정렬이 필요한 UI

``` swift
Picker("나이", selection: $selectedAge) {
    ForEach(ages.sorted()) { age in
        Text("\(age)")
    }
}


여기서 Comparable이 없지만 배열 안의 Element가 Comparable을 따라야지만 이 함수를 쓸 수 있음.
Int 자체가 Comparable 프로토콜 자동 반영 하게 된다.

but 
let ages = [30, 25, 40]
let sortedAges = ages.sorted()  // OK
```

둘다 구조체를 만들 때 거의 기본처럼 붙이는 프로톨콜 이다.


------------------------------------------------------------------------
(이 부분은 Hashable 값 공부를 하면서 추가적으로 설명 진행 예정)







## 해시값 생성 (Hashable)

→ 객체를 Dictionary의 키 또는 Set 의 요소로 사용할 수 있도록 고유한 해시 값을 생성하는 프로토콜

1. Hashable을 채택하면 고유한 해시 값을 생성해야 함
2. 구조체 및 열거형의 경우 모든 저장 속성이 Hashable을 따를 경우 자동으로 구현
3. 클래스는 반드시 직접 구현해야 함
4. hash(into:) 메서드를 사용하여 해시 값을 생성
   

```swift
struct User: Hashable {
    let id: Int
    let name: String
}

// 자동으로 Hashable 구현됨
var userSet: Set<User> = []
userSet.insert(User(id: 1, name: "Chanuk"))
userSet.insert(User(id: 2, name: "Donghee"))
userSet.insert(User(id: 1, name: "Chanuk")) // 중복으로 추가되지 않음

print(userSet.count) // 2

```

여기에 hash(into:) 를 통해 여러 속성을 조합하여 고유한 해시 값을 생성 가능

```swift
struct User: Hashable {
    let id: Int
    let name: String

    func hash(into hasher: inout Hasher) {
        hasher.combine(id)
        hasher.combine(name)
    }
}

1. 해시 값을 직접 생성하는 역할
2. Hasher라는 객체를 이용해서 어떤 속성을 기반으로 해시값을 만들지 결정함

hasher.combine(id) → id를 해시 값에 포함
hasher.combine(name) → name도 해시 값에 포함
즉, id와 name을 조합하여 고유한 해시 값을 생성함!

(직접 구현해야 하는 경우 -> 자동으로 생성되는 해시 값이 비효율적이거나
특정한 기준이 필요할 때)
```