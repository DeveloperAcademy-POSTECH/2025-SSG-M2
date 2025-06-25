### **Property Wrapper란?**

- 반복되는 프로퍼티 관련 로직을 **재사용 가능하고 깔끔하게 분리**할 수 있도록 도와주는 기능
- 주로 **저장 프로퍼티에 부가적인 동작이나 제약을 추가**할 때 사용

### **주로 사용되는 Property Wrapper**

|카테고리|대표 래퍼|용도|
|---|---|---|
|**뷰 상태 관리**|`@State`, `@StateObject`, `@Binding`, `@ObservedObject`|상태 저장, 공유, 참조|
|**환경/저장소**|`@Environment`, `@EnvironmentObject`, `@AppStorage`, `@SceneStorage`|시스템/앱 설정 공유|
|**데이터 관찰**|`@Published`|값이 바뀔 때 뷰에 알림|
|**포커스 및 애니메이션**|`@FocusState`, `@Namespace`|UX 개선용 상태 추적|
|**커스텀 래퍼**|직접 구현 (`@Clamped`, `@UserDefault` 등)|재사용 가능한 값 처리 로직|

- 원하는 목적에 맞는 프로퍼티 래퍼를 직접 만들 수도 있음
- SwiftUI에서는 이미 `@State`, `@Binding`, `@ObservedObject` 같은 내장 래퍼를 제공함

### **C2에서 사용한 Property Wrapper**

@State @Binding @Environment @Bindable

### **@State vs @Binding**

|구분|`@State`|`@Binding`|
|---|---|---|
|목적|**뷰 내부 상태**를 저장하고 관리|**다른 뷰에서 상태를 전달받아 수정**|
|데이터 소유권|**자기 자신이 소유**함 (State의 원본)|**다른 뷰의 상태를 참조**함 (State의 복사 아님)|
|수정 가능 여부|자신이 소유한 값이므로 수정 가능|바인딩된 값이므로 수정 가능 (단, 원래의 값을 수정하는 것)|
|사용 예|Toggle 상태, 입력값 등 뷰 내부에서만 사용하는 상태|자식 뷰에 상태를 전달하고, 자식 뷰에서 그 값을 변경해야 할 때|

### **EX)**

@State example

```swift
struct CounterView: View {
    @State private var count = 0

    var body: some View {
        VStack {
            Text("Count: \\\\\\\\(count)")
            Button("Increase") {
                count += 1
            }
        }
    }
}

```

- count는 이 뷰가 직접 관리하는 상태
- 외부에서 접근하거나 공유할 수 X

@Binding example

```swift
// 부모 뷰
struct ParentView: View {
    @State private var isOn: Bool = false  // 상태의 소유자

    var body: some View {
        VStack {
            Toggle("Parent Toggle", isOn: $isOn)
            ChildView(isOn: $isOn)  // 바인딩 전달
        }
    }
}

```

```swift
// 자식 뷰
struct ChildView: View {
    @Binding var isOn: Bool  // 바인딩된 상태를 받아옴

    var body: some View {
        Toggle("Child Toggle", isOn: $isOn)
    }
}

```

- ParentView는 @State로 isOn상태를 직접 관리
- 실제 데이터는 여전히 부모뷰인 ParentView가 소유하고 있고, ChildView는 @Binding으로 이 값을 공유받아 사용함
- 값을 변경하면 부모의 상태가 함께 바뀜
- @Binding은 상태를 전달받는 자식뷰에 써야함
- **@Binding은 상태를 직접 만들 수 없음 (자식뷰에서 `@Binding var isOn = false` 이렇게 쓰면 에러남)**
- 부모뷰에 @Binding을 쓰면 에러남

### **cf. public와 private**

|구분|역할|
|---|---|
|`@State`, `@Binding`|프로퍼티의 **데이터 관리 방식**을 정의|
|`public`, `private`, `internal`, `fileprivate`|코드가 **접근 가능한 범위(scope)** 를 제어**(접근 제어자 access modifier)**|

