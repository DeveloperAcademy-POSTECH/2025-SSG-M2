

### MusicKit

-> Swift을 사용해 App에서 음악 항목에 접근할 수 있는 프레임 워크

1) Apple Music의 컨텐트 (노래, 플레이리스트 등 ) 검색
2) 해당 컨텐츠에 대한 메타데이터 (제목, 아티스트, 앨범커버 등) 제공
3) MediaPlayer 프레임워크를 활용한 노래 재생-멈춤기능 제공

플레이어,검색, 뮤직비디오, 음악 데이터 필터링, 추천, 음원 음질, 음악차트 , 과거기록, 라디오
다 사용할 수 있음!!

*여기서 포인트*
-> ==Apple Radio는 구독하지 않아도 무료료 청취 가능==


단, MusicKit을 활용한 앱을 사용하려면 Apple Music 구독이 되어 있어야함


- 주요 클래스
  
1) MusicCatalogSearchRequest - Apple Music 카탈로그에서 음악, 앨범, 아티스트 검색
2) MusicPlayer - 음악 재생 제어. play(), pause(), queue 등의 기능 포함
3) ApplicationMusicPlayer.shared - 앱 전용 재생 인스턴스 (Apple Music 플레이어와 별개)
4) MusicItemCollection - 곡, 앨범, 아티스트 등 아이템의 배열 (리스트로 다룰 때 사용)
5) Song, Album, Artist - 각각 곡/앨범/아티스트를 나타내는 기본 데이터 모델
6) MusicAuthorization - Apple Music 접근 권한 요청 (request())
7) MusicLibraryRequest - 사용자의 보관함에서 데이터를 불러올 때 사용
8) MusicSubscription - 현재 사용자의 Apple Music 구독 상태 확인



- 사용자 인증 및 음악 가져오기

``` swift

import MusicKit

Task {
    do {
        let status = try await MusicAuthorization.request()
        // Apple Music 권한요청 사용하도록 권한 요청
        if status == .authorized {
        // 사용자가 권한을 허용한 경우에만 아래 코드 실행
            let request = MusicCatalogSearchRequest(term: "Taylor Swift", types: [Song.self])
            // "Taylor Swift"라는 검색어로 Apple Music 카탈로그를 검색
            let response = try await request.response()
            let songs = response.songs
            print("찾은 곡 수: \(songs.count)")
        }
    } catch {
        print("오류 발생: \(error)")
    }
}

```






### AVFoundation

-> 애플 플랫폼에서 동적 시청각 미디어 작업을 위한 모든 기능을 갖춘 프레임워크
- Apple 플랫폼에서 시청각 미디어를 검사, 재생, 캡쳐 및 처리하기 위한 광범위한 작업을 포괄하는 여러 주요 기술영역을 결합한 프레임워크
- iOS, tvOS, macOS를 위한 미디어 프레임 워크 (비디오, 오디오, 애니메이션에 사용)

- 활용방법
1) HLS - 고화질 영상 스트리밍 및 AirPlay 스트리밍
2) 동적 시청각 미디어 표현 및 활용
3) 디바이스 캡쳐 (영상 녹화, 카메라 활영 녹음) 및 저장
4) 동적 시청각 미디어 편집



- 간단한 오디오 재생 코드

```swift
import AVFoundation

var player: AVAudioPlayer?

func playSound() {
// 소리를 재생하는 함수 정의
    if let path = Bundle.main.path(forResource: "sound", ofType: "mp3") {
    // Bundle.main = 앱 내부 자원 접근
        let url = URL(fileURLWithPath: path)
        // 문자열 형태의 파일 경로를 URL 객체로 변환
        do {
            player = try AVAudioPlayer(contentsOf: url)
            // mp3파일을 불러옴
            player?.play()
            // 오디오 재생시작
        } catch {
            print("오디오 재생 실패: \(error)")
        }
        // 재생중 문제가 생기면 애러 출력
    }
}

// sound.mp3 오디오 파일을 앱 내부에서 찾아서 재생하는 코드
// AVAudioPlayer는 간단한 효과음 재생이나 로컬 오디오 파일 재생에 적합.
```


- 주요 클래스 

1) AVAudioPlayer - 오디오 파일 재생용 클래스 (MP3, WAV 등)
2) AVAudioRecorder - 마이크로 입력된 오디오 녹음
3) AVAudioSession - 오디오 환경 설정 (녹음/재생 모드, 백그라운드 설정 등)
4) AVPlayer - 비디오/오디오 스트리밍 재생 (스트리밍, 로컬 둘 다 가능)
5) AVPlayerItem - AVPlayer에서 재생할 개별 미디어 콘텐츠
6) AVCaptureSession - 카메라 및 마이크 입력 세션 관리
7) AVCaptureDevice - 실제 카메라 및 마이크 장치에 대한 접근
8) AVCaptureVideoPreviewLayer - 카메라 프리뷰를 UI에 렌더링할 때 사용
9) AVAsset - 오디오/비디오 미디어 파일 추상화 객체
10) AVAudioEngine - 오디오 처리용 실시간 엔진 (이펙트, 믹싱, 필터 등 고급기능 가능)