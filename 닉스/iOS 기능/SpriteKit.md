2D 게임 전용 프레임워크
터치 기반 조작과 물리 엔진, 애니메이션 등을 쉽게 다룰 수 있게 해줌!

### SpriteKit의 기본 구조
SpriteKit에서는 게임을 **장면(Scene)과 노드(Node)** 중심으로 구성함

|`SKView`|게임이 렌더링되는 화면. SwiftUI에서는 `SpriteView`로 사용 가능|
|---|---|
|`SKScene`|하나의 게임 장면. 모든 노드는 이 안에 배치됨|
|`SKNode`|모든 SpriteKit 객체의 기본 클래스|
|`SKSpriteNode`|이미지나 색상을 화면에 표시할 수 있는 기본 노드|
|`SKAction`|노드에 애니메이션 효과(이동, 회전 등)를 부여|
|`SKPhysicsBody`|물리 속성(중력, 충돌 등)을 설정할 수 있는 객체|

### 기본 게임 루프 흐름
1. `GameViewController`에서 `SKScene`을 로드
2. `SKScene`의 `update(_:)` 메서드가 프레임마다 호출됨
3. 노드 업데이트, 충돌 처리, 물리 엔진 연산 등이 여기서 이루어짐!

### 간단한 예시 코드
> 까만 네모가 위아래로 반복해서 움직이는 간단한 SpriteKit 장면
```swift
import SpriteKit
import SwiftUI

struct GameView: View {
    var body: some View {
		    // GameScene(size:) - 직접 정의한 SKScene의 하위 클래스인 GameScene을 인스턴스화함
        SpriteView(scene: GameScene(size: CGSize(width: 300, height: 600)))
        // 실제 디바이스의 해상도와는 무관하게 SpriteKit 내부에서의 좌표 공간
            .ignoresSafeArea()
    }
}

class GameScene: SKScene {
    override func didMove(to view: SKView) { // 장면이 뷰에 나타났을 때 호출
        backgroundColor = .blue
        
        //까만색 네모
        let sprite = SKSpriteNode(color: .black, size: CGSize(width: 100, height: 100))
        sprite.position = CGPoint(x: frame.midX, y: frame.midY)
        addChild(sprite)
        
        // 위아래로 움직임
        let move = SKAction.moveBy(x: 0, y: 100, duration: 1)
        let reverse = move.reversed()
        let sequence = SKAction.sequence([move, reverse])
        sprite.run(SKAction.repeatForever(sequence))
    }
}
```

++++ 게임이라면 조이스틱을 빼놓을 수 없음ㅎ

### SpriteKit을 이용해서 조이스틱을 구현하려면 어떻게 해야할까?
: SpriteKit에서 조이스틱을 직접 구현하거나 외부 라이브러리를 사용할 수 있다. 기본적인 터치 이벤트(`touchesBegan`, `touchesMoved`, `touchesEnded`)를 활용해서 조이스틱의 중심과 터치 위치를 계산하고 방향을 구할 수 있음!

