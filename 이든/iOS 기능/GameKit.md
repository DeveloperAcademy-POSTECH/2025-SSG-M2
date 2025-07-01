> [!question] 
> GQ1. GameKit 이란? 
> GQ2. GameKit을 사용하는 경우 
> GQ3. GameKit 사용방법

## 주요 기능

- GameKit 프레임 워크를 사용하면, 소셜 게임 네트워크 기능 구현이 가능합니다. 이걸 사용하면, 로그인, 리더보드, 도전 과제, 멀티 플레이어 게임 같은 소셜 게임 기능을 쉽게 구현할 수 있습니다.

```swift
import GameKit

func authenticateGameCenter() { // 로그인 처리 함수
	// 로그인 / 인증 처리 클로저 설정
    GKLocalPlayer.local.authenticateHandler = { viewController, error in
	    // 현재 앱 가장 첫 번째 윈도우에 rootViewController를 찾아서 로그인 화면 띄우기
        if let vc = viewController {
            // 로그인 화면이 필요한 경우 표시
            if let windowScene = UIApplication.shared.connectedScenes.first as? UIWindowScene,
               let rootVC = windowScene.windows.first?.rootViewController {
               // Game Center 로그인 화면 보여줌 (사용자가 로그인 안 했을 경우)
                rootVC.present(vc, animated: true)
            }
            이미 로그인 된 상태일 경우
        } else if GKLocalPlayer.local.isAuthenticated {
            print("Game Center 로그인 성공") // 닉네임 가져오기
            let playerName = GKLocalPlayer.local.displayName
            print("유저 이름: \\\\(playerName)")
        } else {
            print("Game Center 로그인 실패: \\\\(error?.localizedDescription ?? "알 수 없는 오류")")
        }
    }
}

```

---

### 친구연결 기능

- 게임을 할 때 친구들과의 연결은 필수적이라고 생각합니다 ! ! ! 다만 이 친구들과의 연결이 굉장히 어려운데, GameKit에서는 이 부분을 제공해주고 있습니다 !

```swift
import GameKit

func presentMatchmaker(from rootVC: UIViewController) {
    let request = GKMatchRequest()
    request.minPlayers = 2
    request.maxPlayers = 4
    // 필요한 경우 특정 친구의 playerID를 직접 설정
    // request.playersToInvite = ["playerID1", "playerID2"]

    let mmvc = GKMatchmakerViewController(matchRequest: request)
    mmvc?.matchmakerDelegate = .self
    rootVC.present(mmvc!, animated: true)
}

```

#presentMatchmaker -> 친구 초대/ 매칭 화면(UI)을 보여주는 역할의 함수 #GKMatchRequest -> 매칭 조건 설정( 몇명필요 ? ) #GKMatchmakerViewController -> Game Center에서 제공하는 친구 초대 / 매칭 화면 UI를 생성 #present -> 생성한 매칭 UI를 현재 화면 위에 띄움 #matchmakerDelegate -> 매칭완료, 취소, 실패 등을 처리할 델리게이트 지정

## 자 ! 그러면 여기서 Delegate란 ? !

### "어떤 객체가 해야 할 일 중 일부를 다른 객체에게 맡기는 것"

- > 패턴 구조 : Protocol : "이런 메서드를 구현해줘" 라고 약속 Delegate 객체 : 그 프로토콜을 채택하고, 메서드를 실제로 구현 주체 : 무언가 이벤트가 생겼을 떄 , delegate에게 물어봄
    

번외편 : 전화가 올 때, 직접 받는 대신 비서(delegate)가 받아줌 = 이벤트가 생기면 delegate 메서드 호출

| **항목** | **Delegate**                       | **클로저 (Closure)**   |
| ------ | ---------------------------------- | ------------------- |
| 목적     | 여러 메서드 이벤트 위임                      | 하나의 작업을 간결하게 넘김     |
| 구조     | protocol 기반                        | () -> Void 형태 함수 변수 |
| 복잡도    | 설정은 조금 복잡                          | 간단하게 넘겨주기 쉬움        |
| 사용 예   | TableView, GameKit, CoreLocation 등 | 버튼 액션, 단일 이벤트 처리    |

## 직접 사용해보기

```swift

import GameKit
import SwiftUI

struct ContentView: View {
    var body: some View {
        VStack(spacing: 20) {
            Text("Game Center 로그인 예제")
                .font(.title)

            Button("로그인 시도") {
                authenticateGameCenter()
            }
        }
        .padding()
    }

    // Game Center 로그인 함수
    func authenticateGameCenter() {
        GKLocalPlayer.local.authenticateHandler = { viewController, error in
            if let viewController = viewController {
                // 로그인 화면이 필요한 경우 띄워주기
                if let scene = UIApplication.shared.connectedScenes.first as? UIWindowScene,
                   let window = scene.windows.first,
                   let rootVC = window.rootViewController {
                    rootVC.present(viewController, animated: true)
                }
            } else if GKLocalPlayer.local.isAuthenticated {
                print("로그인 성공: \\\\(GKLocalPlayer.local.displayName)")
            } else {
                print("로그인 실패: \\\\(error?.localizedDescription ?? "알 수 없음")")
            }
        }
    }
}

```

- import GameKit
    
    → 애플에서 제공하는 게임 관련 기능을 담은 프레임워크. 로그인, 리더보드, 도전 과제, 친구 초대, 매치메이킹 등을 구현할 수 있다.
    
- GKLocalPlayer
    
    → 현재 기기의 Game Center 사용자(플레이어)를 나타냄. 로그인 여부나 사용자 정보 접근 시 사용한다.
    
- authenticateHandler
    
    → 로그인 과정을 처리하는 클로저. 사용자가 로그인하지 않았으면 로그인 화면을 띄우고, 인증 여부를 알려줌.
    
- GKLocalPlayer.local.isAuthenticated
    
    → 사용자가 Game Center에 로그인되어 있는지를 나타내는 불리언 값.
    
- GKLocalPlayer.local.displayName
    
    → 로그인된 Game Center 사용자의 닉네임. 게임 내에서 사용자 이름으로 표시할 때 사용.
    
- GKGameCenterViewController
    
    → Game Center에서 제공하는 리더보드, 도전 과제 등 UI를 보여주는 기본 뷰 컨트롤러.
    

## References

- Apple 공식문서 & Chat Gpt