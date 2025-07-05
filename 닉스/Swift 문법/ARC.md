# ARC(Automatic Reference Counting)
객체가 더 이상 필요하지 않을 때 메모리 누수가 발생하지 않도록 메모리를 **자동으로 해제**해주는 시스템
**** Swift는 ARC를 중심으로 메모리를 관리하는 몇 안 되는 최신 언어**

## ✅ 메모리 누수란?
> 앱이 더 이상 필요 없는 데이터를 메모리에서 버리지 못하고 계속 갖고 있어서 메모리가 낭비되는 현상

Swift는 **ARC(Automatic Reference Counting)**라는 시스템이 “이 객체(예: 사람, 뷰, 데이터)가 몇 명에게 참조되고 있는지”를 세어보고, **더 이상 아무도 쓰지 않으면 메모리에서 제거한다.**
그런데 어떤 경우에는 **A가 B를, B가 다시 A를 참조하고 있는 구조**가 생겨서 **서로 놓지를 못하고 둘 다 메모리에서 지워지지 않는** 일이 생김! 이걸 **강한 참조 순환(Strong Reference Cycle)**이라고 함

## ✅ ARC 작동 방식
1. 클래스 인스턴스를 생성하면 **참조 횟수 = 1**
2. 다른 변수에 할당되면 **참조 횟수 +1**
3. 참조를 제거하면 **참조 횟수 -1**
4. 참조 횟수가 0이 되면 **해제(deinit 호출)**

## ✅ 예제
```swift
class Person {
    let name: String

    init(name: String) {
        self.name = name
        print("\\(name) is initialized")
    }

    deinit {
        print("\\(name) is deinitialized")
    }
}

var person1: Person? = Person(name: "Nyx")
var person2: Person? = person1

person1 = nil  // 참조 횟수 1 (person2)
person2 = nil  // 참조 횟수 0 → deinit 호출됨
```

## ✅ ARC가 적용되는 대상
class : 참조타입이라 ARC 적용됨
❌ struct : 값 타입이라 ARC 영향 없음
❌ 열거형 : 값 타입이라 ARC 영향 없음

## ✅ 강한 참조 순환 (Strong Reference Cycle) 문제
> 두 객체가 서로를 참조하면 서로의 참조 횟수가 0이 되지 않아 메모리가 해제되지 않음

### 🔸 문제 상황
```swift
class Person {
    var pet: Pet?
    deinit { print("Person deinit") }
}

class Pet {
    var owner: Person?
    deinit { print("Pet deinit") }
}

var yujin: Person? = Person()
var cat: Pet? = Pet()

nyx?.pet = cat
cat?.owner = nyx

nyx = nil   // ❌ Person deinit 호출 안 됨
cat = nil     // ❌ Pet deinit 호출 안 됨
```

## ✅ 해결 방법: `weak`, `unowned`

| `weak`    | 참조는 하되, 참조 횟수를 올리지 않음. **옵셔널만 가능**       | 순환 참조 방지, 객체가 해제될 수 있음     |
| --------- | ---------------------------------------- | -------------------------- |
| `unowned` | 약한 참조지만 **비옵셔널**, 참조 대상이 해제되면 **런타임 에러** | 참조 대상이 더 오래 살아 있음 확실할 때 사용 |

### 🔸 수정 예
```swift
class Pet {
    weak var owner: Person?
}
```

## ✅ `weak self`가 필요한 이유
Swift에서 `class`는 참조 타입이고, ARC는 클래스 인스턴스의 **참조 횟수(reference count)**를 추적해서 메모리를 관리한다. 그런데 **클로저는 주변 값을 캡처**할 수 있기 때문에 **self를 강하게 캡처(strong capture)**하면 다음과 같은 **강한 순환 참조(Strong Reference Cycle)**가 발생할 수 있음

### 🔸 문제 예시: 메모리 누수 발생
```swift
class ViewModel {
	    var name = "nyx"

    func loadData(completion: @escaping () -> Void) {
        DispatchQueue.global().asyncAfter(deadline: .now() + 1) {
            print(self.name)  // ❌ self를 클로저가 강하게 참조
            completion()
        }
    }

    deinit {
        print("ViewModel deinitialized")
    }
}

var vm: ViewModel? = ViewModel()
vm?.loadData { print("Done") }
vm = nil  // ❌ ViewModel이 해제되지 않음 → 메모리 누수 발생
```
- 클로저가 `self`를 강하게 참조하면서 `vm`이 `nil`이 되어도 인스턴스가 해제되지 않음

## ✅ 해결 방법: `weak self`로 약한 참조
```swift
DispatchQueue.global().asyncAfter(deadline: .now() + 1) { [weak self] in
    guard let self = self else { return }
    print(self.name)
    completion()
}
```
- `self`를 **약한 참조(weak)**로 캡처해서, 외부 인스턴스가 먼저 해제되면 `self == nil`이 되고,
    메모리는 정상적으로 해제됨.
    

## ✅ Combine에서 ARC 문제와 `weak self`
Combine은 비동기 이벤트 처리 프레임워크
Combine에서도 클로저가 자주 쓰이기 때문에 **`sink`, `map`, `assign` 등에서 self를 캡처할 때도 주의**해야함

### 🔸 Combine 예시
```swift
class ViewModel: ObservableObject {
    @Published var count = 0

    init() {
        Timer.publish(every: 1, on: .main, in: .common)
            .autoconnect()
            .sink { [weak self] _ in
                self?.count += 1  // ✅ 메모리 누수 방지
            }
            .store(in: &cancellables)
    }

    var cancellables = Set<AnyCancellable>()

    deinit {
        print("ViewModel deinit")
    }
}
```
- `sink` 내부에서 `self`를 강하게 참조하면 → 뷰모델이 해제되지 않음
- `weak self`로 안전하게 메모리 누수를 방지

## ✅ SwiftUI와 ARC
SwiftUI는 상태를 **struct + @State / @ObservedObject / @StateObject 등으로 관리**하지만, 여전히 내부적으로 **참조 타입(ViewModel 등)**을 사용할 수 있어 ARC가 적용된다

### 🔸 예시
```swift
struct ContentView: View {
    @StateObject var viewModel = MyViewModel()

    var body: some View {
        Text("Count: \\(viewModel.count)")
    }
}
```
- SwiftUI 뷰는 값 타입(struct)이지만 ViewModel은 class라서 ARC 대상 → Combine 연산자 사용 시 `weak self` 필요


** 클로저 안에서 `self`를 쓰면 **항상 weak/unowned 써야 할까?**
→ **self가 메모리 누수 원인이 될 수 있다면 YES!** (특히 `[weak self]` → `guard let self = self else { return }` 패턴)

### ✅ `weak self`와 ARC 관계 요약
클로저에서 self : 기본적으로 **strong capture** (순환 참조 위험)
`weak self` : 순환 참조를 **방지**, 해제 가능
Combine : `sink`, `assign`, `map` 등에서 자주 사용 → 반드시 `weak self` 고려
SwiftUI : 뷰는 struct지만 ViewModel은 class → ARC와 관련 있음
메모리 누수 방지 : `weak` or `unowned` 사용하여 클로저가 인스턴스를 붙잡지 않도록 조심해야 함