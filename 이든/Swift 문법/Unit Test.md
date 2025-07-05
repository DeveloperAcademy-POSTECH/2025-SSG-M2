>[!question]
>GQ1. Unit Test에 대해 알아보자
>GQ2. Unit Test는 언제 쓰일까?
>GQ3. Unit Test의 장점은 ? 

## Unit Test란
-  Unit Test는 소스 코드의 특정 함수, 메소드, 클래스 등 ***가장 작은 단위가 개발자의 의도대로 정확히 동작***하는지 독립적으로 검증하는 절차입니다.
#### 목적 
- 코드의 각 부분이 올바르게 동작하는지 빠르고 반복적으로 검증하여, 버그를 조기에 발견하고 코드의 신뢰성을 높입니다.
#### FIRST 원칙
- ***FIRST*** 원칙 
	- Fast(빠르게) : 테스트는 짧은 시간에 실행되어야 합니다.
	- Independent(독립적) : 각 테스트는 서로 영향을 주지 않고 독립적으로 동작해야 합니다.
	- Repeatable(반복 가능) : 언제, 어디서 실행해도 같은 결과를 내야 합니다.
	- Self0Automated(자동화) : 테스트 실행과 결과 확인이 자동으로 이루어져야 합니다.
	- Timely(제시각, 적시에) : 코드 변경 시점에 맞춰 테스트가 이루어져야합니다.

## Unit Test 는 언제 쓰일까 ?
- 코드의 특정 기능이나 동작이 의도대로 잘 동작하는지 자동으로 검증할 때 사용한다. 
	- 새로운 기능을 추가할 때, 해당 기능이 정상적으로 동작하는지 확인할 때 
	- 버그 수정 후, 같은 문제가 다시 발생하지 않는지 검증할 때
	- 리팩토링 후, 기존 동작이 깨지지 않았는지 확인할 때
	- 코드의 성능을 측정하고 관리할 때
```swift
//Model
struct TodoTask: Identifiable {
	let id = UUID()
	var name: String
}
// ViewModel
class TodoViewModel {
	private(set) var tasks: [TodoTask] = []
	func addTask(name : String) {
	let task = TodoTasl(name:name)
	tasks.append(task)
	}
}
```

---
```swift
import XCTest 
@testable import MyApp

final class TodoViewModelTests: XCTestCase {
	var sut: TodoViewModel!

	override func setUp(){
	super.setUp()
	sut = TodoViewModel()
	}

	override func tearDown() {
	sut = nil
	super.tearDown()
	}
	func test_addTask() {
	let initialTaskCount = sut.tasks.count
	let newTodoTask = "Test Task"
	}
	sut.addTask(name: newTodoTask)

	XCTAssertEqual(sut.tasks.count, initialTaskCount + 1)
	XCTAssertEqual(sut.tasks.last?.name, newTodoTask)
	}
}
```
- Arrange: 테스트를 위한 초기 상태를 준비한다.
- Act: 실제로 테스트할 동작(addTask)을 수행한다.
- Assert: 기대한 결과와 실제 결과를 비교해 검증 
- ---
## Unit Test의 장점은 ? 

- 버그 감소 및 코드 품질 향상 
	- 코드의 예상 동작과 실제 동작 사이의 차이를 조기에 발견하고 수정할 수 있어, 개발 초기부터 버그를 줄이고 전체적인 코드 품질을 높일 수 있습니다.
- 리팩토링의 안정성 확보
	- 코드 구조를 개선하거나 기능을 추가할 때, 기존 기능이 정상적으로 동작하는지 빠르게 확인할 수 있어 리팩토링을 안전하게 진행할 수 있습니다. 
- 개발 속도 향상
	- 반복되는 테스트를 자동화함으로써, 코드 변경 후에도 빠르게 문제를 확인하고 수정할 수 있어 전체 개발 속도가 빨라집니다.
- 문서화 역할
	- 테스트 코드는 코드의 사용법과 의도를 명확하게 보여주므로, 새로운 개발자가 코드를 쉽게 이해하고 활용할 수 있습니다.
- 유지보수 용이성
	- 코드 변경 시 테스트를 통해 기존 기능의 정상 동작을 즉시 확인할 수 있어, 유지보수가 쉬어집니다.
- 회귀 방지
	- 기존 기능이 코드 변경이나 추가로 인해 깨지는 것을 방지할 수 있습니다. 테스트를 통해 코드의 일관성을 계속 유지할 수 있습니다.
- 모듈성 증가
	- 테스트 가능한 구조로 코드를 작성하게 되어, 각 기능이 독립적으로 관리되고 테스트될 수 있습니다. 

## 더 알면 좋을거?

#### Given-When-Then 패턴
- 테스트 코드를 작성할 때 논리적 흐름을 명확히 하기위해 사용합니다.
	- Given: 테스트를 위한 초기 상태나 조건을 설정
	- When : 테스트 대상 동작을 실행
	- Then : 결과를 검증 
- 이 패턴을 사용하면 테스트의 목적과 과정을 명확하게 드러낼 수 있습니다. 
- setUp/tearDown 활용
	- setUpWithError(): 각  테스트 메서드 실행 전 초기화 코드 작성
	- tearDownWithError() : 각 테스트 메서드 실행 후 정리 코드 작성 
이를 통해 테스트 간의 독립성을 보장할 수 있습니다. 


## References
- Perplexity 