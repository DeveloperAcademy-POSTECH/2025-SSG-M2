>[!question]
>GQ1. CloudKit이란?
>GQ2. CloudKit과 CareKit을 같이 사용하는 방법이 뭐지 ?

## CloudKit이란? 
- 주요특징 ! 
	 iCloud 연동 데이터 저장 : 앱 내 데이터를 iCloud의 컨테이너 단위로 저장합니다.
- 자동 동기화 : iCloud 계정 기반으로 여러 기기 간 실시간 데이터 동기화가 지원됩니다.
- 확장성 및 관리 효율 : 앱별로 별도의 CloudKit 컨테이너를 구성하여 데이터가 혼재되지 않도록 하며, 최대 1테라바이트까지 저장 공간 지원이 가능합니다.
- 오프리안 캐싱 : 어느 정도 오프라인 상태에서도 데이터를 이용할 수 있지만, 동기화는 인터넷 연결이 필요합니다. 
##### 구조적 개념
- CKContainer :앱별 iCloud 데이터 공간을 나타내는 객체
- CKRecord: key-value 쌍으로 이루어진 개별 데이터 레코드
- CKDatabase: Public, Private, Shared 데이터베이스로 구성됨
- CloudKit Zones: 레코드 그룹화 및 효율적 동기화 관리 단위 


## 주요 기능
---

앱 데이터를 iCloud에 저장하여 iPhone, iPad, Mac, 웹 간 자동으로 최신 상태로 동기화할 수 있습니다. 별도의 서버를 구축하지 않아도 안정적인 클라우드 저장과 동기화를 지원합니다.

CloudKit은 세 가지 데이터베이스 유형을 제공합니다. Private Database는 사용자 개인 데이터 저장에 사용되며, 로그인한 사용자만 접근 가능합니다. Public Database는 모든 사용자에게 공개되는 공용 데이터 저장소로, 예를 들어 공개 게시판이나 공통 콘텐츠에 적합합니다. Shared Database는 특정 사용자 간 데이터 공유가 가능한 공간으로, 협업 기능이나 공동 작업에 활용할 수 있습니다.

앱별로 독립된 데이터 저장 공간인 CloudKit Container를 제공하며, 하나의 앱에서 여러 개의 컨테이너를 사용할 수도 있고, 여러 앱이 하나의 컨테이너를 공유할 수도 있습니다.  

CloudKit Zones를 통해 레코드를 논리적으로 그룹화하여, 효율적인 동기화 및 충돌 방지, 선택적 백업 및 복원이 가능합니다. 이를 통해 필요한 구간만 선택적으로 동기화하여 네트워크 부담을 줄일 수 있습니다.

CKSubscription을 활용하면 데이터 변경 사항을 실시간으로 감지하고, 푸시 알림을 통해 사용자에게 빠르게 전달할 수 있어 사용자 경험을 개선할 수 있습니다.

보안 측면에서는 Apple ID 기반의 인증 시스템과 데이터 암호화를 통해 개인정보 및 저장 데이터를 안전하게 보호합니다.

Public Database 기준으로 최대 1페타바이트(PB)까지 저장 공간을 사용할 수 있어, 대용량 서비스에도 유연하게 대응할 수 있습니다.

CloudKit은 Xcode 및 Apple 개발자 도구와의 통합을 지원하여 설정 및 관리가 간편하며, Swift Concurrency(async/await)를 활용해 비동기 작업 구현도 용이합니다.  

웹 기반의 CloudKit Console을 통해 데이터 레코드 관리, 스키마 수정, 사용량 모니터링 등을 직관적으로 수행할 수 있어 운영 효율성을 높일 수 있습니다.

## 코드 예시
```swift
import CloudKit
//데이터 저장 예시
func saveNote() {
    let record = CKRecord(recordType: "Note")
    record["title"] = "테스트 노트" as CKRecordValue
    record["content"] = "CloudKit 저장 예시입니다." as CKRecordValue

    let privateDatabase = CKContainer.default().privateCloudDatabase
    privateDatabase.save(record) { savedRecord, error in
        if let error = error {
            print("저장 실패: \(error.localizedDescription)")
        } else {
            print("저장 성공!")
        }
    }
}
//이 코드는 CloudKit의 CKRecord를 생성하고, 해당 데이터를 사용자의 Private Database에 저장하는 기본적인 예시입니다. CKRecord는 key-value 쌍의 구조로 데이터를 저장하며, CKContainer.default().privateCloudDatabase를 통해 현재 iCloud 계정의 개인 데이터베이스에 접근합니다. 저장 결과는 비동기적으로 처리됩니다.


//데이터 불러오기 예시 
func fetchNotes() {
    let query = CKQuery(recordType: "Note", predicate: NSPredicate(value: true))
    let privateDatabase = CKContainer.default().privateCloudDatabase

    privateDatabase.perform(query, inZoneWith: nil) { records, error in
        if let error = error {
            print("불러오기 실패: \(error.localizedDescription)")
        } else if let records = records {
            for record in records {
                let title = record["title"] as? String ?? "제목 없음"
                let content = record["content"] as? String ?? "내용 없음"
                print("title: \(title), content: \(content)")
            }
        }
    }
}
//이 코드는 “Note”라는 recordType에 해당하는 모든 데이터를 불러오는 예시입니다. NSPredicate(value: true)를 통해 조건 없이 전체 데이터를 조회하며, 결과는 [CKRecord]로 반환됩니다. key를 통해 레코드의 필드 값을 읽을 수 있습니다.

```

