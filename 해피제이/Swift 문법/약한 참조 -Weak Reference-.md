# **약한 참조를 학습하게 된 계기**

이번 AR 윷놀이 앱 프로젝트에서 ARView를 다른 클래스인 Coordinator 내부에서 사용해야 할 상황이 생겼다.

그런데 ARView는 시스템적으로 생성되고 소유되는 뷰이기 때문에, 이를 강한 참조(strong reference)로 보관할 경우 **메모리 누수나 순환 참조 문제가 발생할 수 있다**는 점을 고려해야 했다.

이러한 상황을 안전하게 처리하기 위해 weak 키워드를 사용하게 되었고, 자연스럽게 **Swift에서의 약한 참조(weak reference)** 개념을 학습하게 되었다.

특히 Coordinator 클래스 내부에서 다음과 같이 선언된 코드:

```
private weak var arView: ARView?
```

를 통해, **약한 참조(weak reference)**가 어떤 상황에서 사용되는지, 왜 옵셔널 타입이어야 하는지, 참조 대상이 해제되면 어떻게 되는지 등에 대해 학습하게 되었고, 이 개념이 **ARC 기반의 메모리 관리 흐름에서 매우 핵심적인 역할을 한다**는 점을 알게 되었다.

이 글은 위의 계기를 바탕으로 Swift의 weak에 대해 제대로 이해하기 위한 정리이다.

---
# **1. 약한 참조란?**

Swift는 ARC(Automatic Reference Counting)를 통해 객체의 메모리를 자동으로 관리한다.

객체는 **참조 카운트(reference count)**가 0이 되면 메모리에서 해제된다.

하지만 두 객체가 서로를 강하게 참조하면, 참조 카운트가 0이 되지 않는 **순환 참조(Retain Cycle)**가 발생하여 메모리 누수가 발생한다.

이를 해결하기 위한 대표적인 방법이 **약한 참조(weak reference)**이다.

weak로 선언된 참조는 객체를 **참조하되 소유하지 않기 때문에** 참조 카운트를 증가시키지 않으며, 대상 객체가 해제되면 자동으로 nil로 설정된다.

---

# **2. weak의 문법 및 실제 사용 예 (프로젝트 코드 기반)**

```
final class Coordinator: NSObject {
    private weak var arView: ARView?  // 약한 참조
    ...
}
```

이 코드에서는 Coordinator가 ARView를 직접 참조하지만, 소유하지는 않는다.

만약 strong으로 선언하면 ARView → Coordinator → ARView의 **순환 참조**가 발생할 수 있다.

따라서 weak로 선언하여,

- ARView가 시스템에 의해 해제될 때
- Coordinator가 그 객체를 **더 이상 붙잡고 있지 않도록** 하고
- **자동으로 nil이 되게 만들어** 메모리 누수를 방지한다.

---

# **3. 왜 weak는 옵셔널이어야 하는가?**

weak로 선언된 변수는 참조 대상이 메모리에서 해제되면 ARC가 자동으로 nil을 대입한다.

이 때문에 weak 변수는 **반드시 옵셔널**로 선언해야 한다.

예시:

```
weak var delegate: SomeDelegate? // 올바른 사용
```

non-optional로 선언할 경우, 해제된 객체에 접근하려고 할 때 런타임 오류가 발생하므로 컴파일 타임에서 에러를 발생시킨다.

---

# **4. 실전에서 weak를 꼭 써야 하는 이유 (Coordinator의 맥락)**

ARKit에서 ARView는 시스템에서 관리하는 뷰이고, 이를 임의로 메모리에 유지하거나 해제하는 것은 바람직하지 않다.

따라서 Coordinator가 ARView를 strong으로 소유할 경우:

- 앱에서 ARView를 제거하려 해도 제거되지 않음 (→ 메모리 누수)
- Coordinator가 해제되지 않고 남아 있음 (→ 감지 계속 발생, 불필요한 CPU 사용)

반면 weak로 선언할 경우:

- ARView가 먼저 해제되면 arView는 자동으로 nil이 되어 이후 참조하지 않게 됨
- Coordinator의 생명 주기를 시스템이 자연스럽게 제어할 수 있음

---

# **5. 클로저 내 weak self의 의미 (메모리 안전성을 위한 캡처 전략)**

Coordinator 클래스의 startMonitoringMotion() 메서드 내부에서는 CoreMotion의 실시간 센서 업데이트를 클로저를 통해 수신하고 있다:

```
motionManager.startDeviceMotionUpdates(to: .main) { [weak self] motion, error in
    guard let self, let motion = motion else { return }
    ...
}
```

이 코드에서 [weak self]는 **ARC와 클로저가 동작하는 방식 때문에 반드시 필요**하다. 그 이유를 단계별로 정리하면 다음과 같다:

---

### **1. 클로저는 기본적으로 강한 참조로 캡처한다**

Swift에서 클로저는 **외부 변수**를 자동으로 **강한 참조(strong reference)**로 캡처한다.

즉, 클로저 내부에서 self를 참조하면, 그 self 객체를 클로저가 **잡고 있는 상태가 된다**.

> 이건 마치 “A가 B를 참조하고 있고, B도 A를 붙잡고 있어서 둘 다 메모리에서 사라지지 않는” 순환 고리가 생긴 것과 같다.

---

### **2. Coordinator는 motionManager를 통해 클로저를 등록하고 있음**

```
motionManager.startDeviceMotionUpdates(...)
```

이 메서드는 **센서가 멈추지 않는 한 계속 실행되는 클로저**를 등록한다.

그리고 이 클로저 내부에서 self.throwYuts()와 같이 Coordinator의 인스턴스를 참조하고 있다.

즉, 클로저가 self를 잡고 있고, self는 motionManager를 잡고 있으며, 이 motionManager는 클로저를 소유하고 있다.

> 이 구조는 사실상 Coordinator → motionManager → 클로저 → self(Coordinator) 순환 참조가 생긴 것이고,
> 
> **메모리 해제가 불가능**

---

### **3. 이를 방지하려면 [weak self]로 캡처해야 한다**

[weak self]를 사용하면, 클로저 내부에서 self를 **약한 참조**로 캡처한다.

이 말은 곧:

- Coordinator가 더 이상 사용되지 않을 경우, ARC가 정상적으로 메모리에서 해제할 수 있고
- 클로저 내부의 self 참조는 자동으로 nil이 되어 이후 실행되지 않게 된다

즉, **클로저가 객체의 생명 주기를 인위적으로 늘리지 않도록 안전 장치를 두는 것**이다.

---

### **4. 왜 guard let self = self가 필요한가?**

weak self는 옵셔널이다.

따라서 클로저가 호출되는 시점에 Coordinator 인스턴스가 이미 해제되었다면, self는 nil일 수 있다.

이 때문에 옵셔널 바인딩이 필요하다:

```
guard let self, let motion = motion else { return }
```

> 이 코드는 “만약 self가 여전히 살아있고, motion 값도 있다면 실행하라”는 뜻이며,
> 
> **불필요한 접근을 방지하여 앱 안정성을 높인다.**

---

### **5. 한 문장으로 요약**

> 센서 업데이트처럼 지속적이고 강한 생명 주기를 가진 클로저 안에서 self를 참조할 경우에는, 반드시 [weak self]를 사용하여 순환 참조를 방지해야 한다.