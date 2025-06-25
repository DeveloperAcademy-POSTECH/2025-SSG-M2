>[!question]
>GQ1. WeatherKit은 어디에 사용할까?
>GQ2. WeatherKit은 어떻게 사용할까?

## Description
- WeatherKit은 Apple이 제공하는 새로운 날씨 예보 서비스 API이며, 
  고해상도 모델 + 머신러닝 + 예측 알고리즘을 사용해 정확한 지역 날씨를 제공
  (WeatherKit은 WWDC 2022에서 공개된 Apple에서 제공하는 날씨API로, 애플 기본 날씨 앱이 사용하는 날씨 데이터)

- Apple Weather Service 기반이며, 사용자에게 신뢰할 수 있는 날씨 데이터를 

+ WeatherKit API를 실제로 활용 중인 앱
	1. Apple 기본 Weather 앱
	2. CARROT Weather (WWDC24 언급)
		- iOS/macOS/watchOS에서 인기 있는 날씨 앱
		- 풍부한 커스터마이징 + 위트 있는 UI/UX로 유명
	3. DuckDuckGo Weather (WWDC24 언급)
		- 개인정보 보호 중심의 검색 엔진 및 브라우저 앱
		- 개인 정보 추적 없이 위치 기반 날씨 제공
		- DuckDuckGo의 철학인 프라이버시 보호와 WeatherKit의 설계 철학(위치 익명성)이 잘 맞음

## 주요 기능
- ==**Apple Weather(Apple Weather Service) 데이터**== 기반
	- Apple이 직접 운영하는 기상 데이터 서비스 인프라이자 백엔드 서버
	- Apple 플랫폼의 날씨 데이터(Powered by Apple Weather)를 활용해 앱 및 웹서비스에 현재 날씨, 10일간 시간별/일일 예보, 바람, UV 지수 등 유용한 정보를 제공

+ 개인정보 보호
	+ 위치 데이터는 예보용으로만 사용됨
	+ 사용자의 식별 정보와 연결되지 않으며, 저장되거나 판매되지 않음
	+ 개발자가 **프라이버시 설계를 쉽게 준수**할 수 있도록 설계됨

- ==**Swift API**== 및 ==**REST API**== 지원
	- iOS, macOS, watchOS, visionOS 등의 ==Apple 앱에는 Swift API==를, 
	 ==플랫폼 및 웹에서는 REST API==를 사용 가능

+ 제공되는 데이터 세트
	+ **현재 날씨**: 자외선 지수, 기온, 풍속 등 현재 상태
	+ **분(min) 예보**: 1분 단위 강수량 예측 (최대 1시간)
	+ **시간별(hourly) 예보**: 최대 240시간의 예보 (습도, 기압, 이슬점 등)
	+ **일일(daily) 예보**: 최대 10일 예보 (최고/최저 기온, 일출/일몰 등)
	+ **기상 경보(alerts)**: 지역별 기상 주의보 또는 경고
	+ **과거 날씨(history)**: 과거의 시간별/일일 날씨 조회 가능 (swift API 미지원)

+ Swift API 사용법
	1. WeatherKit & CoreLocation import
	2. WeatherService() 인스턴스 생성
	3. CLLocation(latitude, longitude) 생성
	4. weather(for:) 호출
	5. 반환된 객체에서 현재 기온, 습도 등 추출

+  SwiftUI 앱 예시:
	+ 비행 일정을 위한 앱에서 각 공항 위치의 날씨 정보를 표시
	+ 어트리뷰션(출처 표시)도 자동으로 처리 가능

+ REST API 사용법
	+ REST API는 Swift 이외의 플랫폼(예: 웹, 서버)에서도 사용할 수 있음
	1. 인증 키 발급 (Developer Portal에서)
	2. JWT 토큰 생성 (서명 포함)
	3. 날씨 요청 URL 구성 (좌표, 언어, 데이터셋 포함)
	4. HTTP 요청 시 Authorization 헤더에 JWT 포함
	5. 응답은 JSON 형식으로 수신
	

