기원
-> Robert C. Martin이 만들었음..이사람이 누구냐?

애자일 방법론 , SOLID (객체지향 설계원칙) 등등의 창시자로 알려져 있음.
뭐 그냥 좋은건 다 만들었음

-> '추상화 개념' **(Abstraction principle)** 으로써 관심사를 분리시키고 의존도를 낮추는 것에 목적을 둔 아키텍쳐임
(아니 그래도 그게 먼데 ...)

자자 주상복합 아파트로 비유를 해보자!

- 1층 : 상가 (사용자가 직접 만나는 곳)
- 2~10층 : 주거공간 (핵심 비즈니스가 돌아가는 곳)
- 지하 : 기계실, 전기실 (데이터베이스, 외부 시스템)

이렇게 보면 개쉬움 ㅋㅋㅋ

여기에 아파트 위원회에서 층간 규칙을 만들었음
1) 위층에서 아래층으로만 의존 (상가에서 전기를 쓰지만, 전기실이 상가를 알 필요는 없음)
2) 각 층은 자기 일만 집중 (상가는 손님 응대, 기계실은 전기 공급)

이게 클린 아키텍쳐임 
무슨 ..xx이런 벤다이어그램 보면 누가 이해를 함 ..(개발 고수들은 이해할지도 ㅋㅋㅋㅋㅋㅋ)
![[Pasted image 20250724195726.png]]

자자 그럼 위 아파트를 실제 개발로 바꿔보면

- UI층 : 화면 보여주기만
- 비즈니스층: 핵심 로직만
- 데이터층 : 저장/조회만
  
  이렇게 되는 거임, ===여기서 의존도를 낮춘다 라는 개념으로 가면.
  한 층이 바뀌어도 다른 층에 영향 최소화===

1) 데이터베이스를 MySQL에서 MongoDB로 바꿔도 비즈니스 로직은 그대로
2) UI를 웹에서 모바일 앱으로 바꿔도 핵심 기능은 그대로

그럼 ..이걸 왜 써야함?

- 수정하기 쉬워짐 (한 부분만 바꾸면 됨)
- 테스트하기 쉬워짐 (각 층을 따로 테스트)
- 여러 사람이 동시에 작업 가능 (층별로 분담)
  
  이렇게 되는 거임 ..근데 것만 보면 이해를 하기 아직도 어려움
  실제 C4 예시를 보여 드리겠음.
  
   ![[Pasted image 20250724201243.png]]

-> 저희 팀 폴더 구조 입니다.

CodePlay라는 3층 짜리 집이 있다면

3층 (Presentation) : 사람들이 실제로 보고 만지는 곳 
2층 (Domain) : 집의 핵심 기능들 (전기, 수도 등) 
1층 (Data) : 기초 토목 공사, 재료 저장소

이라고 생각 하면 됩니다.

------------------------------------------------------------------


- 3층 : Presentation Layer (사용자가 보는 화면)

![[Pasted image 20250724201536.png]]

근데 열라 짜증나는게 Presentation 열면 바로 View 나올거 같지만
실제로는 이렇게 나옴 그럼 여기서 

폴더 들은 왜 저렇게 나누냐?

===Factory가 뭐고
Scene는 뭐고
Utils 는 뭐냐 ..===


- 만약 이렇게 나누기 않고 View에서 직접 ViewModel을만들면 (큰 프로젝트 기준)

``` swift
// ❌ View에서 직접 만드는 경우
struct SomeView: View {
    var body: some View {
        // 😱 View가 너무 많은 걸 알고 있어야 함
        let repository = DefaultScanPosterRepository()
        let useCase = DefaultScanPosterUseCase(repository: repository)
        let viewModel = DefaultPosterViewModel(scanPosterUseCase: useCase)
        
        MainPosterView()
            .environmentObject(PosterViewModelWrapper(viewModel: viewModel))
    }
}
```

근데 Factory가 있다면

```swift
// ✅ Factory 사용
struct SomeView: View {
    let factory: MainFactory
    
    var body: some View {
        factory.mainPosterView()  // 한 줄로 끝!
    }
}
```

이렇게 끝이다..ㄷㄷ

즉, Factory의 역할은 = 조립 역할임
- View는 "포스터 화면 줘" 라고만 요청 하고
- Factory가 알아서 ViewModle, UseCase, Repository다 만들어서 조립
- View는 복잡한 의존성 몰라도 되는 거임

실제 제가 C4에서 사용 했던 파일을 보면

