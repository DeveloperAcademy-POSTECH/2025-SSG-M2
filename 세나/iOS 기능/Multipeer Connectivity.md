>[!question]
>GQ1. 어떻게 쓰는 걸까?
>GQ2. 어디에 쓰이는 걸까?

## Description
- Multipeer Connectivity는 iOS에서 **근거리 무선 네트워크(P2P)**를 통해 **장치 간 데이터 교환**을 가능하게 하는 프레임워크이다. Wi-Fi, Bluetooth, Ad-hoc Wi-Fi 및 인터넷 연결이 필요 없는 Peer-to-Peer 통신을 지원한다.
  이 기능을 통해 **인터넷 없이도** 같은 공간의 여러 iOS 기기가 서로 데이터를 주고받을 수 있다.

- iOS 7 이상에서 제공
-  Bonjour 서비스 탐색 및 세션 기반 데이터 전송
-  비동기 이벤트 기반 콜백(delegate/closure)로 작동
- 텍스트, 이미지, 동영상 등 파일 전송 가능

## 주요 기능
1. **Peer Discovery**
    - 주변 기기를 **탐색(Advertiser/Browser)**하고 연결할 수 있습니다.
    - MCNearbyServiceAdvertiser(호스트 역할)와 MCNearbyServiceBrowser(탐색자 역할) 사용.

2. **Session Management**
    - MCSession을 이용해 연결된 피어 간 **세션 유지 및 데이터 전송**.

3. **Data Transmission**
    - 텍스트, JSON, 바이너리 데이터, 파일 전송 가능.
    - send(_:toPeers:with:) 메서드로 전송.

4. **Automatic Connection Handling**
    - 연결이 끊어지면 자동으로 재연결 시도 가능.

+ EX
	1. 사진, 파일, 스트리밍 데이터, 메시지 등 전송
    2. 게임, 협업 앱, 채팅 앱 등에서 활용 가능
    3. AirDrop 같은 근거리 데이터 전송
    4. 최대 8명까지 안정적인 연결 가능 (권장 인원 수)

+ 핵심 개념
	`MCSession` - 피어 간 실제 데이터 통신을 담당하는 세션
	`MCPeerID` - 기기를 식별하는 ID
	`MCNearbySeviceAdvertiser` - 본인의 서비스(존재)를 주변에 알리는 역할
	`MCNearbyServiceBrowser` - 주변의 피어를 검색하는 역할
	`MCSessionDelegate` - 데이터 수신, 피어 연결 상태 등을 감지하는 델리게이트
## 코드 예시
+ 사용 흐름
	1. Peer ID 생성
		```swift
		let peerID = MCPeerID(displayName: UIDevice.current.name)
		```
	2. Session 생성
		```swift
		let session = MCSession(peer: peerID, securityIdentity: nil, encryptionPreference: .required)
		```
	3. Advertiser 혹은 Browser 시작
		+  Advertiser: 본인의 존재를 주변에 알림
		```swift
		let session = MCSession(peer: peerID, securityIdentity: nil, encryptionPreference: .required)
		```
		+ Browser: 주변의 피어를 검색
		```swift
		let browser = MCNearbyServiceBrowser(peer: peerID, serviceType: "my-service")
		browser.startBrowsingForPeers()
		```
	4. 연결 수락 및 데이터 송수신

