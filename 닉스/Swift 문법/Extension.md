기존 타입의 **소스 코드를 수정하지 않고도** 새로운 기능을 붙일 수 있음

### 1. **기존 코드 수정 없이 기능 확장(타입 확장)**
- 외부 라이브러리나 시스템 타입에도 적용 가능

```swift
// 기존 타입 String을 확장해서 새로운 기능 추가
extension String {
    var isEmoji: Bool {
        return self.unicodeScalars.first?.properties.isEmoji ?? false
        //문자열이 비어 있지 않고, 첫 번째 유니코드 스칼라가 이모지이면 true,
        //그렇지 않으면 false를 반환
    }
}

// 사용 예시
let dog = "🐶"
print(dog.isEmoji) // true
```

### 2. **기능별 코드 분리 (코드 조직화)**
```swift
struct HelloView: View {
    var body: some View {
        VStack {
            helloText()
            helloButton()
        }
    }
```

```swift
// 기능 분리
extension HelloView {
    func helloText() -> some View {
        Text("Hello World")
    }

    func helloButton() -> some View {
        Button("push") {
            print("버튼 눌렀음")
        }
    }
}
```

- 화면의 구성 요소들을 `body`에 모두 작성하지 않고 기능별로 나눔

### 3. **프로토콜 준수를 extension으로 분리 가능**
```swift
struct MyData: Identifiable {
    let id = UUID()
    let name: String
}

// 프로토콜 채택을 extension으로 분리
extension MyData: CustomStringConvertible {
    var description: String {
        return "이름은 \\(name)입니다"
    }
}

// 사용 예시
let item = MyData(name: "Nyx")
print(item.description)  // 이름은 Nyx이입니다
```
- `CustomStringConvertible`이라는 프로토콜을 따르는 기능을 따로 분리

| 목적                | 코드 요약                                       | 설명                    |
| ----------------- | ------------------------------------------- | --------------------- |
| 기존 코드 수정 없이 기능 확장 | `extension String`                          | 새로운 기능 붙이기            |
| 기능별 코드 분리         | `extension HelloView`                       | View 안에 UI 요소를 정리     |
| ✅프로토콜 준수 분리       | `extension MyData: CustomStringConvertible` | 프로토콜 채택을 따로 모아서 보기 쉽게 |

## 실제 활용 예시

### SwiftUI에서 View 확장
1. 폰트
```swift
extension Font { // 나눔손글씨 하나손글씨 Nanum_HanaHandwriting
    static let HanaHandWriting16: Font = .custom("나눔손글씨 하나손글씨", size: 16)
    static let HanaHandWriting17: Font = .custom("나눔손글씨 하나손글씨", size: 17)
    static let HanaHandWriting20: Font = .custom("나눔손글씨 하나손글씨", size: 20)
}
```

```swift
Text("Hello")
    .font(.HanaHandWriting16)
```

1. 터치포인터
```swift
struct TouchEffect: Identifiable {
    let id: UUID
    let position: CGPoint
    var isAnimating: Bool = false
}
// 터치 하나를 나타내는 데이터 모델입니다.
// SwiftUI의 ForEach에서 반복하려면 각 항목이 Identifiable해야 하므로 id가 필요함
// position: 터치 위치
// isAnimating: 현재 애니메이션 중인지 표시하는 플래그

struct SparkleTouchEffectContainer<Content: View>: View {
    let content: Content
    @State private var touches: [TouchEffect] = []
    @State private var hasTouched = false

    init(@ViewBuilder content: () -> Content) {
        self.content = content()
    }

    var body: some View {
        ZStack {
            content
                .contentShape(Rectangle()) // 터치 영역 지정
                .simultaneousGesture( // 터치 감지
                    DragGesture(minimumDistance: 0) // 아주 작은 드래그도 감지
                        .onChanged { value in
                            if !hasTouched {
                                addTouch(at: value.location)
                                hasTouched = true
                            }
                        } // 처음 터치할 때만 반응
                        .onEnded { value in
                            addTouch(at: value.location) // 드래그 끝 위치에서 반짝임
                            hasTouched = false
                        } // 손을 뗐을 때도 반응
                )
						// ForEach: 터치 위치마다 Image("touchEffect3")을 표시
            ForEach(touches) { touch in
                Image("touchEffect3")
                    .resizable()
                    .frame(width:40, height: 20)
                    .position(touch.position)
                    .transition(.opacity)
                    .animation(.easeOut(duration: 0.2), value: touch.id)
            }
        }
    }

    private func addTouch(at location: CGPoint) {
        let newTouch = TouchEffect(id: UUID(), position: location)
        touches.append(newTouch)
        DispatchQueue.main.asyncAfter(deadline: .now() + 0.2) {
            touches.removeAll { $0.id == newTouch.id }
        }
    }
    // 새로운 터치 데이터를 추가하고 0.2초 후에 제거 → 잠깐만 화면에 나타났다가 사라짐
}

// 화면을 터치했을 때 이펙트가 나오는 효과를 만들어주는 ViewModifier 정의
// ViewModifier로 구현해서 기존 View에 .sparkleTouchEffect()만 붙이면 효과 적용되도록 함
struct SparkleTouchEffect: ViewModifier {
    func body(content: Content) -> some View {
        SparkleTouchEffectContainer {
            content
        }
    }
}

// 뷰 확장 함수
extension View {
    func sparkleTouchEffect() -> some View {
        self.modifier(SparkleTouchEffect())
    }
}

```

```swift
@main
struct MingguejeokApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
                .sparkleTouchEffect() // <앱 전역에 설정하려면 이 위치에 호출
        }
        .modelContainer(for: [MainGoal.self])
    }
}
```

+) modal에서는 따로 설정해줘야함
```swift
ScrollStack{ ... }
	.sheet(isPresented: $onSubGoalCreateModal) {
		SubGoalCreateModal(subGoals: $mainGoal.subGoals)
			.sparkleTouchEffect() // <이 위치에서 호출
	}
```


** 주의

| 저장 프로퍼티 추가 불가    | `extension`에서는 `var name: String = ""` 같은 저장 프로퍼티 추가 불가 |
| ---------------- | ------------------------------------------------------- |
| 기존 기능 재정의 불가     | 기존 메서드를 override 하거나 덮을 수 없음                            |
| private 멤버 접근 불가 | 타입 외부에서는 private 멤버 접근이 제한됨                             |
- `extension`은 **기존 타입의 정의 바깥에서 덧붙이는 것**이라 메모리 구조를 바꿀 수 없음
- “기능 추가" 목적이지, “기능 수정”을 위한 도구가 아님! 수정하려면 **상속이나 타입 내부 수정**을 써야 함
- 타입의 **외부**에서 정의되기 때문에, `private`으로 선언된 프로퍼티나 메서드에는 접근불가!! 근데 같은 파일 내의 fileprivate은 접근 가능