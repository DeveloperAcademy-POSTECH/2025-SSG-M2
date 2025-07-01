> Apple의 음성 비서 **Siri와 앱을 연결**할 수 있게 해주는 프레임워크

### SiriKit을 이루는 2가지 구성요소
**Intents(의도)** : Siri가 사용자의 명령을 파악한 “의도” 정보(INIntent)
**Intent Extension** : 앱이 해당 의도를 처리하는 실행 코드
> > Intent : 사용자가 말하거나 요청한 행동을 앱이 이해할 수 있게 만든 형식화된 요청
** Xcode에서 `.intentdefinition` 파일을 만들어 다음을 정의한다

| 항목         | 예시                                                    |
| ---------- | ----------------------------------------------------- |
| Intent 이름  | `OrderCoffeeIntent` (어떤 행동을 의미하는지)                    |
| Parameter  | `coffeeType: String`, `size: String`, `quantity: Int` |
| 응답 메시지     | “아이스 라떼 1잔을 주문했어요.”                                   |
| Siri 노출 여부 | “Siri로 이 작업 실행 가능하게 할 것인지” 설정                         |

### Siri 사용 예시
"OO앱으로 커피 주문해줘"
"오늘 일정 추가해줘"
"타이머 시작해줘"
"차 문 잠가줘"
"메시지 보내줘"

### SiriKit이 지원하는 기능 영역

| 도메인              | 예시              |
| ---------------- | --------------- |
| **메시지**          | 메시지 보내기, 읽기     |
| **VoIP 통화**      | 전화 걸기, 받기, 기록   |
| **Ride booking** | 택시 호출, 취소       |
| **결제/지불**        | 송금, 잔액 조회       |
| **운동/건강**        | 운동 시작, 기록, 종료   |
| **음악/미디어 제어**    | 음악 재생, 일시정지 등   |
| **카 커맨드**        | 차 문 열기/닫기, 시동 등 |
| **예약/주문**        | 레스토랑 예약, 음식 주문  |
| **타이머/알람**       | 설정, 수정, 삭제 등    |

### 동작흐름
1. 사용자: "Siri야, OO앱으로 차 문 잠궈줘"
2. Siri → Apple Intent 처리 시스템에서 해당 도메인 매칭
3. 앱의 Intent Extension이 호출됨
4. 앱이 요청 처리 후 응답 반환 (예: "차 문을 잠갔습니다")

### 개발 구성 요소

| 요소                         | 설명                                                 |
| -------------------------- | -------------------------------------------------- |
| **Intent 정의**              | `.intentdefinition` 파일로 Siri가 이해할 수 있는 의도와 파라미터 정의 |
| **Intent Extension**       | 의도를 받아 처리하는 Swift 코드 (예: 차 문 잠금 처리)                |
| **Siri 권한 설정**             | `Info.plist`에 `IntentsSupported` 추가                |
| **App Groups**             | 본앱 ↔ Extension 간 데이터 공유 시 필요                       |
| **Siri UI Extension (선택)** | Siri에서 커스텀 응답 UI (예: 커피 이미지 등) 보여주고 싶을 때 사용        |

### 코드 예시
Intent 정의 파일 (`CoffeeOrderIntent.intentdefinition`)에서 다음 의도 정의:
```
Intent 이름: OrderCoffee
매개변수: 커피 종류, 사이즈, 수량
```

Intent 처리 코드:
```swift
final class OrderCoffeeIntentHandler: NSObject, OrderCoffeeIntentHandling {
    func handle(intent: OrderCoffeeIntent, completion: @escaping (OrderCoffeeIntentResponse) -> Void) {
        let coffee = intent.coffeeType ?? "아메리카노"
        let size = intent.size ?? "중간"
        // 주문 처리 로직
        let response = OrderCoffeeIntentResponse.success(coffee: coffee, size: size)
        completion(response)
    }
}
```

