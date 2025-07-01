>[!question]
>GQ1. 어떻게 사용하는가
>GQ2. 어디에 쓰이는가

## Description
- **ARKit**은 iOS와 iPadOS에서 **현실 세계를 이해하고, 그 위에 가상 콘텐츠를 배치할 수 있게 해주는 프레임워크**
- 카메라, 모션 센서, LiDAR, TrueDepth 등의 **하드웨어 센서와 소프트웨어 알고리즘을 결합**해서 작동함

## 주요 기능
+ 주요 기술
	+ 모션 추적
	+ 수평/수직 평명 인식
	+ 조명 추정
	+ 페이스 트래킹
	+ 이미지 인식
	+ 월드 매핑
	+ 심도 인식 및 사람 인식

+ 주요 클래스
	+ ARSession: AR 경험을 제어하는 세션
	+ ARConfiguration: 어떤 AR 경험을 할지 설정 (예: WorldTracking, FaceTracking 등)
		+ [`ARBodyTrackingConfiguration`](https://developer.apple.com/documentation/arkit/arbodytrackingconfiguration)
		- [`ARFaceTrackingConfiguration`](https://developer.apple.com/documentation/arkit/arfacetrackingconfiguration)
		- [`ARGeoTrackingConfiguration`](https://developer.apple.com/documentation/arkit/argeotrackingconfiguration)
		- [`ARImageTrackingConfiguration`](https://developer.apple.com/documentation/arkit/arimagetrackingconfiguration)
		- [`ARObjectScanningConfiguration`](https://developer.apple.com/documentation/arkit/arobjectscanningconfiguration)
		- [`AROrientationTrackingConfiguration`](https://developer.apple.com/documentation/arkit/arorientationtrackingconfiguration)
		- [`ARPositionalTrackingConfiguration`](https://developer.apple.com/documentation/arkit/arpositionaltrackingconfiguration)
		- [`ARWorldTrackingConfiguration`](https://developer.apple.com/documentation/arkit/arworldtrackingconfiguration)
	+ ARAnchor: 현실 공간에 가상 객체를 고정시키는 기준점
		+ [`ARAppClipCodeAnchor`](https://developer.apple.com/documentation/arkit/arappclipcodeanchor)
		- [`ARBodyAnchor`](https://developer.apple.com/documentation/arkit/arbodyanchor)
		- [`AREnvironmentProbeAnchor`](https://developer.apple.com/documentation/arkit/arenvironmentprobeanchor)
		- [`ARFaceAnchor`](https://developer.apple.com/documentation/arkit/arfaceanchor)
		- [`ARGeoAnchor`](https://developer.apple.com/documentation/arkit/argeoanchor)
		- [`ARImageAnchor`](https://developer.apple.com/documentation/arkit/arimageanchor)
		- [`ARMeshAnchor`](https://developer.apple.com/documentation/arkit/armeshanchor)
		- [`ARObjectAnchor`](https://developer.apple.com/documentation/arkit/arobjectanchor)
		- [`ARParticipantAnchor`](https://developer.apple.com/documentation/arkit/arparticipantanchor)
		- [`ARPlaneAnchor`](https://developer.apple.com/documentation/arkit/arplaneanchor)
	+ ARFrame: 한 순간의 카메라 이미지와 트래킹 정보
	+ ARSCNView/ARView: AR 콘텐츠를 화면에 보여주는 뷰 (SceneKit / RealityKit)
	
	+ 설정 방식
		```swift
		let configuration = ARWorldTrackingConfiguration()
		configuration.planeDetection = [.horizontal, .vertical]
		// planeDetection 수평/수직 평면 탐지 설정
		
		let session = ARSession()
		session.run(configuration)
		```
		- ARWorldTrackingConfiguration: ARKit에서 가장 일반적이고 강력한 추적 설정으로, 사용자의 움직임을 3D 공간에서 인식할 수 있음
			- **6DOF(6 Degrees of Freedom)**: 사용자의 위치(x, y, z) + 방향(roll, pitch, yaw)을 추적
			- **평명 인식**: 수평, 수직 평면 탐지 가능
			- **이미지/객체 인식** 가능


## 코드 예시
+ ARKit + RealityKit
	```swift
	import SwiftUI
	import RealityKit
	
	struct ContentView: View {
	    var body: some View {
	        RealityView { content in
	            let box = ModelEntity(mesh: .generateBox(size: 0.1))
	            box.position = [0, 0.05, 0]
	
	            let anchor = AnchorEntity(plane: .horizontal)
	            anchor.addChild(box)
	
	            content.add(anchor)
	        }
	        .edgesIgnoringSafeArea(.all)
	    }
	}
	```
	- **RealityView**: SwiftUI에서 RealityKit 콘텐츠를 보여주는 뷰
	- **ModelEntity**: 박스 3D 객체 생성
	- **AnchorEntity**: 수평면에 박스를 배치

+ Face Tracking 구현 방법(ARKit + SceneKit)
	+ 기본 설정
	```swift
	let configuration = ARFaceTrackingConfiguration()
	configuration.isLightEstimationEnabled = true
	sceneView.session.run(configuration)
	```
		- ARFaceTrackingConfiguration()
		  : 얼굴 추적 기능을 위한 설정 객체를 생성
		- isLightEstimationEnabled = true
		  : 주변 조도를 측정해서 가상 콘텐츠의 조명과 그림자를 현실감 있게 조절
		- sceneView.session.run(configuration)
		  : 위 설정을 바탕으로 AR 세션을 실행함 (얼굴 추적 시작)
	
	+ 얼굴 표현 값 추출
	```swift
		func renderer(_ renderer: SCNSceneRenderer, didUpdate node: SCNNode, for anchor: ARAnchor) {
	    guard let faceAnchor = anchor as? ARFaceAnchor else { return }
	
	    let blendShapes = faceAnchor.blendShapes
	    let smileLeft = blendShapes[.mouthSmileLeft]?.floatValue ?? 0.0
	    let smileRight = blendShapes[.mouthSmileRight]?.floatValue ?? 0.0
	
	    if smileLeft > 0.5 || smileRight > 0.5 {
	        print("사용자가 웃고 있어요!")
	    }
	}
	```
		- renderer(_:didUpdate:for:)
		  : 얼굴이 업데이트될 때마다 호출되는 델리게이트 메서드
		- guard let faceAnchor = anchor as? ARFaceAnchor else { return }
		  : 이 anchor가 얼굴 관련 앵커인지 확인하고 형변환
		- faceAnchor.blendShapes
		  : 눈, 입, 눈썹 등 얼굴의 움직임 정보를 담고 있는 딕셔너리
		- .mouthSmileLeft, .mouthSmileRight
		  : 왼쪽/오른쪽 입꼬리 움직임의 정도 (0.0 ~ 1.0 사이 값)
		- floatValue ?? 0.0
		  : nil 방지를 위한 기본값 설정
		- if smileLeft > 0.5 ...
		  : 입꼬리가 일정 이상 올라갔다면 웃고 있는 것으로 판단
	- blendShapes
		-  ARFaceAnchor가 제공하는 얼굴 표정 관련 데이터
		- 표정 요소의 움직임을 0.0(움직임 없음) ~ 1.0(최대 움직임) 범위로 표현함

+ Face Tracking 구현 방법(ARKit + RealityKit) - UIKit
	```swift
	import UIKit
	import RealityKit
	import ARKit
	
	class FaceViewController: UIViewController {
	    
	    var arView: ARView!
	
	    override func viewDidLoad() {
	        super.viewDidLoad()
	
	        // 1. ARView 생성
	        arView = ARView(frame: view.bounds)
	        view.addSubview(arView)
	        
	        // 2. Face Tracking 지원 여부 확인
	        guard ARFaceTrackingConfiguration.isSupported else {
	            fatalError("이 기기는 Face Tracking을 지원하지 않습니다.")
	        }
	
	        // 3. 설정 구성
	        let config = ARFaceTrackingConfiguration()
	        config.isLightEstimationEnabled = true
	        arView.session.run(config, options: [])
	
	        // 4. delegate 설정
	        arView.session.delegate = self
	    }
	}
	```
		- ARView
		  : RealityKit 콘텐츠를 렌더링하는 기본 뷰
		- ARFaceTrackingConfiguration
		  : 얼굴 인식용 설정
		- ARSessionDelegate
		  : 얼굴 데이터(앵커 업데이트 등)를 받을 수 있도록 설정
		- blendShapes
		  : 표정 요소 추출 (입꼬리, 눈 깜박임, 찡그림 등)
	
	
	+ ARSessionDelegate 구현 – BlendShapes 출력
		```swift
		extension FaceViewController: ARSessionDelegate {
		    func session(_ session: ARSession, didUpdate anchors: [ARAnchor]) {
		        for anchor in anchors {
		            guard let faceAnchor = anchor as? ARFaceAnchor else { continue }
		            let blendShapes = faceAnchor.blendShapes
		            
		            let smileLeft = blendShapes[.mouthSmileLeft]?.floatValue ?? 0.0
		            let smileRight = blendShapes[.mouthSmileRight]?.floatValue ?? 0.0
		
		            if smileLeft > 0.5 || smileRight > 0.5 {
		                print("사용자가 웃고 있어요! 😄")
		            }
		        }
		    }
		}
		```
	
	+ 직접적으로 SwiftUI만으로는 Face Tracking + delegate 기반 데이터 추출이 어려움
	+ 보통은 UIKit + UIViewControllerRepresentable을 써서 SwiftUI와 연결

## Keywords
+ 파생된 키워드들을 작성

## References
- 참고한 레퍼런스를 작성 (예 : Apple의 공식 문서)