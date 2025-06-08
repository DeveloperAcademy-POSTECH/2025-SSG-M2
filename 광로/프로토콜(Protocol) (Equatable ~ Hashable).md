
*프로토콜*
-> 특정 기능을 제공하기 위해 타입이 지켜야 하는 '규칙'의 집합
쉽게 말하면 이 타입은 반드시 이런 기능을 구현해야 한다 와 같은 약속!

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
let person3 = Person(name: "Dong", age: 30)

print(person1 == person2) // true
print(person1 == person3) // false

1)
원래 이런 코드가 있다고 가정 한다면 ..
위에 Equatable을 채택하면 자동적으로 Equatable을 저장 채택 하게 되므로
위의== 연산자 정의를 생략해도 된다. ""

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
let player2 = Player(name: "Dong", score: 200)

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

ex) Comparable을 채택하는 것만으로는 < 연산자가 자동으로 생성되지 않음!

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
userSet.insert(User(id: 2, name: "Dong"))
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