```swift
// MainFactory.swift
final class DefaultMainFactory: MainFactory {
    private let posterViewModelWrapper: PosterViewModelWrapper
    private let musicViewModelWrapper: MusicViewModelWrapper
    
    // 이미 조립된 부품들을 받아서 저장
    init(posterViewModelWrapper: PosterViewModelWrapper, 
         musicViewModelWrapper: MusicViewModelWrapper, 
         diContainer: MainSceneDIContainer) {
        self.posterViewModelWrapper = posterViewModelWrapper
        self.musicViewModelWrapper = musicViewModelWrapper
    }
    
    // "포스터 화면 줘!" 요청이 오면
    func mainPosterView() -> AnyView {
        return AnyView(MainPosterView()  // View 만들고
            .environmentObject(posterViewModelWrapper))  // ViewModel 연결
    }
    
    // "음악 화면 줘!" 요청이 오면
    func mainMusicView() -> AnyView {
        return AnyView(AppleMusicConnectView()  // View 만들고
            .environmentObject(musicViewModelWrapper))  // ViewModel 연결
    }
}
```

주석으로 처리해 두었음. (가져오는 파일들이 있는 것임)
미리 View 와 ViewModel을 만들어 두고 View에는 주기만 하면 되는거임
그 Factory에는 Domain, Data도 포함 되어 있음


자 근데 여기서 중요함 ...그럼 Domain, Data의 값은 어디서 가져 올까?
Viewmodel이 가져오는 거임

**"ViewModel = DI Container가 만든 부품들을 조립받은 완성품"**

- ViewModel 자체는 아무것도 만들지 않음
- DI Container에서 필요한 모든 걸 받아옴
- 받은 것들을 사용해서 비즈니스 로직 처리

**"Factory = 완성품을 View에 배달하는 배달원"**

- ViewModel(완성품)을 View에게 전달만 함
- 완성품이 어떻게 만들어졌는지는 모름 그냥 받기만 함 ㅋㅋㅋㅋ

이게 바로 ===Clean Architecture===의 장점임



**의존성 주입의 핵심**

DI Container (상위) 
	↓ "너한테 필요한 UseCase 줄게" 
ViewModel (하위) 

UseCase도 마찬가지로: 
DI Container (상위) 
	↓ "너한테 필요한 Repository 줄게" 
UseCase (하위)

이렇게 되는거임


자자 그럼 정리하면 Factory의 기능을 정리하면

1) 복잡한 조립 과정을 숨겨줌
2) View는 단순하게 화면만 요청
3) 조립 방법이 바뀌어도 View는 몰라도 됨



## Scene (앱의 주요 기능별 화면)
-> 사용자가 실제로 보고 상호작용하는 모든 것

1) 화면에 무엇을 표시할지
2) 사용자가 어떤 행동을 할 수 있는지
3) 어떤 순서로 화면이 전환되는지
4) 사용자에게 어떤 피드백을 줄지

와 같이 사용자가 앱을 쓸 때 보고 만지는 모든 것들 이라고 생각 하면 됩니다.

- 사용자는 Domain/Data가 먼지 알필요 없음
- Scene을 통해서만 앱과 상호작용
다시 말해 온전히 보여주기 위한 곳임



## Utils (도구상자)

-> 같은 UI를 여러 곳에서 써야 할 때

```swift
// ❌ 각 화면마다 똑같은 버튼 코드 복사-붙여넣기
struct Screen1: View {
    var body: some View {
        Button(action: { /* 액션 */ }) {
            Text("확인")
                .foregroundStyle(.white)
                .padding(.horizontal, 87)
                .padding(.vertical, 18)
                .background(Color.blue)
                .cornerRadius(999)
        }
    }
}

struct Screen2: View {
    var body: some View {
        Button(action: { /* 액션 */ }) {
            Text("다음")
                .foregroundStyle(.white)
                .padding(.horizontal, 87)  // 똑같은 코드 반복...
                .padding(.vertical, 18)
                .cornerRadius(999)
        }
    }
}
```

근데 만약 Utils를 사용 하면

```swift
// ✅ Utils에서 공통 컴포넌트 사용
struct Screen1: View {
    var body: some View {
        BottomButton(title: "확인") { /* 액션 */ }
    }
}

struct Screen2: View {
    var body: some View {
        BottomButton(title: "다음") { /* 액션 */ }
    }
}
```

이렇게 끝남 (사실이건 쉬운듯 컴포넌트를 여기다 넣는다고 생각 하면 됨)

1) UI 컴포넌트
2) Extension (기능확장)
3) 유틸리티 클래스 

이런 기능 들이고 그냥 한번 만들어서 여러번 쓴다고 생각 하면 됨


------------------------------------------------------------------

# Domain Layer 

-> 


![[Pasted image 20250724212335.png]]

![[Pasted image 20250724212400.png]]

2탄에서 이어질 예정