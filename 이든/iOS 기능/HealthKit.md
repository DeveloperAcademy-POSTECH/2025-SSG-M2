>[!question] 
> GQ1. HealthKit 이란? 
> GQ2. HealthKit 이외에 어떤 관련 Kit 들이 있나 ?  
> GQ3. 어디에 사용할 수 있을까? 

## HealthKit 
| **항목**      | **설명**                                                                         |
| ----------- | ------------------------------------------------------------------------------ |
| **주요 목적**   | **iPhone/Apple Watch**에서 수집한 건강 데이터를 중앙 저장소에 저장하고, 여러 앱에서 **공유 및 활용**할 수 있도록 함 |
| **도입 시기**   | iOS 8.0+                                                                       |
| **지원 플랫폼**  | iOS, iPadOS, watchOS, 일부 visionOS/macOS                                        |
| **데이터 종류**  | 걸음 수, 심박수, 수면, 식이, 혈압, 운동 기록 등 100+ 항목                                         |
| **개발자 접근성** | 가능 (단, 사용자 동의 필요) info.plist !                                                 |
| **주의사항**    | Info.plist에 사용 목적 설명 필수 / 민감한 데이터는 심사 시 주의                                     |
##### 수면 분석 앱 “ 스노어 랩 “

직접 사용해봤던 앱인데, 사용성이 굉장히 좋았던 경험이 있셈 ! 무료 체험 가능하니 한 번 사용해 보는 것도 좋을듯 !
### HealthKit 의 활용 및 개발 

##### 앱 간 데이터 공유 : 
사용자가 허용하면 여러 앱이 HealthKit을 통해 데이터를 읽고 쓸 수 있음.
###### 주요 개발 클래스 : 
- `HKHealthStore`: HealthKit 데이터베이스와 상호작용하는 핵심 객체
- `HKObjectType`, `HKSampleType`, `HKQuantityType`: 저장 가능한 데이터 유형 정의
- `HKQuery`: 데이터 쿼리 및 통계 작업
---
##### ResearchKit
- 설명 : iOS 앱에서 설문, 동의서, 활동 기반 테스트 등을 손쉽게 만들 수 있도록 설계된 프레임워크

2. 주요 특징
    - 동의서, 설문 조사, 활동 과제
3. 주요 기능
    - 동의서 수집 (연구에 필요한 서류 작성, 시각적 설명, 서명 포함 검토 단계 구성)
    - 설문 조사 UI (질문형 설문 구성)
    - 활동 테스트 (실제 iPhone 센서를 활용해 사용자 행동 측정)
        - 보행 분석, 음성 녹음, 손가락 두드리기, 균형 테스트, 기억력 테스트, 심박수 테스트

##### CareKit
1. **기본 개요**
    - 설명 : 사용자의 **건강 관리 및 치료 계획을 추적하고 공유할 수 있게 해주는 오픈소스 프레임워크**
        - 환자 중심 앱을 만들 때 유용하며, 의사/병원과의 연동을 통해 **자기 관리 + 피드백 루프**를 구성 가능
2. 주요 기능
    - Task 관리 - 복약, 운동, 스트레칭, 재활 등 건강 루틴 만들고 추적
    - 리포트 기록 - 증상, 통증, 기분 등 사용자 상태 기록 (그래프화 가능)
    - 의료진 공부 - API 또는 CloudKit 연동으로 데이터 공유 가능
        - 별도로 구현해야함!

## WWDC 2025에서 발표된 HealthKit 주요 업데이트 정리

## **Medications API**