+ 코드 전체 예시
	```swift
	import MultipeerConnectivity
	
	class MPCManager: NSObject {
	    private let serviceType = "my-app-service"
	    
	    private let myPeerId = MCPeerID(displayName: UIDevice.current.name)
	    private var session: MCSession!
	    private var advertiser: MCNearbyServiceAdvertiser!
	    private var browser: MCNearbyServiceBrowser!
	    
	    override init() {
	        super.init()
	        session = MCSession(peer: myPeerId, securityIdentity: nil, encryptionPreference: .required)
	        session.delegate = self
	        
	        advertiser = MCNearbyServiceAdvertiser(peer: myPeerId, discoveryInfo: nil, serviceType: serviceType)
	        advertiser.delegate = self
	        
	        browser = MCNearbyServiceBrowser(peer: myPeerId, serviceType: serviceType)
	        browser.delegate = self
	    }
	    
	    func startHosting() {
	        advertiser.startAdvertisingPeer()
	    }
	    
	    func joinSession() {
	        browser.startBrowsingForPeers()
	    }
	    
	    func send(message: String) {
	        if !session.connectedPeers.isEmpty {
	            do {
	                try session.send(message.data(using: .utf8)!,
	                                 toPeers: session.connectedPeers,
	                                 with: .reliable)
	            } catch {
	                print("Error sending message: \(error.localizedDescription)")
	            }
	        }
	    }
	}
	
	extension MPCManager: MCSessionDelegate, MCNearbyServiceAdvertiserDelegate, MCNearbyServiceBrowserDelegate {
	    // 필수 delegate 메서드 구현 (예: 데이터 수신)
	    func session(_ session: MCSession, didReceive data: Data, fromPeer peerID: MCPeerID) {
	        let receivedMessage = String(data: data, encoding: .utf8)
	        print("Received: \(receivedMessage ?? "") from \(peerID.displayName)")
	    }
	
	    func advertiser(_ advertiser: MCNearbyServiceAdvertiser, didReceiveInvitationFromPeer peerID: MCPeerID, withContext context: Data?, invitationHandler: @escaping (Bool, MCSession?) -> Void) {
	        invitationHandler(true, session)
	    }
	
	    func browser(_ browser: MCNearbyServiceBrowser, foundPeer peerID: MCPeerID, withDiscoveryInfo info: [String : String]?) {
	        browser.invitePeer(peerID, to: session, withContext: nil, timeout: 30)
	    }
	
	    // 생략된 나머지 delegate 메서드는 필요에 따라 구현
	}
	```
	
	사용 흐름
	```swift
	let mpc = MPCManager()
	mpc.startHosting()  // 광고 시작 (호스트)
	mpc.joinSession()   // 주변 피어 탐색 (클라이언트)
	mpc.send(message: "Hello Peer!") // 메시지 전송
	```

+ SwiftUI에서 메시지 전송 UI 예시
	```swift
		struct ContentView: View {
		    @StateObject private var mpc = MPCManager()
		    @State private var message = ""
		    
		    var body: some View {
		        VStack {
		            TextField("메시지 입력", text: $message)
		                .textFieldStyle(RoundedBorderTextFieldStyle())
		            Button("전송") {
		                mpc.send(message: message)
		            }
		        }
		        .onAppear {
		            mpc.startHosting()
		            mpc.joinSession()
		        }
		    }
		}
		```
	+ `@StateObject private var mpc = MPCManager()`
	  : `MPCManager`는 Multipeer Connectivity의 핵심 로직(세션 생성, 광고, 탐색, 데이터 전송 등)을 담당하는 클래스이다.
	+ `mpc.send(message: message)`
	  : `MPCManager`의 send 메서드를 호출해서, 입력한 메시지를 연결된 피어들에게 보낸다.
	+ `mpc.startHosting()`
	  : 광고 시작(Advertiser 역할), 즉 내 기기의 존재를 주변에 알린다.
	+ `mpc.joinSession()}`
	  : 탐색 시작(Browser 역할), 주변의 피어를 검색해서 연결을 시도한다.
	
	+ 실행 흐름
	  1. **화면이 켜지면 (onAppear)**
	    - 기기가 자동으로 네트워크에 자신의 존재를 알리고(startHosting)
	    - 동시에 주변 기기를 검색함(joinSession).
	2. **사용자가 메시지를 입력하고 전송 버튼을 누르면**
	    - message 상태값이 `mpc.send()`로 전달되어
	    - 연결된 모든 피어에게 텍스트가 전송됨
	3. **상대방 기기에서는**
	    - `MPCManager` 내부의 `MCSessionDelegate`가 `didReceive` 콜백을 통해 메시지를 수신하고 처리함


> [!answer]
> **GQ1. 어떻게 쓰는 걸까?**  
> - `MCPeerID`로 내 기기를 식별하고, `MCSession`으로 연결 세션을 만든다.  
> - `MCNearbyServiceAdvertiser`(호스트) 또는 `MCNearbyServiceBrowser`(탐색자)로 기기를 연결한다.  
> - 연결 후 `send(_:toPeers:with:)`로 데이터 전송, delegate로 수신 처리.  
>   
> **GQ2. 어디에 쓰이는 걸까?**  
> - 오프라인 환경의 **멀티플레이 게임, 채팅 앱, 협업 도구**  
> - **AirDrop 대체용 P2P 파일 공유**  
> - 교육용 앱, 회의 자료 공유 등 로컬 네트워크 기반 실시간 데이터 공유
## Keywords
+ 파생된 키워드들을 작성

## References
- 참고한 레퍼런스를 작성 (예 : Apple의 공식 문서)