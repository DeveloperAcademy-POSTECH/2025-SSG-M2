## 1. **프로토콜 (Protocol)**
| `프로토콜`은 **클래스, 구조체, 열거형이 따라야 하는 "규칙"**을 정의하는 타입
어떤 타입이 특정 프로토콜을 채택하면 그 프로토콜이 정의한 메서드나 프로퍼티를 반드시 구현해야 함
```swift
protocol Drivable {
    func startEngine()
    func drive()
}
```
이 프로토콜을 채택하면 아래처럼 메서드를 구현해야 함!
```swift
struct Car: Drivable {
    func startEngine() {
        print("Engine started")
    }

    func drive() {
        print("Car is driving")
    }
}
```

### 왜 필요할까?
- **일관된 인터페이스 보장**: 여러 타입이 동일한 동작을 할 수 있도록 강제함
- **의존성 역전 (DIP)**: 구체적인 타입이 아닌 "기능"에 의존하게 함
- **다형성**: 다양한 객체를 같은 방식으로 처리할 수 있음

## 2. **델리게이트 패턴 (Delegate Pattern)**
> 델리게이트(Delegate) 패턴은 **어떤 객체가 해야 할 일을 다른 객체에게 위임(delegate)** 하는 디자인 패턴
보통 프로토콜을 기반으로 구성되며, 어떤 객체가 프로토콜을 통해 "일을 대신할 대상"을 등록하고, 그 대상이 프로토콜을 채택해 필요한 기능을 구현함

왜 필요할까?
- **역방향 데이터 전달**: 자식 → 부모 뷰로 이벤트 전달
- **책임 분리**: 하나의 객체에 너무 많은 역할을 몰아주지 않음
- **유연성**: 동작을 외부에서 커스터마이즈 가능

### 델리게이트 패턴 구성 요약

|프로토콜 정의|`MyViewDelegate`|어떤 메서드를 구현해야 할지 명시|
|---|---|---|
|델리게이트 변수|`weak var delegate: MyViewDelegate?`|대리자(역할 수행자) 연결|
|호출|`delegate?.somethingDidHappen()`|대리자에게 이벤트 전달|
|프로토콜 채택 및 구현|보통 ViewController|이벤트에 반응하는 코드 작성|

### 예시) 버튼이 눌렸을 때 ViewController에게 알리는 커스텀 뷰
```swift
// 1. 프로토콜 정의
protocol CustomButtonDelegate: AnyObject {
    func buttonWasTapped()
}

// 2. 커스텀 뷰 정의
class CustomButtonView: UIView {
    weak var delegate: CustomButtonDelegate?

    func buttonTapped() {
        // 3. 델리게이트에게 알림
        delegate?.buttonWasTapped()
    }
}
```
```swift
// 4. ViewController에서 델리게이트 채택
class ViewController: UIViewController, CustomButtonDelegate {
    func buttonWasTapped() {
        print("Button was tapped in the custom view!")
    }

    func setup() {
        let customView = CustomButtonView()
        customView.delegate = self
    }
}
```

## ** 정리
||프로토콜|델리게이트 패턴|
|---|---|---|
|개념|기능의 "약속"|"위임"을 통해 일을 분리함|
|목적|인터페이스 통일|이벤트 전달 & 역할 분산|
|연관성|프로토콜 기반으로 동작|항상 프로토콜과 함께 사용|
|주요 용도|기능 설계|자식 → 부모 이벤트 전달 등|


## Q) 언제 쓰면 좋을까?
- View ↔ ViewController 간 통신 (ex. 버튼 누름, 스크롤 이벤트 등)
- TableView, CollectionView처럼 iOS 시스템이 제공하는 델리게이트 패턴 활용 시
- 복잡한 동작을 모듈로 분리하고 싶을 때

# !! 근데 SwiftUI에서는 delegate가 사라짐 !!
> SwiftUI의 설계 철학과 아키텍처가 기존 UIKit과 크게 다름 UIKit은 **객체지향(OOP)** 기반으로 동작함
```swift
class MyViewController: UIViewController, UITableViewDelegate {
    func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        // 셀 선택됨
    }
}
```

- delegate = UIKit 컴포넌트의 이벤트를 외부 객체로 전달하기 위한 방법!!
- UIKit은 명령형(imperative) UI 모델로 구성돼 있어서, 이벤트가 발생할 때마다 개발자가 수동으로 상태를 업데이트해줘야함

→ 다양한 이벤트 처리를 위해 delegate 패턴이 필수…였는데요
## SwiftUI에서는 왜 delegate가 사라졌을까?
> SwiftUI는 UIKit과 달리 선언형(declarative) UI 모델을 사용한다
```swift
List(items) { item in
    Text(item.name)
        .onTapGesture {
            // 이 아이템이 탭됐을 때 처리
        }
}
```
SwiftUI는 **뷰의 상태(state)**와 UI를 선언적으로 연결해 놓음

### 1. **상태 기반 데이터 흐름 (State-Driven UI)**
SwiftUI는 `@State`, `@Binding`, `@ObservedObject`, `@Environment` 등 다양한 상태 속성 래퍼를 사용해서 데이터의 변경이 곧 UI의 변경으로 직결되는 구조
```swift
@State private var isOn = false

Toggle("Switch", isOn: $isOn)
```
- `delegate` 없이도 `@State`가 바뀌면 UI가 자동으로 다시 렌더링됨
- 반응형 프로그래밍 → 별도로 이벤트 처리 객체(delegate)를 둘 필요가 없음!

### 2. **View는 구조체 Struct → 소유관계가 사라짐**
UIKit은 클래스 기반이기 때문에 `delegate`처럼 다른 객체를 참조해서 처리해야 했고,
SwiftUI의 뷰는 대부분 `struct`라서 참조보다 데이터 전달과 바인딩에 집중함
```swift
struct CustomView: View {
    var onComplete: () -> Void   // delegate 대신 클로저를 받음
}
```
- delegate 프로퍼티를 가질 수 없음 (구조체는 참조를 보관하지 않음)
- 대신 클로저로 필요한 로직을 주입 (event handler로 대체)

### 3. **단방향 데이터 흐름**
SwiftUI는 모든 데이터가 위에서 아래로 흐르도록 설계되어 있음
```swift
ParentView
  └── ChildView(data: ..., onTap: { ... })

```
- 뷰는 데이터를 직접 수정하지 않고, 상위 뷰의 바인딩이나 클로저를 통해 동작만 위임
- delegate 같은 양방향 흐름은 SwiftUI에서 지양함

## Q) 그럼 delegate는 완전히 사라졌을까?
아니요!!!!!!! UIKit을 사용할 때는 여전히 필요함!!!
`UIViewRepresentable` 같은 SwiftUI <-> UIKit 브리징 컴포넌트에서는 여전히 delegate를 사용함

ex)
```swift
class Coordinator: NSObject, UITextFieldDelegate {
    func textFieldShouldReturn(_ textField: UITextField) -> Bool {
        ...
    }
}
```

## ** 결론
> SwiftUI는 상태 중심 + 선언형 UI + 함수형
> UIKit에서 쓰던 delegate 패턴은 필요 없어졌고, 대신 클로저, 바인딩, ObservableObject 같은 기능이 대신하고 있음