- **신규 API 추가**: HKUserAnnotatedMedication 및 HKMedicationConcept 타입이 도입됨으로써 처방약, 복용 일정, 별명 등의 데이터 읽기/쓰기가 가능해졌어 .
- **앱 활용 예시**: 약물 추적, 복용 알림, 복용 빈도 분석 등 **정교한 약 관리 UI**와 **헬스리포트 생성**에 활용할 수 있어.
- **요구사항**: 개별 약물 객체에 대한 사용자 권한 요청 ([requiresPerObjectAuthorization](https://www.notion.so/requiresPerObjectAuthorization-21d2b843d5af80ec9a3bd5af2aff10dc?pvs=21)())이 필요함 .

---

## **수면 단계(Sleep Stages) 지원**

- Apple Watch가 **REM, Core, Deep** 수면 단계를 자동으로 감지하고, 이를 HealthKit(sleepAnalysis)을 통해 앱에서 읽거나 저장할 수 있게 됨 .
- 개발자용 **Predicate API**가 추가되어, 예를 들어 REM 단계의 수면 기간만 쉽게 쿼리 가능함 .

---

## **Mental Wellbeing API 강화**

- 전년도 WWDC24에 소개됐던 **GAD‑7·PHQ‑9** 설문형 우울/불안 지표와 **State of Mind**(기분 상태) API들이 계속해서 강화되고 있음 .
- 이 데이터는 일기, 명상, 감정 추적 앱 등에서 활용 가능하며, 사용자 상태에 대한 문맥 기반 기록이 가능함 .

---

## **VisionOS 지원 확대**

- HealthKit이 **visionOS** (Apple Vision Pro)에서 정식 지원됨 .
- **공간 기반 UX**: 입체 차트, 공간 창 구성, Guest User 환경에서의 보건 데이터 처리 등이 가능함 .

---

## **기타 요약**

- Swift의 async/await 쿼리 지원으로 데이터 접근이 더욱 **간편하고 비동기적으로 변경됨** .
- 워크아웃 기능 강화로 **커스텀 인터벌 기반 운동**, Apple Watch 운동 미러링, 고주기 모션 데이터 접근이 더 효율적으로 개선됨 .

##### requiresPerObjectAuthorization
requiresPerObjectAuthorization은 HealthKit에서 특정 데이터 타입(특히 Medication 등)에 대해 "객체별(per-object)"로 접근 권한을 요청해야 하는지 여부를 나타내는 프로퍼티 또는 메서드입니다.

---
## **주요 특징 및 동작 방식**

- **Per-Object Authorization**: 일반적인 HealthKit 데이터(예: 걸음수, 심박수 등)는 데이터 타입 단위로 권한을 요청하지만, Medication(약물) 데이터처럼 민감하거나 개별 항목별로 관리가 필요한 경우에는 "객체별"로 권한을 요청합니다. 즉, 사용자가 Health 앱에 등록한 약물 각각에 대해 앱이 접근 권한을 별도로 받아야 합니다.

[Meet the HealthKit Medications API - WWDC25 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2025/321/)

[WWDC 2025 - Meet the HealthKit Medications API](https://dev.to/arshtechpro/wwdc-2025-meet-the-healthkit-medications-api-1d23)

- **API 사용 예시**:

```swift
swiftlet medicationType = HKUserAnnotatedMedicationType()
healthStore.requestPerObjectReadAuthorization(for: medicationType) { success, error in
    *// 권한 요청 결과 처리*
}
```

이 방식으로 요청하면, 사용자는 Health 앱에 등록된 약물 목록 중에서 앱에 공유할 약물을 직접 선택할 수 있습니다[2](https://dev.to/arshtechpro/wwdc-2025-meet-the-healthkit-medications-api-1d23)3.

- **자동 권한 관리**: 사용자가 새 약물을 Health 앱에 추가하면, 앱은 해당 약물에 대한 권한을 새로 요청해야 하며, 이미 승인된 약물만 접근할 수 있습니다[1](https://developer.apple.com/videos/play/wwdc2025/321/)[2](https://dev.to/arshtechpro/wwdc-2025-meet-the-healthkit-medications-api-1d23)3.
- **반환 값**:
    - **`requiresPerObjectAuthorization()`** 메서드는 해당 데이터 타입이 per-object 권한이 필요한 경우 **`true`**를 반환합니다. 예를 들어, 약물(Medication), 심전도(Electrocardiogram) 등 일부 데이터 타입이 이에 해당합니다[4](https://docs.rs/objc2-health-kit/latest/objc2_health_kit/struct.HKElectrocardiogramType.html).

## **요약**

- requiresPerObjectAuthorization은 HealthKit 데이터 타입이 "객체별 권한"이 필요한지 확인하는 메서드/프로퍼티입니다.
- Medication 등 일부 민감 데이터는 per-object 권한이 필요하며, 사용자가 개별 항목별로 앱 접근을 허용/거부할 수 있습니다[1](https://developer.apple.com/videos/play/wwdc2025/321/)[2](https://dev.to/arshtechpro/wwdc-2025-meet-the-healthkit-medications-api-1d23)3[4](https://docs.rs/objc2-health-kit/latest/objc2_health_kit/struct.HKElectrocardiogramType.html).
- 이 기능을 활용하면 사용자의 프라이버시와 데이터 통제권을 더욱 강화할 수 있습니다.