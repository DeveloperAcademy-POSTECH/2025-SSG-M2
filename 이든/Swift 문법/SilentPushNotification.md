>[!question]
>GQ1. SilentPushNotification란?
>GQ2. SilentPushNotification 어디에서 사용하나 ? 

## Description
- Silent Push Notification은 사용자에게 별도의 알림, 소리, 배지 등 어떠한 시각적 표시 없이 디바이스와 앱에 전달되는 푸시 메시지 방식입니다.
#### 특징
	iOS 와 안드로이드 
	동작방식 : content-available | data
	조건 : UIBackgroundModes | 기본적으로 동작, 자체 리시버 작성시 UI 표시 생략 필요 
	사용 예 : 콘텐츠 갱신, 비동기 데이터 수집, 앱 상태 추적 | 데이터 동기화, 상태 확인, 사용자 행동 데이터 수집 

##### Xcode 프로젝트 설정 
- Background Modes 활성화 
	- Xcode의 Signing  & Capabilities에서 Background Modes를 추가 후 , Remote notifications 체크 
- Push Notifications 활성화 
	- 마찬가지로 Push Notifications capability 추가 
- 알림 권한 등록 
	- 일반 푸시 알림과 동일하게 앱에서 알림 수신 권한 요청 필요 
```swift
import UIKit

@main 
class Appdelegate : UIResponder, UIApplicationDelegate {
	func application(_ application : UIApplication,
									didFinishLaunchingWithOptions launchOptions:
[UIApplication.LaunchOptionsKey: Any]?) -> Bool { 
	UIApplication.shared.registerForRemoteNotifications()
	return true
	}
}
```

AppDelegate에서 처리
	•	핸들러 메서드 구현
	•	Silent Push를 수신하면 아래 메서드가 호출됩니다.
```swift 
func application(_ application: UIApplication,
                 didReceiveRemoteNotification userInfo: [AnyHashable: Any],
                 fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
    // 백그라운드 작업(데이터 동기화, 서버 요청 등)
    if let value = userInfo["customData"] {
        // 데이터 분석 및 작업
    }
    completionHandler(.newData)
}

```

- 작업 완료 후 반드시 completionHandler 를 호출, 약 30초 이내에 백그라운드 작업을 마쳐야 합니다. 

## 주요 기능
+ 백그라운드 앱 데이터 업데이트
+ 사용자에게 알림 표시 없이 작업 수행 
+ 서버-클라이언트 동기화 및 실시간 기능 지원
+ 핸들러에서 자유로운 백그라운드 작업 기능
+ 유저 경험 및 효율성 개선 

## 코드 예시
+ 설정 및 권한 요청 
```swift
import HealthKit

let healthStore = HKHealthStore()
let readTypes: Set = [HKObjectType.quantityType(forIdentifier: .stepCount)!]

healthStore.requestAuthorization(toShare: nil, read: readTypes) { (success, error) in
    // 권한 처리
}
```

```swift
func application(_ application: UIApplication,
                 didReceiveRemoteNotification userInfo: [AnyHashable: Any],
                 fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {

    let healthStore = HKHealthStore()
    let stepsType = HKQuantityType.quantityType(forIdentifier: .stepCount)!

    // 최근 하루의 걸음 수 읽어오기
    let start = Calendar.current.startOfDay(for: Date())
    let predicate = HKQuery.predicateForSamples(withStart: start, end: Date())
    let query = HKStatisticsQuery(quantityType: stepsType, quantitySamplePredicate: predicate, options: .cumulativeSum) { (_, result, error) in
        guard let result = result, let sum = result.sumQuantity() else {
            completionHandler(.failed)
            return
        }
        let stepCount = sum.doubleValue(for: HKUnit.count())
        // stepCount를 서버로 전송하는 네트워크 요청(비동기 처리)
        // 서버 요청 완료 후 아래와 같이 처리
        completionHandler(.newData)
    }
    healthStore.execute(query)
}

```
- 앱이 백그라운드에 있어도 사용자의 운동 데이터, 수면 데이터, 바이탈 기록등을 주기적으로 서버와 동기화 
- 의료기관, 피트니스 서비스 등이 사용자 건강 상태 변화를 신속하게 파악
---
- Silent Push를 사용하면 사용자는 별도의 알림을 받지 않고 건강 데이터가 최신 상태로 관리됨 
 - 단, HealthKit 데이터 접근 권한과 Background Modes(`Remote notifications`)가 필수이며, 네트워크 요청과 데이터 처리는 `fetchCompletionHandler` 내에서 ***30초*** 이내에 완료해야 함
### 저희 앱이 헬스Kit 사용하는 앱 이자나여 ? 
-> 근데 이 망할 헬스킷때문에, 지금 아무것도 못하게 생겼음
위에 소개했듯이 silentpush 알림을 사용하려 했지만, 저희 일광시간을 가져오기엔,,,, 시간이 너무 짧고 그리고 앱을 닫아버리면 주기적으로 가져올 수가 없답니다..
C4.. 포기하고싶다..

## Keywords
+ 파생된 키워드들을 작성

## References
- Perplexity 
-