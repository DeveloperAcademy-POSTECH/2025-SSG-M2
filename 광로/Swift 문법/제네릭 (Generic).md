>[!question]
>GQ1. 제네릭 뭐야?
>GQ2. 어디에 쓰는거야??

## Description

- 제네릭 (Generic)
-> "어떤 타입이 들어올지 모르지만, 나중에 정해질 타입을 미리 설정해두는 것"

ex) GPT가 아주 쉽게 설명해줌...예시가 너무 좋아서 가지고 옴


1) 컵을 만들고 싶음
2) 그런데 물, 주스, 커피 뭐가 들어올지 몰라.
3) 그렇다고 물 전용 컵, 주스 전용 컵, 커피 전용 컵 따로 만들면 비효율임??
   -> 그래서 **“어떤 음료든 받을 수 있는 컵”**을 만들고,
   -> ==**실제 사용할 때 타입(물, 주스, 커피)을 정하는 것**==이 제네릭이야.

## 주요 기능

+ 사실 Swift의 기본 타입이 이미 제네릭으로 만들어져 있음
``` swift
let names: [String] = ["Chanu", "Joon"]         // Array<String>
let scores: [Int: String] = [1: "A", 2: "B"]     // Dictionary<Int, String>
let maybeValue: Optional<Double> = 3.14          // Optional<Double>}

그럼 왜 제네릭로 해야 함?
-> 간단함 안에 들어가는 타입(String, Int, Double이 다를 수 있기 때문)
-> 추가로 다양한 타입을 안전하게, 타입체크하며 사용하려고 제네릭이 쓰이는 거임
```



## 코드 예시
``` swift

func printInt(value: Int) {
    print(value)
}

func printString(value: String) {
    print(value)
}

	이렇게 하면 Int나 String마다 함수가 따로 필요하기 때문에..코드가 길어지고 중복됨 ...
```

만약 제네릭으로 바꾸면

``` swift

func printValue<T>(value: T) {
    print(value)
}
T는 "타입의 자리표시자"임 
-> 어렵게 생각 하지 마셈 그냥 자리 표시 하려면 앞뒤로 <T> 쓰면됨
그러면 알아서 타입 감지 해줌...

따라서 printValue는 어떤 타입이든 받을 수 있음
나중에 호출할 때 타입이 자동으로 결정 됨...

예시로..

printValue(value: 123)       // Int
printValue(value: "Hello")   // String

알아서 타입이 결정됨 

```


==실제 우리가 사용 하게 될때==

- Reusable SwiftUI View 만들기
-> SwiftUI에서는 화면이 전부 View 타입이니깐, 어떤 뷰든 담을 수 있어야 함

이게 참 중요함 우리가 가장 많이 쓰는 부분중 하나!

** 컴포넌트를 재사용하려면 내부에 어떤 View가 들어올지 유연해야 함**

만약, 버튼을 하나 만들 건데 그안에 Text, Image등등 어떤게 들어 올지 모름 ..

이럴떄 매번 다르게 만들면 재사항 하기 불편 따라서
-> 아래와 같은 제니릭 Content: View를 씀


``` swift
struct CustomButton<Content: View>: View {
    let action: () -> Void
    let content: () -> Content
    
    var body: some View {
        Button(action: action) {
            content()  // 여기에서 어떤 View든 받아서 보여줌!
        }
    }
}

이 코드를 설명하면

Content는 자리표시자 -> 어떤 View타입이 들어 올수 있음
다만 SwiftUI View 프로토콜을 따르는 타입만 들어 올 수 있음

content: () -> Content
-> 외부에서 View를 클로저로 넘겨 줄 수 있음


이걸 실제 사용한 코드로 변경 하면


// 1. 텍스트를 넣은 버튼
CustomButton(action: {
    print("Tapped")
}) {
    Text("클릭!")
}

// 2. 이미지를 넣은 버튼
CustomButton(action: {
    print("이미지 버튼 클릭")
}) {
    Image(systemName: "heart.fill")
        .foregroundColor(.red)
}

// 3. 복잡한 구조도 가능!
CustomButton(action: {
    print("복합 버튼 클릭")
}) {
    HStack {
        Image(systemName: "photo")
        Text("사진 업로드")
    }
}

이렇게 되는거임

CustomButton 하나만 으로 Text, Image, HStack 등 다양한 버튼 만들 수 있음


```

## Keywords
+ 프로토콜

## References