- @State 속성은 무조건 private로 선언해야함 → 외부에서 직접 접근하거나 수정하면 SwiftUI의 상태 추적이 깨질 수 있음
- @Binding은 외부에서 값을 주입받는 개념이므로 외부 접근을 허용해야 할 수도 있음(internal 또는 public 가능)

@Binding → 값을 외부에서 주입받아 공유하는 속성(값을 소유하지 않음)

public → 이 속성을 모듈 외부에서 접근 가능하게 허용하는 접근 제어자

### **Q. @Binding은 최상위 뷰로부터 값을 전달받고 public은 외부에서의 접근을 허용하는 접근 제어자인데 어떻게 같이 사용할 수 있을까?**

```swift
public struct CustomView: View {
    @Binding public var isOn: Bool

    public init(isOn: Binding<Bool>) {
        self._isOn = isOn
    }

    public var body: some View {
        Toggle("Toggle", isOn: $isOn)
    }
}

```

이때 `CustomView(isOn: $someBool)`로 뷰를 생성하면

- `CustomView` 내부에서는 `isOn` 값을 읽고 쓸 수 있음
- `CustomView`를 한 번 생성한 뒤에는 `isOn`에 직접 접근해서 다시 바꾸는 건 불가능

`public var isOn: Binding<Bool>` (or `@Binding public var isOn`)

- 외부에서 `CustomView`를 초기화할 때 바인딩 값을 넘길 수 있음
- 외부에서 접근해서 주입 가능하지만, 뷰가 한 번 만들어지고 나면 SwiftUI 특성상 직접 값을 바꾸지는 않음

### **@Environment**

시스템이 제공하는 환경 값(ex. 다크모드, 폰트 사이즈 등)을 주입받음

```swift
@Environment(\\\\\\\\.colorScheme) var colorScheme

```

### **@Bindable**

SwiftData 모델 인스턴스를 SwiftUI에서 양방향 바인딩 가능하게 하는 속성 래퍼

```swift
@Bindable var goal: Goal

```

- 일반 클래스나 구조체에는 사용할 수 없음
- 뷰에서 모델을 @Bindable로 선언할 때, 보통은 상위 뷰에서 모델 인스턴스를 넘겨줘야함

인스턴스란?

```swift
class Person {
    var name: String

    init(name: String) {
        self.name = name
    }
}

let person1 = Person(name: "해리")   // 인스턴스
let person2 = Person(name: "말포이")   // 또다른 인스턴스

```

### @Bindable 없이 모델 속성을 수정할 때 vs @Bindable을 사용할 때

모델 정의 (SwiftData @Model)

```swift
import SwiftData

@Model
class Goal {
    var title: String
    var isCompleted: Bool

    init(title: String, isCompleted: Bool = false) {
        self.title = title
        self.isCompleted = isCompleted
    }
}

```

@Bindable 없이 뷰 구현

```swift
import SwiftUI

struct GoalEditorView: View {
    @ObservedObject var goal: Goal  // @Bindable 대신 @ObservedObject 사용

    var body: some View {
        VStack {
            TextField("목표 제목", text: Binding(
                get: { goal.title },
                set: { newValue in
                    goal.title = newValue
                }
            ))
            .textFieldStyle(.roundedBorder)

            Toggle("완료", isOn: Binding(
                get: { goal.isCompleted },
                set: { newValue in
                    goal.isCompleted = newValue
                }
            ))
        }
        .padding()
    }
}

```

- 직접 `Binding`을 만들어줘야 함 (`get`과 `set` 클로저를 수동으로 작성)
- 코드가 길어지고 반복적임

@Bindable로 뷰 구현

```swift
import SwiftUI
import SwiftData

struct GoalEditorView: View {
    @Bindable var goal: Goal // @Bindable 속성 래퍼 사용

    var body: some View {
        VStack {
            TextField("목표 제목", text: $goal.title) // $goal.title: 바인딩으로 바로 접근
                .textFieldStyle(.roundedBorder)

            Toggle("완료", isOn: $goal.isCompleted)
        }
        .padding()
    }
}

```

- `Binding`을 수동으로 만들 필요 없고, `$goal.title`처럼 직관적인 문법으로 바인딩 가능
- 코드가 간결하고 가독성 좋음!