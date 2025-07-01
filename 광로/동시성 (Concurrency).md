-> 여러 작업을 동시에 처리하는 방식 (그렇다고 "꼭" 동시에 실행 되는 건 아님)



Why 알아야 함?
-> 비동기 작업을 효율적으로 수행하기 위해 꼭 알아 두어야할 개념
Swift 5.5부터는 async/await 키워드와 함께 구조적으로 잘 다룰수 있게 되었다.

1) UI 멈춤 방지 
2) 네트워크나 디스크 같은 느린 작업 처리
3) 여러 작업을 병렬로 처리해서 성능 개선


==비동기 (Asynchornous)== 
-> 결과를 기다리지 않고 다음 작업으로 넘어감 (개발자가 원할때 실행 가능)



# 동시성의 대표 요소

- async / await
- Task

- TaskGroup
-> 여러 비동기 작업을 동시에 실행 하고, 결과를 모아서 처리
- actor
-> 동시성 환경에서 데이터 경쟁(race condition)을 막기 위한 타입 (내부 상태를 안전하게 보호함)





- ==async / await==
-> 이거 ..맨날 어려웠는데 ..

1) async - 비동기 함수 정의 (함수 선언 쪽)
2) await - 비동기 함수 호출 시 사용, 결과가 나올 때 까지 '대기' (호출 쪽)
   
   이 둘은 반드시 세트로 사용 된다!!
   아직은..잘 느껴 지지 않으니 코드 예시로 한번 보자!!


``` swift

func fetchUserName() async -> String {
    // 서버에서 이름을 가져온다고 가정
    try? await Task.sleep(nanoseconds: 2_000_000_000) // 2초 대기
    return "Chanuk"
    // 비동기 작업이 끝난 후 결과로 "Chanuk"이 리턴 되는 거임 await으로 기다려야 받을 수 있음
}

Task {
    let name = await fetchUserName()
    // 비동기 함수 호출이니깐 await로 기다림
    print("사용자 이름: \(name)")
}





1. 첫줄 보면
   async : 즉시 결과를 리턴하지 않고, 기다렸다가 결과를 달라고 한다.

2. try? await Task.sleep(nanoseconds: 2_000_000_000)
-> 이거 포인트

여기서 Task.sleep -> 현재 작업을 일시정지 시키는 Swift Concurrency 제공함수
nanoseconds = 2_000_000_000 2초 동안 쉬어라.










func fetchUserName() async -> String {
    // 서버에서 이름을 가져온다고 가정
    try? await Task.sleep(nanoseconds: 2_000_000_000) // 2초 대기
    return "Chanuk"
    // 비동기 작업이 끝난 후 결과로 "Chanuk"이 리턴 되는 거임 await으로 기다려야 받을 수 있음
}

Task {
    let name = await fetchUserName()
    // 비동기 함수 호출이니깐 await로 기다림
    print("사용자 이름: \(name)")
}






**** Point 
await = 잠시 쉬고 다시 실행하라는 뜻

근데 왜 앞에 try를 썼나??

Task.sleep이 Error를 던질수 있기 때문 ...왜 그런가??

Task {
    try await Task.sleep(nanoseconds: 5_000_000_000)
}

만약 위와 같은 코드가 있는데..
중간에 사용자가 "뒤로가기"를 눌러서 이 작업이 취소가 되면 ..?

이때를 대비하기 위해 Task.sleep은 "취소되었음"을 알리기 위해 에러를 던짐

그럼 try는 "에러가 나면 그냥 nil로 처리하고 넘어가자" - 즉, 오류를 무시하고 지나간다는 뜻
다시 말해 

"에러가 날 수 있는 상황에, 굳이 잡지 않고 무시하고 지나가고 싶어서"임!!



그럼 밑에 왜 Task로 감쌋을까?

fetchUserName()은 async 함수임
근데 일반 Swift 일반 코드에서는 await을 바로 쓸수 없음. 
so await를 쓸 수 있는 "비동기 환경"인 Task{} 안에 넣어준 거임


```


최종적으로 정리하면!!

``` swift
async = 이 함수는 기다릴 수 있는 함수라고 "약속, 정의" 해주는 것
await = 이 비동기 함수의 결과가 나올 때 까지 기다릴게 라는 뜻


async → 이 함수는 기다릴 수 있음  
await → 지금 진짜 기다리는 중
Task { ... } → 이걸 감싸야 await가 가능함

```



- ==Task==
-> 비동기 작업을 즉시 실행 할 수 있게  해주는 컨테이너 (메인 쓰레드와 별도로 작동 가능)

``` swift

func loadImage() async -> String {
    try? await Task.sleep(nanoseconds: 1_000_000_000) // 1초 대기
    return "🖼 이미지 로딩 완료"
}

// 어디서든 Task로 실행 가능
Task {
    let result = await loadImage()
    print(result)
}
```
이렇게 Task로 어디서든 비동기함수를 실행 할수 있다고 알면 ..너무 쉽지만 
조금만 파보면!! 사용할떄 주의 할 점이 있음!!


예시

``` swift

struct ContentView: View {
    var body: some View {
        Text("Hello")
            .onAppear {
                Task {
                    await fetchSomething()
                }
            }
    }
}

```

이 코드를 잘 보면 fetchSomething()은 문제 없이 실행이 될 수 있음

근데 만약 사용자가 **다른 화면으로 이동하거나**,
**이 View 자체가 사라지는 순간** → Swift는 내부적으로 이 View에 연결된 Task를 **취소**할 수 있음!!
왜?? 그게 Swift의 구조화된 동시성 철학 때문이다!!

1) Swift는 View와 관련된 작업(Task)도 "View의 생명주기"에 묶어서 자동 관리 하기 떄문에
   -> View가 사라지면 그 View에서 만든 Task도 같이 사라지게 해야 프로그램이 안전해 진다.

이건 안해두면 -> 비동기 작업이 끝나고 나서 없어진 View를 업데이트 하려다 앱이 Error가 남


그럼 어떻게 해야 하냐!!!

- Task(priority:) + 참조

``` swift
let handle = Task(priority: .userInitiated) {
    await fetchData()
}
// 나중에 handle.cancel() 도 가능
```
-> 이렇게 하면 Task를 변수로 보관해서 중간에 수동 취소 or 추적가능 해진다



Swift UI 에서는 task {} modifier 사용

``` swift
Text("Loading...")
    .task {
        await fetchData()
        //이 구조가 SwiftUI View 전용 modifier임
    }
이 modifier 안에 들어가는 코드는 **자동으로 Task {}로 감싸진 비동기 실행 환경**이 돼!
```



자자 정리하면

Task {} 는 "그냥 실행만 하는 것"이 아니라

View가 없어지면 자연스럽게 취소될 수 있음
안정성을 원한다면 -> Task{priority:} 나 .task{}를 써서 생명주기를 명확히 관리해야 한다.







- TaskGroup
-> 여러 비동기 작업을 동시에 실행 하고, 결과를 모아서 처리


- actor
-> 동시성 환경에서 데이터 경쟁(race condition)을 막기 위한 타입 (내부 상태를 안전하게 보호함)








# 주의해야 할 점

- Race Condition - 여러 쓰레드가 동시에 데이터를 바꾸려고 하면 충돌 발생
- Deadlock - 서로 작업이 끝나길 기다리면 멈춰버리는 상황
- Priority Inversion - 낮은 우선순위 작업이 높은 우선순위 작업을 막는 경우