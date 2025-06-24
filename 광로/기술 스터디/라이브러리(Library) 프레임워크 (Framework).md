>[!question]
>GQ1. 라이브러리 프레임워크의 차이??


## Description
- 쉽게 설명하면 외부 코드를 재사용 하는 방식이며, 구조와 사용 목적에 약간의 차이가 있음

1. 프레임 워크 
-> 사용자가 작성한 추가 코드에 의해 선택적으로 변경되어  APP 고유의 소프트웨어를 제공할 수 있는 추상화 기술 sk##$(#J)@JD (뭐라는 거임 개어렵네 ..)

쉽게 이야기 하면..

=="개발자에게 구조와 형태를 제공하는 뼈대"== 정도라고 알면된다.
이에 프레임워크가 제공하는 "뼈대"위에 추가 코드를 작성해야 개발 할 수 있다고 생각하면 됨

- 프레임 워크를 사용 하는 목적 (이유) = '생산성'
  
1) 구조적인 코드나 라이브러리가 포함
   
 -> 프레임 워크의 기반 코드만큼 코드를 덜 작성할 수 있음
   ex) 프레임워크라는 '차'를 제공 받으면 제공 받은 차를 운전반 하면됨..
   만약 프레임 워크가 없다고 '차'를 만들어서 운전해야 함

2) 공통의 틀을 제공하여 코드 품질을 높임
-> 사람마다 코드 습관이 다르고 선호하는 패턴드 다름, 
프레임 워크를 이용하면 어느 정도는 틀이 잡혀서 거기에 맞추면 통일성이 생겨 전체적인 가독성도 좋아지고 '협업'에도유리하다

## 프레임워크 예시

1) UIKit (https://developer.apple.com/documentation/uikit)
-> 뷰기반 구조 (UIView, UIViewController)
코드 + 스토리보드 기반 UI 구성
그 외에 000Kit 등등등

2) Foundation (https://developer.apple.com/documentation/foundation)
-> 데이터, 날짜, 문자열, 파일 처리 등 로우레벨 로직 담당

- 주요 기능 
  
- 날짜/시간: Date, Calendar, DateFormatter
    
- 문자열 처리: String, NSAttributedString
    
- 컬렉션: Array, Dictionary, Set
    
- 파일 및 네트워크 I/O: FileManager, URLSession
    
- 타이머, 스레드, Notification, Codable 등
  
3) SwiftUI (https://developer.apple.com/documentation/swiftui/)
   
   -> 선언형 UI를 지향하는 최신 프레임워크 (쉽게 말해 어떻게 보여질지 선언하면 시스템이 처리)
3-1) struct 기반의 View 구성 (스토리 보드 없이 코드로만 UI 작성)
3-2) @State , @Binding, @Environment emd 데이터-UI 바인딩
대부분 알거 같아서 ...ㅎㅎ



## 라이브러리 (Library)

-> 소프트웨어 개발에 필요한 특정 기능을 위해 작성한 코드, 함수들의 집합
사람들이 API랑 헷갈릴때가 많은데

1) 라이브러리 - 구현 로직이 존재하는 컴포넌트 그 자체
   -> 재사용 가능한 코드의 모음 , 어떤 기능을 수행하는 코드가 모여있는 컴포넌트 집합
   
- 특징 
   - 개발자가 직접 불러서 사용 (직접 호출)
   - 함수, 클래스, 데이터 타입 등이 포함

     
2) API - 라이브러리를 활용하는 규약
	-> 기능을 사용하기 위한 규칙과 약속
	- 특정 소프트웨어의 기능을 외부에서 접근할 수 있도록 만든 인터페이스  

- 특징
- 어떤 라이브러리나 시스템을 "어떻게 사용할 수 있는지" 알려 주는 약속
- 함수명, 파라미터, 응답형식 등의 "규칙"을 말함
   
   
- 사용하는 이유 - 생산성과 편의성
-> 다른 사람이 만들어 둔 라이브러리를 필요할 때 가져다 쓰기만 하면됨!!
반대로 내가 구현한 기능을 다른 사람에게 제공할 수도 있음

![[스크린샷 2025-06-22 오전 12.55.20.png]]

ex) 아주 적절한 예시 ㅋㅋㅋㅋㅋㅋ


## 라이브러리 예시

1) snapkit
   
   -> AutoLayout을 코드로 쉽게 작성하게 도와주는 라이브러리
- UIKit의 Auto Layout을 더 간결하게 작성할 수 있도록 도와줌
- NSLayoutConstaint 코드가 복잡한 걸 보기 좋게 정리해줌
```swift
titleLabel.translatesAutoresizingMaskIntoConstraints = false
NSLayoutConstraint.activate([
    titleLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 20),
    titleLabel.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 16)
])
```

snapkit 사용시

``` swift
titleLabel.snp.makeConstraints {
    $0.top.equalToSuperview().offset(20)
    $0.leading.equalToSuperview().offset(16)
}

1) 코드로 UI 구성할 때 Auto Layout을 간단하게 쓰고 싶을 때
2) 스토리보드 없이 작업하는 UIKit 기반 프로젝트에서 특히 유용
```

- rxswift - 비동기 이벤트를 함수형으로 다루게 해주는 리액티브 프로그램핑 라이브러리
-> 복잡한 이벤트 조합이나 상태 변화 처리를 선언적으로 구성가능
- **MVVM 아키텍처**에서 ViewModel <-> View 간 바인딩할 때


- alamofire - HTTP 네트워킹을 간단하게 해주는 라이브러리
1) Apple의 URLSession을 감싼 래퍼 라이브러리
2) REST API 요청, JSON 응답피싱, 업로드/다운로드 등을 간편

- 복잡한 API 요청/응답 처리할 때
- URLSession을 직접 쓰기에는 코드가 너무 많아 질때 .
   등등이 있음

## 정리

2가지의 가장 큰 차이는 Flow(흐름)의 제어에 대한 주도성

+ 프레임워크
-> 프레임워크가 뼈대를 제공하고 개발자는 뼈대에 살을 붙인다.
즉 주도권이 프레임워크에 있다.

- 라이브러리
-> 주도권이 개발자 즉 호출하는 쪽에 있음
개발을 하면서 필요한 라이브러리가 있을 때 개발자가 호출을 한다.
전체 흐름의 주도권이 호출하는 쪽에 있는 것,

결국에는 효율적인 개발을 하기 위해서는 적절한 2가지를 잘 사용 해야함