```swift
func checkiCloudAccountStatus() async throws -> CKAccountStatus {
    return try await CKContainer.default().accountStatus()
}
// 상태 체크 
```


---
#### CloudKit과 CareKit 연동 

```swift 
import CareKitStore
import CloudKit

// CoreData + CloudKit NSPersistentCloudKitContainer 구성
let container = NSPersistentCloudKitContainer(name: "CareKitModel")

let description = container.persistentStoreDescriptions.first
description?.cloudKitContainerOptions = NSPersistentCloudKitContainerOptions(containerIdentifier: "iCloud.com.example.CareKitDemo")

container.loadPersistentStores { desc, error in
    if let error = error {
        fatalError("CoreData + CloudKit 연동 실패: \(error)")
    }
}

// OCKStore 생성
let store = OCKStore(name: "CareKitModel",
                     type: .cloud,
                     cloudKitContainer: container)
             
//이 코드는 CareKitStore에서 사용하는 OCKStore를 CloudKit과 연동하기 위해 NSPersistentCloudKitContainer를 설정하는 예시입니다. CoreData를 기반으로 CareKit 데이터를 저장하며, CloudKit을 백엔드로 사용함으로써 자동 동기화 및 다중 디바이스 간 데이터 공유가 가능해집니다. NSPersistentCloudKitContainer는 CloudKit을 지원하는 CoreData 컨테이너로, cloudKitContainerOptions에 iCloud 컨테이너 식별자(containerIdentifier)를 설정하여 애플 개발자 계정에서 등록한 iCloud Container와 연결합니다. 그 후 loadPersistentStores를 통해 영구 저장소를 초기화하고, 이를 바탕으로 OCKStore를 생성합니다. OCKStore는 CareKit 데이터(Tasks, Outcomes 등)를 저장/조회하는 객체이며, .cloud 타입으로 선언함으로써 CloudKit 백엔드 연동이 활성화됩니다.이 구성으로 앱은 CareKit 데이터를 로컬 CoreData와 동시에 iCloud를 통해 자동으로 동기화할 수 있습니다.
```

```swift
// CareKit 데이터를 CloudKit에 저장하기
import CareKit
import CloudKit

// 예: 간단한 OCKTask를 CloudKit 레코드로 변환 및 저장
func saveTaskToCloudKit(task: OCKTask) {
    let record = CKRecord(recordType: "Task")
    record["id"] = task.id as CKRecordValue
    record["title"] = task.title as CKRecordValue
    record["schedule"] = task.schedule.elements.description as CKRecordValue

    let privateDB = CKContainer.default().privateCloudDatabase
    privateDB.save(record) { savedRecord, error in
        if let error = error {
            print("CloudKit 저장 실패: \(error.localizedDescription)")
        } else {
            print("CloudKit 저장 성공!")
        }
    }
}

```

```swift
//데이터 불러오기 예시 
func fetchTasksFromCloudKit(completion: @escaping ([OCKTask]) -> Void) {
    let query = CKQuery(recordType: "Task", predicate: NSPredicate(value: true))
    let privateDB = CKContainer.default().privateCloudDatabase

    privateDB.perform(query, inZoneWith: nil) { records, error in
        guard let records = records, error == nil else {
            print("CloudKit 불러오기 실패: \(error?.localizedDescription ?? "Unknown error")")
            completion([])
            return
        }

        let tasks = records.compactMap { record -> OCKTask? in
            guard let id = record["id"] as? String,
                  let title = record["title"] as? String
            else { return nil }
            // schedule 파싱 로직 필요 (간단 예시)
            return OCKTask(id: id, title: title, carePlanID: nil, schedule: .init(occurrencesPerDay: 1))
        }
        completion(tasks)
    }
}

```
- 이 함수는 CloudKit의 Private Database에서 “Task” 레코드를 조회하여 OCKTask 객체 배열로 변환하고, 결과를 클로저를 통해 비동기적으로 반환합니다. CKQuery를 사용하여 모든 Task 데이터를 가져오며, 쿼리 결과는 CKRecord 배열로 반환됩니다.

- 각 레코드는 compactMap을 통해 OCKTask 객체로 변환됩니다. 이 예시에서는 레코드의 “id”와 “title” 필드 값을 추출하여 OCKTask의 생성자에 전달하고 있으며, schedule은 간단한 형태로 초기화되어 있어 실제 서비스 적용 시에는 CKRecord에 저장된 스케줄 데이터를 파싱해 OCKSchedule로 변환하는 별도의 로직이 필요합니다.

- CloudKit 연동 구조상, 이 함수는 네트워크 지연이 발생할 수 있으므로 @escaping 클로저를 통해 결과를 호출 측으로 전달하며, 에러 발생 시에는 빈 배열을 반환합니다. 이 방식은 CareKit 기반 앱에서 CloudKit에 저장된 Task 데이터를 가져와 로컬 UI나 상태에 반영하는 데 활용됩니다.

## Keywords
+ 파생된 키워드들을 작성

## References
- Perplexity
- Chat GPT