### SpriteKit 기반 조이스틱 구현 예제
1. 화면 왼쪽 아래에 조이스틱 생성
2. 사용자가 터치하고 드래그하면 방향 벡터 계산
3. 해당 방향으로 캐릭터(SKSpriteNode)가 이동
```swift
import SpriteKit

class GameScene: SKScene {

    // 플레이어 캐릭터 노드
    var player: SKSpriteNode!
    
    // 조이스틱 배경 원 (고정)
    var joystickBase: SKShapeNode!
    
    // 조이스틱 핸들 (움직이는 부분)
    var joystickHandle: SKShapeNode!
    
    // 조이스틱이 활성화된 상태인지
    var joystickActive = false
    
    // 조이스틱의 중심 위치
    var joystickCenter = CGPoint.zero
    
    // 이동 방향 벡터 (정규화된 방향)
    var movementVector = CGVector.zero

    override func didMove(to view: SKView) {
        backgroundColor = .white
        
        // 플레이어 캐릭터 생성
        player = SKSpriteNode(color: .blue, size: CGSize(width: 40, height: 40))
        player.position = CGPoint(x: size.width / 2, y: size.height / 2)
        addChild(player)
        
        // 조이스틱 생성
        createJoystick()
    }

    func createJoystick() {
        // 조이스틱 베이스 (회색 반투명 원)
        joystickBase = SKShapeNode(circleOfRadius: 50)
        joystickBase.position = CGPoint(x: 100, y: 100)
        joystickBase.strokeColor = .gray
        joystickBase.fillColor = UIColor.gray.withAlphaComponent(0.4)
        joystickBase.lineWidth = 2
        addChild(joystickBase)
        
        // 조이스틱 핸들 (작은 진한 회색 원)
        joystickHandle = SKShapeNode(circleOfRadius: 25)
        joystickHandle.position = joystickBase.position
        joystickHandle.strokeColor = .darkGray
        joystickHandle.fillColor = .darkGray
        addChild(joystickHandle)
        
        // 조이스틱 중심값 저장
        joystickCenter = joystickBase.position
    }

    // MARK: - 터치 시작
    override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
        guard let touch = touches.first else { return }
        let location = touch.location(in: self)
        
        // 터치가 조이스틱 영역 안에 있는지 확인
        if joystickBase.contains(location) {
            joystickActive = true
        }
    }

    // MARK: - 터치 이동
    override func touchesMoved(_ touches: Set<UITouch>, with event: UIEvent?) {
        guard joystickActive, let touch = touches.first else { return }
        let location = touch.location(in: self)
        
        // 중심에서 벡터 계산
        let dx = location.x - joystickCenter.x
        let dy = location.y - joystickCenter.y
        let distance = sqrt(dx*dx + dy*dy)
        let angle = atan2(dy, dx)
        
        // 최대 거리 (베이스 반지름)
        let maxDistance: CGFloat = 50
        
        // 거리 제한: 조이스틱 핸들이 베이스를 벗어나지 않게
        let clampedDistance = min(distance, maxDistance)
        
        // 핸들 위치 업데이트
        let x = joystickCenter.x + cos(angle) * clampedDistance
        let y = joystickCenter.y + sin(angle) * clampedDistance
        joystickHandle.position = CGPoint(x: x, y: y)
        
        // 정규화된 이동 벡터 저장
        movementVector = CGVector(dx: cos(angle), dy: sin(angle))
    }

    // MARK: - 터치 종료
    override func touchesEnded(_ touches: Set<UITouch>, with event: UIEvent?) {
        joystickActive = false
        movementVector = CGVector.zero
        joystickHandle.position = joystickCenter  // 조이스틱 리셋
    }

    override func touchesCancelled(_ touches: Set<UITouch>, with event: UIEvent?) {
        touchesEnded(touches, with: event)
    }

    // MARK: - 매 프레임마다 호출됨
    override func update(_ currentTime: TimeInterval) {
        // 이동 속도 설정
        let speed: CGFloat = 2.0
        
        // 이동 벡터 적용
        let dx = movementVector.dx * speed
        let dy = movementVector.dy * speed
        player.position = CGPoint(x: player.position.x + dx,
                                  y: player.position.y + dy)
    }
}
```

### 연결 파일 예시 :: GameViewController.swift
```swift
import UIKit
import SpriteKit

class GameViewController: UIViewController {

		 // 뷰가 메모리에 로드된 직후 호출되는 메서드 
    override func viewDidLoad() {
        super.viewDidLoad()

				// GameScene이라는 SKScene 하위 클래스를 생성
        // 크기는 현재 뷰의 bounds를 기준으로 설정
        let scene = GameScene(size: view.bounds.size)
        
        // SKView는 SpriteKit의 씬(Scene)을 렌더링하는 뷰
        // 이 뷰를 현재 UIViewController의 뷰 위에 올림
        let skView = SKView(frame: view.frame)
        view.addSubview(skView)

        scene.scaleMode = .resizeFill
        
        // SKView에 GameScene 표시(렌더링 시작)
        skView.presentScene(scene)
    }
}
```

근데 우리는 SwiftUI를 배우고 있으니까 SwiftUI에 연결하는 방법을 추가하자면
## 전체 구성
1. `GameScene.swift`: SpriteKit 게임 로직(위에 코드)
2. `SpriteViewContainer.swift`: SpriteKit 뷰를 SwiftUI에 연결하는 래퍼
3. `ContentView.swift`: SwiftUI 메인 뷰

### SpriteViewContainer.swift
`UIViewRepresentable`을 사용해 SpriteKit 뷰를 SwiftUI에 포함시킴
(UIViewRepresentable: UIKit 기반 뷰를 SwiftUI에 통합할 때 사용됨)
```swift
import SwiftUI
import SpriteKit

struct SpriteViewContainer: UIViewRepresentable {
    
    // UIViewRepresentable의 필수 메서드
    func makeUIView(context: Context) -> SKView {
        let view = SKView()
        
        // SpriteKit 씬 생성
        let scene = GameScene(size: UIScreen.main.bounds.size)
        scene.scaleMode = .resizeFill
        
        // SKView에 씬 연결
        view.presentScene(scene)
        
        // FPS, 노드 수 디버그용으로 표시 가능
        view.showsFPS = true
        view.showsNodeCount = true
        
        return view
    }

    func updateUIView(_ uiView: SKView, context: Context) {
        // SwiftUI 상태 변화에 따라 뷰를 업데이트할 때 사용
    }
}
```

### ContentView.swift
```swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        VStack {
            Text("SwiftUI + SpriteKit 연결 예제")
                .font(.title)
                .padding()
            
            // SpriteKit 씬을 SwiftUI에 연결
            SpriteViewContainer()
                .edgesIgnoringSafeArea(.all) // 전체 화면에 꽉 차게
        }
    }
}
```