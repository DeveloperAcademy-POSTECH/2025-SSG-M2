>[!question]
>GQ1. CareKit은 어떻게 사용하는건가 !
>GQ2. CareKit과 같이 사용할 수 있는 Kit은 뭐가 있지 ? 

## Description
- CareKit은 Apple이 개발한 오픈 소스 건강 관리 앱 개발 프레임워크로, 사용자가 스스로 증상, 복약, 치료 계획 등을 기록·관리할 수 있는 iOS 앱을 쉽게 만들도록 설계되었습니다.
## 주요 기능
+ 일단 CareKit 을 사용하려면 , 개발자계정이 무족권 있어야됩니다. (애플 쪼잔)
+ 그리고 info.plist에서도 health와 관련된 것들을 사용하겠다는 텍스트를 넣어주셔야 됩니다. 
![[스크린샷 2025-08-10 오후 10.06.46.png]]
- 이렇게 말이죠 ! 여기서 끝난게 아니랍니다 ! 
![[스크린샷 2025-08-10 오후 10.07.23.png]]
- Code Signing에서도 CareKit.entitlements를 설정해줘야 ! 건강 앱 권한 요청이 가능합니다.

---
##### 이제 찐찐찐막
- 파일 - Add Packages . . .  머시기 클릭 - careKit 검색 후 Add Package -  Target 설정
![[스크린샷 2025-08-10 오후 10.08.55.png]]
- 제가 이걸 몰라서,, 2시간을 낭비 했으니, 여러분들은 하지마세요.. 그냥 헬스킷 하지마..
- 자 그럼 이제 코드 예시들을 살펴봅시다 ! 
---
## CareKit의 주요함수 및 핵심 개념 
-  **Task (OCKTask)**: “무엇을, 언제” 할 건지(예: 혈당 측정). 스케줄을 가짐
- **Event(Occurrence)**: 스케줄에 의해 **날짜·시간별로 펼쳐진 단위 실행**(오늘 08:00 측정 같은 한 번의 실행)
-  **Outcome (OCKOutcome)**: Event의 **결과 데이터**(수치, 체크 여부 등)
-  **OutcomeValue (OCKOutcomeValue)**: Outcome 안의 **실제 값**(Double, String 등 + units, kind)
***Task -> Event -> Outcome 이 흐름 !***

### 저장소(스토어)
- **OCKStore**: 기본 로컬 스토어(메모리/파일 기반).
    
- **OCKCDStore**: Core Data 기반(클라우드 동기화 계획이 있으면 이걸 고려).
    
- 공통적으로 **CRUD**와 **쿼리** API를 제공.
```swift
let store = OCKStore(name: "MyCareStore")
```
- 스케줄 만들기
- 해당 코드는 제가 헬스케어 스터디에 곧 발표 할 주제인 혈당을 기록하는 간단한 앱입니다.
```swift
// 하루 1회(자정)
let daily = OCKSchedule.dailyAtTime(
  hour: 0, minutes: 0, start: startOfDay, end: nil, text: "혈당 측정"
)

// 하루 여러 회
let times = OCKSchedule.dailyAtTimes(
  [DateComponents(hour: 8), DateComponents(hour: 12), DateComponents(hour: 18)],
  start: startOfDay, end: nil, text: "혈당 측정"
)

// 주 단위
let weekly = OCKSchedule.weeklyAtTime(
  weekday: .monday, hour: 9, minutes: 0, start: startOfDay, end: nil, text: "주간 점검"
)
```
- Task만들기 / 조회(중요함수)
```swift
// 생성
var task = OCKTask(id: "bloodGlucose", title: "혈당 측정", carePlanUUID: nil, schedule: daily)
task.impactsAdherence = true                    // 완료율 계산에 반영
let created = try await store.addTask(task)

// 조회(특정 ID)
var tq = OCKTaskQuery()
tq.ids = ["bloodGlucose"]
let tasks = try await store.fetchTasks(query: tq)

// 조회(특정 날짜에 “유효한” Task)
let todayTasks = try await store.fetchTasks(query: OCKTaskQuery(for: Date()))

```
- Event(Occurrence) 조회 (중요 ! ! )
	- Outcome은 항상 Event에 귀속되어야 합니다.
```swift
var eq = OCKEventQuery(for: Date())         // 오늘
eq.taskIDs = ["bloodGlucose"]
let events = try await store.fetchAnyEvents(query: eq)

guard let event = events.first else { throw MyError.noEvent }
let occurrenceIndex = event.scheduleEvent.occurrence // ← 이 인덱스가 핵심!
```

## 코드 예시
```swift
let startOfDay = Calendar.current.startOfDay(for: Date())
let schedule = OCKSchedule.dailyAtTime(hour: 0, minutes: 0, start: startOfDay, end: nil, text: "혈당을 기록하세요")
```
- 해당 코드에서 보시면 OCKSchedule를 사용하는데, 이 코드는 하루 1회 발생하는 Task를 만들어줍니다 ! 
```swift
let outcome = OCKOutcome(taskUUID: …, taskOccurrenceIndex: 0, values: [outcomeValue])
```
- 해당 코드는 Outcome 저장의 핵심인 occurrence 인덱스입니다.
- saveBloodGlucoseOutcome은 위 코드와 같이 고정으로 taskOccurrenceIndex: 0 을 사용합니다 . 
- taskOccurrenceIndex는 스케줄 시작일로부터 발생한 이벤트의 일련 번호입니다 ! 
### 이렇게 하는 이유는 ? 
- CareKit 자체는 Task를 스케줄에 따라 여러 이벤트로 쪼갭니다.
- 예를 들어서 하루 1회 측정 (당 체크)이라면 : -  taskOccurrenceIndex: 0 → 첫날 - taskOccurrenceIndex: 1 → 둘째 날 이런식입니다.
- 이 인덱스는 Task가 시작된 날을 기준으로 날짜별로 증가하기 때문에, 오늘이 꼭 0번째가 아닙니다.
- 고정 0으로 저장해버리면 CareKit 입장에서 첫날 이벤트에 값을 덮어쓰는 꼴이 되버리는거죠 !  그럼 과거 데이터가 변경되겠죠 ? 
- 그리고 CareKit의 체크리스트, 그래프, 진척도 계산은 이벤트 단위로 동작합니다.
	- 오늘의 이벤트에 Outcome이 연결 되어야 
		- 오늘의 체크박스 체크
		- 혈당 그래프 값 표시됨
		- 완료율 반영됨 
----
Outcome 조회 쿼리 ! EventQuery 권장 ! 
- fetchBloodGlucoseOutcomes(from:to:)는 OCKOutcomeQuery를 사용하지만, 실전에서는 보통 **OCKEventQuery로 이벤트 단위 조회**를 권장합니다.
- Event에는 Task/스케줄/Outcome이 모두 연결돼 있어 **날짜별 정렬·UI 반영**이 간편합니다.
```swift
var q = OCKEventQuery(dateInterval: DateInterval(start: startDate, end: endDate))
q.taskIDs = ["bloodGlucose"]
let events = try await store.fetchAnyEvents(query: q)
// events.each: event.outcome?.values, event.scheduleEvent.start/end 로 화면 표시
```
