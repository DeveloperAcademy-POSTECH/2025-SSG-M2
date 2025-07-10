*주요 GQ*
(1) 타입 캐스팅이 뭔가요?
(2) 어디에 쓰는 건가요?

1) 객체에 실제 타입을 확인하거나, 다른 타입으로 변환 하는 것.

```swift
let animal: Animal = Dog()
// Dog인데 Animal로 저장되어 있음 → bark() 못 씀
```
2) 부모 타입 (Any, AnyObject 등)으로 저장된 객체를 자식 타입 기능에 접근할 때
   (또는 다형성 상황에서 실제 타입을 분기 처리할 때)

정리하면
"타입 캐스팅은 '진짜 타입이 뭔지" 확인하고, 필요한 기능을 쓰기 위해 변환하는 도구이다


더 풀어보면

Swift는 타입에 엄격한 언어 인데,
어던 객체가 실제로는 Dog 타입인데 변수에는 Animal 타입으로 저장되어 있으면,
Dog의 기능을 쓰고 싶을 때는 타입을 다시 확인하고 바꿔 주는 작업이 필요하다

``` swift
class Animal {
    func makeSound() {
        print("Some animal sound")
    }
}

class Dog: Animal {
    func bark() {
        print("Woof!")
    }
}

let animal: Animal = Dog()  // Dog인데 Animal 타입으로 저장되어 있음
```

여기를 잘 보면 animal.bark() 하고 싶어도 컴파일 에러가 뜬다!!
why? 
animal은 지금 Animal 타입으로 알려져 있기 때문에 bark()를 모른다.

그래서 타입 캐스팅을 해야함

그걸 하기 위해서 is, as가 있는데...

1) is연산자 - (타입인지 확인만 하기)

```swift
if animal is Dog {
    print("animal은 Dog 타입입니다!")
}
```

- is는 그냥 "이 객체가 Dog 타입이야?" 확인만 해준다.
그리고 타입은 바꾸지 않음


2) as? 연산자 - 조건부 다운캐스팅 (Optional 반환)

```swift
if let dog = animal as? Dog {
    dog.bark()  // 성공하면 bark() 사용 가능
}
```

- as? 는 타입을 바꾸려고 시도함
- 성공하면 Optional에 담겨서 나옴 -> 그래서 if let으로 꺼냄
- 실패하면 nil

3) as! 연산자 - 강제 다운 캐스팅 (Crash 위험)

```swift
let dog = animal as! Dog
dog.bark()
```

- 확실히 Dog 일 때만 써야 함
- 타입이 다르면 앱이 바로 크래시 남

아주 간단하 예시를 사용 했는데, 좀더 깊게 다뤄 보자


```swift
class Animal {
    func makeSound() {
        print("동물이 소리를 냅니다")
    }
}

class Dog: Animal {
    func bark() {
        print("멍멍!")
    }
}

class Cat: Animal {
    func meow() {
        print("야옹~")
    }
}
```

우리는 Animal이라는 부모 클래스를 만들었고,
Dog, Cat는 각각 Animal을 상속한 자식 클래스이다.

이제 다양한 동물들을 Animal 타입으로 저장하면

```swift
let animals: [Animal] = [Dog(), Cat(), Dog(), Animal()]
```

이 배열에는 Dog, Cat 그냥 Animal도 있다.

그런데 어떤 애가 Dog 인지 Cat인지 모름...

이럴 때 타입 캐스팅을 써서 알아보고 특정 기능을 실행 할 수 있음


1) is 예시 - 어떤 동물인지 확인만
2) as?예시 - 조건부 캐스팅에서 기능 실행

```swift
for animal in animals {
    if let dog = animal as? Dog {
        dog.bark()  // "멍멍!" 출력
    } else if let cat = animal as? Cat {
        cat.meow()  // "야옹~" 출력
    } else {
        animal.makeSound()  // "동물이 소리를 냅니다"
    }
}
```

as?는 성공하면 타입을 바꿔서, 자식 클래스의 기능까지 쓸 수 있음
실패하면 nil이므로, 꼭 if let이나 guard let으로 감싸야 한다.



* 타입 캐스팅이 주로 쓰이는 곳*

1. 다형성 (Polymorphism) 상황

부모 타입 배열에 자식 객체들이 섞여 있을때,
-> 실제 타입별로 동작을 다르게 해야 할 때 사용

```swift
let animals: [Animal] = [Dog(), Cat(), Dog()]

for animal in animals {
    if let dog = animal as? Dog {
        dog.bark()
    } else if let cat = animal as? Cat {
        cat.meow()
    }
}
```


1. Any / AnyObject를 타입으로 받을 때
타입이 불분명한 상황에서 정확한 타입으로 바꿔 써야 할 때

```swift
let unknown: Any = "Hello"

if let string = unknown as? String {
    print(string.uppercased())  // "HELLO"
}
```

1. SwiftUI에서 AnyView, View 프로토콜 다룰 때

SwiftUI는 모든 뷰가 some View인데,
동작으로 타입을 맞춰야 할 때 AnyView로 래핑하고 필요하면 타입 확인 & 캐스팅해서 사용

```swift
let view: Any = AnyView(Text("Hello"))

if let anyView = view as? AnyView {
    // 타입이 AnyView인지 확인 후 사용
}
```


이런 경우 들이 있음

마지막 정리하면

"타입이 불확실한 상황에서 진짜 타입을 꺼내어 기능을 쓰기 위해 주로 사용"