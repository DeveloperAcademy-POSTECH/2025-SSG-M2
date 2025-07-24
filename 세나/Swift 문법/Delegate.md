>[!question]
>GQ1. Delegate란 무엇인가
>GQ2. 어떻게 쓰는 것인가
>GQ3. 어디에 쓰이는 건가

## Description

- 객체 간의 1:1 위임(위탁) 관계를 만들어 특정 작업이나 이벤트 처리를 다른 객체에 '위임'하는 방식
  -> 한 객체가 해야 할 일부 작업을 대신 **다른 객체에게 맡기는** 디자인 패턴
- 메서드 자체를 인자로 넘겨주는 '형식'
- 델리게이트를 통해 메서드를 매개변수로 전달 할 수 있다
- **함수가 아니라 "형식" 임**!!

> A 객체가 특정 상황이 발생하면 B 객체에게 '이거 좀 처리해줘'라고 부탁하는 방식

+ 언제 쓰일까?
	+ UI 이벤트 처리
	+ 비동기 작업 처리

+ 목적
	+ 객체 간 결합도를 낮추고(loosely coupled), 
	+ 역할을 분리하여 유지보수를 쉽게 하기 위함
	
	1. 역할 분리 - 이벤트 발생하는 객체와 처리하는 객체를 분리할 수 있음
	2. 재사용성 증가 - 동일한 클래스를 다양한 상황에서 활용 가능
	3. 결합도 낮춤 - 직접적인 의존 관게가 줄어 유연성이 높아짐

## 주요 기능

+ 동작 흐름
	1. 프로토콜 정의
	   : "이런 함수들을 구현해야 함"라는 약속을 정의
	```swift
	protocol MyButtonDelegate: AnyObject {
	    func buttonDidTap()   // 위임할 함수
	}
	```
	
	2. 델리게이트 변수 선언
	   : 이벤트를 위임할 객체를 담을 변수(weak var delegate) 선언
	   ```swift
	lass MyButton {
	    weak var delegate: MyButtonDelegate?  // 반드시 weak(순환 참조 방지)
	    func tap() {
	        print("버튼이 눌렸습니다.")
	        delegate?.buttonDidTap()  // 델리게이트에 위임
	    }
	}
	```
		+ ~~weak에 대해 알아봐야 될 것 같다.. (순환 참조도..)~~
	
	3. 프로토콜 채택 및 구현
	   : 다른 클래스에서 해당 프로토콜을 채택하고, 실제 동작을 구현
	   ```swift
		class ViewController: MyButtonDelegate {
		    func buttonDidTap() {
		        print("ViewController에서 버튼 눌림 이벤트 처리!")
		    }
		}
	```
	
	4. 이벤트 발생 시 delegate 호츨
	   : 주체 객체는 이벤트가 발생하면 delegate의 함수를 호출
	   ```swift
		let button = MyButton()
		let vc = ViewController()
		button.delegate = vc
		button.tap()
		// 출력:
		// 버튼이 눌렸습니다.
		// ViewController에서 버튼 눌림 이벤트 처리!
	```

## 코드 예시

+ MPC(Multipeer Connectivity)에서의 Delegate 사용
	+ 흐름
		MultipeerConnectivity 내부 이벤트
	    ↓
		MPCManager(MCSessionDelegate로 콜백 받음)
	    ↓ (자체 프로토콜 델리게이트 호출)
		View/ViewModel (UI 업데이트 or 데이터 처리)
	
	1. 프로토콜 정의 
	```swift
	protocol MPCManagerDelegate: AnyObject {
	    func connectedPeersChanged(_ peers: [MCPeerID])
	    func didReceive(message: String, from peer: MCPeerID)
	}
	```
	
	2. MPCManager 클래스
		- MCSessionDelegate 등 MPC 기본 델리게이트를 채택해 **이벤트를 수신**    
		- 내부에서 **자신이 만든 delegate에게 이벤트를 다시 전달**
	```swift
	import MultipeerConnectivity
	
	class MPCManager: NSObject, ObservableObject {
	    weak var delegate: MPCManagerDelegate?
	
	    private let myPeerID = MCPeerID(displayName: UIDevice.current.name)
	    private var session: MCSession!
	
	    override init() {
	        super.init()
	        session = MCSession(peer: myPeerID, securityIdentity: nil, encryptionPreference: .required)
	        session.delegate = self
	    }
	}
	
	// MARK: - MCSessionDelegate
	extension MPCManager: MCSessionDelegate {
	    func session(_ session: MCSession, peer peerID: MCPeerID, didChange state: MCSessionState) {
	        print("Peer 상태 변경: \(peerID.displayName), state: \(state.rawValue)")
	        DispatchQueue.main.async {
	            self.delegate?.connectedPeersChanged(session.connectedPeers)
	        }
	    }
	
	    func session(_ session: MCSession, didReceive data: Data, fromPeer peerID: MCPeerID) {
	        if let message = String(data: data, encoding: .utf8) {
	            DispatchQueue.main.async {
	                self.delegate?.didReceive(message: message, from: peerID)
	            }
	        }
	    }
	
	    // 나머지는 기본 구현
	    func session(_ session: MCSession, didReceive stream: InputStream, withName streamName: String, fromPeer peerID: MCPeerID) {}
	    func session(_ session: MCSession, didStartReceivingResourceWithName resourceName: String, fromPeer peerID: MCPeerID, with progress: Progress) {}
	    func session(_ session: MCSession, didFinishReceivingResourceWithName resourceName: String, fromPeer peerID: MCPeerID, at localURL: URL?, withError error: Error?) {}
	}
	```
	
	3. ViewModel 또는 ViewController에서 델리게이트 채택
	```swift
	class WaitingRoomViewModel: ObservableObject, MPCManagerDelegate {
	    @Published var connectedPeers: [MCPeerID] = []
	    @Published var receivedMessages: [String] = []
	
	    private var mpcManager = MPCManager()
	
	    init() {
	        mpcManager.delegate = self
	    }
	
	    func connectedPeersChanged(_ peers: [MCPeerID]) {
	        connectedPeers = peers
	    }
	
	    func didReceive(message: String, from peer: MCPeerID) {
	        receivedMessages.append("\(peer.displayName): \(message)")
	    }
	}
	```
	
	4. SwiftUI View에서 ViewModel와 바인딩
	```swift
	struct WaitingRoomView: View {
	    @StateObject private var viewModel = WaitingRoomViewModel()
	
	    var body: some View {
	        VStack {
	            Text("참여자: \(viewModel.connectedPeers.map { $0.displayName }.joined(separator: ", "))")
	            List(viewModel.receivedMessages, id: \.self) { msg in
	                Text(msg)
	            }
	        }
	    }
	}
	```

-> MPCManager가 혼자 모든 일을 하지 않고, "이런 이벤트가 일어나면 너가(ViewModel) 대신 처리해줘" 라고 **부탁**하는 메커니즘 (이벤트 전달자 역할)
+ 비유
	+ MPCManager = 비서
	+ Delegate = 상사
		-> 비서가 알려주고 상사가 직접 처리


>[!Answer]
>델리게이트 = '대신 처리해주는 대리인'


## Keywords
+ 파생된 키워드들을 작성

## References
- 참고한 레퍼런스를 작성 (예 : Apple의 공식 문서)