## 코드 예시
+ Swift API 사용법
	1. WeatherKit & CoreLocation import
	2. WeatherService() 인스턴스 생성
	3. CLLocation(latitude, longitude) 생성
	4. weather(for:) 호출
	5. 반환된 객체에서 현재 기온, 습도 등 추출
	```swift title:1
	// 1. WeatherKit & CoreLocation import
	import WeatherKit // 날씨 정보를 가져오는 Apple 프레임워크
	import CoreLocation // 위치 정보를 다루기 위한 프레임워크 (위도/경도 등)
	```
	
	```swift title:2
	// 2. WeatherService()인스턴스생성
	let weatherService = WeatherService() 
	```
		날씨 정보를 요청할 수 있는 핵심 객체
		해당 객체 생성 이후에 weather(for:)를 통해 데이터를 가져올 수 있음
	
	```swift title:3
	// 3. CLLocation(latitude, longitude) 생성
	let syracuse = CLLocation(latitude: 43, longitude: -76)
	```
		날씨 정보를 가져올 위치 좌표 정의
		Syracuse(시러큐스) 지역의 위도/경도
	
	```swift title:4
	// 4. weather(for:) 호출
	let weather = try! await weatherService.weather(for: syracuse)
	```
		비동기 호출로 해당 위치의 날씨 데이터 요청
		await 키워드는 비동기 작업이 완료될 때까지 기다림,
		try!는 예외 발생 가능성이 있지만 강제 실행하는 것(주의해서 사용하기)
		반환된 weather 객체 안에는 현재/시간별/일일 날씨 정보 등 
		제공되는 데이터 세트가 담겨있음
	
	```swift title:5
	// 5. 반환된 객체에서 현재 기온, 자외선 지수 등 추출
	let temperature = weather.currentWeather.temperature
	let uvIndex = weather.currentWeather.uvIndex
	```
		현재 날씨(currentWeather)에서 기온을 추출
		temperature는 Measurement<UnitTemperature> 타입 (섭씨/화씨 변환 가능)
		현재 날씨에서 자외선 지수를 추출
		uvIndex는 Int형으로, 보통 0~11+로 위험도를 나타냄

+ Swift API 실제 사용 예시
	```swift title:ViewModel
	// SwiftUI에서 메인 스레드에서만 동작하도록 보장
	@MainActor
	class WeatherViewModel: NSObject, ObservableObject, CLLocationManagerDelegate {
	    
	    // 날씨 정보를 담는 Published 프로퍼티 (뷰가 자동 업데이트됨)
	    @Published var weather: Weather?
	    
	    // 에러 메시지를 담는 Published 프로퍼티
	    @Published var errorMessage: String?
	
	    // WeatherKit의 핵심 서비스 객체 (날씨 요청을 담당)
	    private let weatherService = WeatherService()
	    
	    // 위치 정보를 받아오기 위한 CLLocationManager 인스턴스
	    private let locationManager = CLLocationManager()
	    
	    // 현재 사용자의 위치 정보
	    private var currentLocation: CLLocation?
	
	    // ViewModel 초기화 시 위치 권한 요청 및 위치 업데이트 시작
	    override init() {
	        super.init()
	        locationManager.delegate = self  // 위치 업데이트 콜백 받기 위해 delegate 지정
	        locationManager.requestWhenInUseAuthorization() // 앱 사용 중 위치 접근 권한 요청
	        locationManager.startUpdatingLocation() // 위치 업데이트 시작
	    }
	
	    // 비동기 방식으로 날씨 데이터 요청
	    func fetchWeather() async {
	        // 위치 정보가 없을 경우 에러 처리
	        guard let location = currentLocation else {
	            errorMessage = "위치 정보를 불러오지 못했어요."
	            return
	        }
	
	        do {
	            // 현재 위치 기준 날씨 요청 (WeatherKit API 호출)
	            let weather = try await weatherService.weather(for: location)
	            self.weather = weather  // 응답 받은 날씨 데이터를 Published 변수에 저장
	        } catch {
	            // 요청 중 에러가 발생하면 메시지 저장
	            errorMessage = "날씨 데이터를 가져오지 못했어요: \(error.localizedDescription)"
	        }
	    }
	
	    // 위치가 성공적으로 업데이트될 때 호출되는 delegate 메서드
	    func locationManager(_ manager: CLLocationManager, didUpdateLocations locations: [CLLocation]) {
	        currentLocation = locations.first // 가장 최신 위치 저장
	        locationManager.stopUpdatingLocation() // 위치 업데이트 중단 (배터리 절약)
	    }
	
	    // 위치 업데이트에 실패했을 때 호출되는 delegate 메서드
	    func locationManager(_ manager: CLLocationManager, didFailWithError error: Error) {
	        errorMessage = "위치 가져오기 실패: \(error.localizedDescription)" // 에러 메시지 저장
	    }
	}
	```
	
	```swift title:View
	import SwiftUI
	import WeatherKit
	import CoreLocation
	
	struct ContentView: View {
	    @StateObject private var viewModel = WeatherViewModel()
	
	    var body: some View {
	        VStack(spacing: 20) {
	            if let weather = viewModel.weather {
	                Text("🌤 현재 기온: \(weather.currentWeather.temperature.formatted())")
	                Text("🌡 체감 온도: \(weather.currentWeather.apparentTemperature.formatted())")
	                Text("🌞 자외선 지수: \(weather.currentWeather.uvIndex)")
	                Text("🌬 풍속: \(weather.currentWeather.wind.speed.formatted())")
	            } else if viewModel.errorMessage != nil {
	                Text("❌ \(viewModel.errorMessage!)")
	                    .foregroundColor(.red)
	            } else {
	                ProgressView("날씨 정보를 불러오는 중...")
	            }
	        }
	        .task {
	            await viewModel.fetchWeather()
	        }
	        .padding()
	    }
	}
	```