### SiriKit 주의사항
도메인 고정 : Apple이 지정한 Intents만 사용 가능 (Custom Intent 제한적)
SiriKit은 백그라운드에서 동작 : 일반 앱 UI는 SiriKit으로 직접 제어할 수 없음
App Review 제한 : SiriKit 연동 기능은 리뷰에서 테스트 대상이 될 수 있음
→ 테스트 대상
1. Siri 명령이 제대로 동작하는가? : "앱으로 주문해줘" 시 작동
2. 앱이 올바르게 Intent를 처리하는가? : 응답 메시지가 SiriKit 규칙에 맞는가?
3. 민감한 정보 접근이 있는가? : SiriKit으로 개인정보를 노출하는가?

### 예제>>>
## SiriKit Swift 프로젝트 예제 (커피 주문 앱 만들기)
> Siri에게 "커피 주문해줘"라고 말하면, 앱에서 커피 종류와 수량을 받아 주문을 처리

---

### 📁 Step 1. `.intentdefinition` 파일 만들기
Xcode에서 `New File → SiriKit Intent Definition File` 추가

### ✅ Intent 내용 정의
Intent Name: `OrderCoffeeIntent`
Parameters:
- `coffeeType`: String (required)
- `size`: String (optional, default = medium)
- `quantity`: Int (default = 1)

⚙️ **Intent 옵션 설정**
- `Handling class` 자동 생성 체크
- Category: Custom

### 📁 Step 2. Intent Extension 생성
Xcode → `File > New > Target > Intents Extension` 선택
![image.png](attachment:ded184b8-22e5-4af9-88b8-3eeb37e5f5d5:image.png)
예: `CoffeeOrderIntentHandler.swift`

### ✅ 처리 클래스 코드
```swift
//SiriKit에서 제공하는 Intent 관련 타입들 (INIntent, IntentResponse, Handler 등)을 쓰기 위해 import

import Intents

//커피 주문 Intent를 실제로 처리하는 클래스
class OrderCoffeeIntentHandler: NSObject, OrderCoffeeIntentHandling {
		//NSObject : iOS 시스템과의 호환성 유지 (필수)
		//OrderCoffeeIntentHandling : OrderCoffeeIntentHandler 클래스가 특정 Intent(OrderCoffee)를 처리할 수 있도록 약속된 프로토콜 + Xcode에서 .intentdefinition 파일로 생성된 프로토콜

		//handle(...) : 사용자가 Siri에서 "커피 주문해줘"라고 했을 때 실제로 호출되는 함수
		//completion : 결과를 Siri에게 비동기로 알려주는 클로저
    func handle(intent: OrderCoffeeIntent,
                completion: @escaping (OrderCoffeeIntentResponse) -> Void) {

        let coffee = intent.coffeeType ?? "아메리카노"
        let size = intent.size ?? "중간"
        let quantity = intent.quantity?.intValue ?? 1

        // 주문 로직, 응답 객체 생성
        let response = OrderCoffeeIntentResponse.success(
            coffee: coffee,
            size: size,
            quantity: NSNumber(value: quantity)
        )
        completion(response)
        // Siri에게 응답을 넘겨 마무리
        // 이걸 호출하지 않으면 Siri가 사용자의 요청이 완료되지 않은 것으로 판단함

    }
}

```

### ✅ IntentHandler.swift
```swift
class IntentHandler: INExtension {
    override func handler(for intent: INIntent) -> Any {
        return OrderCoffeeIntentHandler()
    }
}
```
`IntentHandler`는 SiriKit Extension의 **메인 클래스**
`INExtension`은 Apple이 제공하는 **SiriKit 기반 Intent Extension의 베이스 클래스 >>** 이 클래스는 Siri가 의도를 처리할 수 있도록 필요한 인터페이스를 제공함
SiriKit은 앱의 메인 실행 코드가 아니라 **확장(Extension)으로 작동함!!** 그래서 이런 별도의 `IntentHandler` 클래스가 필요함

## ** 전체 동작 흐름 요약
```
사용자: "커피 주문해줘"
→ Siri: OrderCoffeeIntent 생성
→ SiriKit: IntentHandler.handler(for:) 호출
→ 리턴된 OrderCoffeeIntentHandler에서 handle(intent:) 실행
→ 커피 종류, 사이즈, 수량 파악 → 응답 반환
```