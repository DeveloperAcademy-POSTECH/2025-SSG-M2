> [!question] 
> GQ1. Coordinato Pattern이란 ? 
> GQ2. Coordinato Pattern 을 왜 사용하지 ? 
> GQ3. Coordinator Pattern 의 사용법이 뭘까 ?

## Description

### GQ1.

- 제가 이번 C3를 진행하면서 Taeni가 Coordinator 라는 패턴을 사용하시는 모습을 보고 이놈은 뭐하는 놈인가 해서 여러분들에게 공유 + 공부하고자 이렇게 작성을 했읍니다.
- 다들 MVC, MVVM, MVP 같은 여러가지 Architecture 들이 있습니다. 그 중에서도 MVC - C 등 뒤에 C가 붙은 친구들이 있는데, C가 Coordinator를 의미합니다.
- 또한 Coordinator는 우리가 아는 라우터(Router)와 같은 역할을 하기도 한답니다. ( 라우터는 쉽게 말해 우편 배달부라고 생각하면 좋아용 )

---

### GQ2.

- Coordinator를 사용하는 이유에 대해 설명드릴게요 ! 우리가 지난번에 Lumi에게 배웠던 객체지향 설계 원칙(SOLID)를 기억하시나요 ? 여기서 S가 의미하는건, Single Responsibility Principle . 즉 단일 책임 원칙(하나에 하나) 을 의미합니다 !
- 기존에는 ViewController가 화면전환을 담당했습니다 ! 때문에 부모 VC는 자식 VC를 알아야 했고, 자식 VC에 필요한 객체들 또한, 모두 부모 VC 내부에서 인스턴스를 생성해야 했습니다. 때문에 객체들 간 결합도가 높아질 수밖에 없고, 아키택쳐를 설계할 때 높은 결합 도는 유지 보수에 불리합니다.
- 여기서 Coordinator 패턴을 사용한다면, 더 이상 부모 VC는 자식 VC를 알 필요가 없습니다. 자식 VC의 의존성을 주입하고 객체를 생성해 화면을 전환하는 역할은 Coordinator가 하기 때문입니다. 이젠 VC는 자기 Coordinator에게 자식 VC로 화면전환을 요청하면 됩니다. 이로써 이제 ViewController 오직 데이터를 화면에 뿌려주는 역할만 집중할 수 있게 되고, 때문에 유지 보수가 용이해졌습니다.
- 이 말들이 잘 이해가 안되죠 ? 그래서 제가 비유를 들고왔습니다!!!
- 예전에는 배우가 대사도 외우고, 무대도 세팅하고, 조명도 조절해야 했습니다 ! 근데 이제는 무대 담당(Coordinator)가 따로 있어서 배우는 대사를 외워서 하는 연기에만 집중한다면, 배우의 연기가 더욱 더 좋아지겠죠 ? 위에 말이 이 말이랍니다 ! 더 효율적이고 안전하다!!

## 주요 기능

- 더 이상 부모 VC는 자식 VC를 알 필요가 없습니다. 자식 VC의 의존성을 주입하고 객체를 생성해 화면을 전환하는 역할은 Coordinator가 하기 때문입니다. 이젠 VC는 자기 Coordinator에게 자식 VC로 화면전환을 요청하면 됩니다.

## 코드 예시

```swift
import SwiftUI

//화면 만들기

struct FirstScreen: View {

var body: some View {
	Color.red
	}
}

struct SecondScreen: View {

var body: some View {
	Color.blue
	}
}

struct ThirdScreen: View {

var body: some View {
	Color.yellow
	}
}

```

이 코드들을 선언한 이후에 탐색 코디네이터를 만듭니다 !

```swift
import SwiftUI
import Coordinators
  //어떤 화면을 보여줄지 정리 !
class CommonCoordinator: NavigationCoordinator {  //이것도 프로토콜

    // screens available for navigation
    enum Screen: ScreenProtocol {
        case first  //태그
        case second  //태그
        case third  //태그
        //이게 프로토콜
    }

    // view for each screen
    func destination(for screen: Screen) -> some View {
        switch screen {
        case .first: FirstScreen()  //이렇게 하면 첫번째 빨간 화면 나옴
        case .second: SecondScreen()
        case .third: ThirdScreen()
        }
    }
}

```

```swift
//루트 수준에서 코티네이터를 초기화 합니다.
@main
struct CoordinatorsExampleApp : App {

// 코디네이터의 인스턴스를 생성합니다
	@StateObject var coordinator = CommonCoordinator ()

	var body: some Scene {
	WindowGroup {
// 코디네이터의 루트 뷰를 표시합니다
			coordinator.view(for: .first)
		}
	}
}

```

현재 예에서 코디네이터는 StateObject로 저장되고 초기화면은 앱의 루트 뷰로 표시

화면 간 이동을 원한다면 !?

```swift
struct  FirstScreen : View {

   // 코디네이터에 대한 참조
   @EnvironmentObject  var coordinator: Navigation < CommonCoordinator >

    var body: some  View {
        VStack {
            Button ( "Second" ) {   //버튼 누르면 요구하는 화면으로 이동
                // 두 번째 화면으로의 탐색
                 coordinator().present(.second)
            }
            Button ( "Third" ) {
                // 세 번째 화면으로의 탐색
                 coordinator().present(.third)
            }
        }
    }
}

```

```swift
//추가 옵션 필요시 코디네이터 참조를 사용할 수 있다 .
Coordinator().pop()  //한 단계 뒤로가기

Coordinator().popTo(.first)  //첫 화면으로 이동

Coordinator().popToRoot()  //가장 처음으로 이동

Coordinator().popTo(where: { screen in }) // 조건에 따라 특정 화면으로 가고싶을 때 !

```

@EnvironmentObject 래퍼 에서 처럼 코디네이터가 모든 자식 뷰에 전달됩니다.

## Keywords

- Coordinator 장점. 화면 전환의 역할을 분리할 수 있다. 유지보수가 용이하다
- Coordinator 단점.
- 코드를 더 많이 작성해야한다. 화면이 Disappear(화면은 안 보이지만, **메모리엔 아직 살아 있을 수도 있음**) 되면, Coordinato 도 같이 deinit(진짜 사라지면) 되어줘야 하는데, 이를 따로 처리하지 않으면 메모리 누수

## References

- 참고한 레퍼런스를 작성 (예 : Apple의 공식 문서)