+ REST API 사용법
	+ REST API는 Swift 이외의 플랫폼(예: 웹, 서버)에서도 사용할 수 있음
	1. 인증 키 발급 (Developer Portal에서)
	2. JWT 토큰 생성 (서명 포함)
	3. 날씨 요청 URL 구성 (좌표, 언어, 데이터셋 포함)
	4. HTTP 요청 시 Authorization 헤더에 JWT 포함
	5. 응답은 JSON 형식으로 수신
	   
	해당 API는 브라우저에서 직접 실행 X
	-> JWT는 비밀키 기반이므로 서버에서 생성하고, 클라이언트는 그 토큰만 받아 사용해야 함
	
	```javascript
	/* 1. 인증 토큰 요청 */
	const tokenResponse = await fetch('https://example.com/token');
	const token = await tokenResponse.text();
	```
		https://example.com/token에서 JWT 토큰을 받아오는 서버 API를 호출
		WeatherKit REST API는 JWT(Json Web Token) 기반 인증
	
	```javascript
	/* 2. 날씨 데이터 요청 (기상 경보) */
	const url = "https://weatherkit.apple.com/1/weather/en-US/41.029/-74.642?dataSets=weatherAlerts&country=US"
	const weatherResponse = await fetch(url, {
	  headers: {
	    "Authorization": token
	  }
	});
	const weather = await weatherResponse.json();
	```
		https://weatherkit.apple.com/1/weather/... 는 
		Apple의 REST API 엔드포인트
		en-US: 응답 언어 설정
		41.029, -74.642: 위도, 경도 (미국 뉴욕 북부 정도)
		dataSets=weatherAlerts: 기상 경보 데이터만 요청
		country=US: 경보 데이터에는 국가 코드 필수
		REST API 요청 시 JWT 토큰을 Authorization 헤더에 포함해 보냄
	    응답은 JSON 형식으로 날씨 데이터가 담긴 객체
	
	```javascript
	/* 3. 기상 경보 확인 */
	const alerts = weather.weatherAlerts;
	const detailsUrl = weather.weatherAlerts.detailsUrl;
	```
		weatherAlerts: 기상 경보 배열 (없으면 빈 배열일 수 있음)
		detailsUrl: 경보 상세 페이지 링크 (Apple에서 제공)


## Keywords
+ Apple Weather Service
+ Swift API
+ REST API

## References
- Meet WeatherKit (WWDC22)
- WeatherKit Documentation

ldkfjlkasjdfkljadlkf'