자유도 높은 대화 
학습을 통해 인간의 감정을 얼마나 잘 분석할 수 있을까? 
비꼬는 문장도 화자의 의도에 맞게 분석할 수 있을까? 
→ 이런걸 생각하고 있다면 GPT 같은 생성모델을 같이 사용해야함

## CreateML 사용 범위

> 머신러닝 모델을 학습하고 .mlmodel 파일로 저장하는 도구

### 사용처

- Swift 코드 또는 GUI 앱(Create ML App)을 통해 사용
- macOS 전용 (CreateML은 macOS에서만 사용 가능)

### 만들 수 있는 모델 종류 (2025 기준)

|모델 종류|설명|
|---|---|
|Text Classifier|감정 분석, 뉴스 분류 등|
|Image Classifier|고양이 vs 개 구분 같은 이미지 분류|
|Object Detector|이미지 속 특정 객체 위치 인식|
|Tabular Classifier / Regressor|CSV 데이터 기반 분류/회귀|
|Activity Classifier|센서 데이터 기반 행동 예측|
|Sound Classifier|소리 종류 분류|
|Word Tagger|문장에서 품사 같은 라벨 붙이기|
|Style Transfer|이미지 스타일 변환|

### 한계

- 모델의 자유도/복잡도는 제한적
- 학습 파라미터 커스터마이징 제한적
- **LLM(대형 언어 모델) 등 생성형 AI는 지원 안 함**

## CoreML 사용 범위

> 학습된 모델(.mlmodel)을 **앱 안에서 실행(추론)**하는 Apple의 ML 런타임

### 사용처

- iOS, macOS, watchOS, tvOS 앱에 내장
- Swift 또는 Objective-C에서 사용

### 주요 기능

- `.mlmodel`을 자동으로 Swift 클래스로 변환하여 사용
- Apple의 하드웨어 가속(NPU, GPU, CPU)을 자동 활용
- 외부 모델(PyTorch, TensorFlow 등)도 CoreML 형식으로 변환 가능

### 대표 사용 예시

|기능|설명|
|---|---|
|얼굴 인식|카메라 앱에서 사용자 얼굴 탐지|
|건강 데이터 예측|심박수 기반 위험도 추정|
|음성 인식|음성으로 명령 분석|
|텍스트 자동 분류|입력 문장 감정 파악|
|오프라인 이미지 분류|인터넷 없이 모델 추론 가능|

### CreateML & CoreML 사용 범위 정리

|구분|CreateML|CoreML|
|---|---|---|
|**역할**|모델 **학습**|모델 **실행(추론)**|
|**사용 시점**|모델을 만들고 훈련할 때|학습된 모델을 앱에 넣고 실행할 때|
|**사용 장소**|주로 macOS (Xcode, Playground, Create ML 앱)|iOS/macOS/watchOS 앱 등 Apple 디바이스 내|
|**입출력**|학습 데이터 → `.mlmodel`|`.mlmodel` → 예측 결과|
|**접근 방식**|Swift 코드, GUI|Swift 코드 (Xcode가 자동 생성한 클래스 사용)|

### ** 함께 사용하는 흐름

[Create ML or Python] → 모델 학습 → .mlmodel 파일 생성 → [Core ML (앱)]에서 모델 불러오기 → 추론 실행

예시)

|1. 데이터 준비|CSV|문장 + 감정 레이블|
|---|---|---|
|2. 모델 학습|Create ML|감정 분류기 `.mlmodel` 생성|
|3. 앱에 적용|Core ML|유저가 입력한 텍스트 감정 추론|

## 요약 비교

|항목|CreateML|CoreML|
|---|---|---|
|사용 목적|모델 학습|모델 실행|
|입력|CSV, 이미지, 오디오 등|`.mlmodel` 파일|
|출력|`.mlmodel` 파일|예측 결과 (`String`, `Double`, 등)|
|플랫폼|macOS (Xcode/Playground/GUI 앱)|iOS, macOS, watchOS 등 앱 내|
|기술 레벨|쉬움 (GUI 지원)|중급 (Swift 사용 필요)|
|확장성|낮음 (단순 모델용)|높음 (외부 모델도 실행 가능)|

Q) GPT API를 사용할 때 Create ML이나 Core ML가 필요할까?

|항목|GPT API (예: ChatGPT, Claude)|Create ML + Core ML|
|---|---|---|
|모델 생성|❌ (이미 학습되어 있음)|✅ (직접 학습 가능)|
|텍스트 생성|✅ 매우 뛰어남|❌ 불가능|
|문맥 이해|✅ 수천 단어 기억|❌ 기억 불가|
|실행 위치|서버 (클라우드)|디바이스 로컬|
|속도|느릴 수 있음 (API 호출)|빠름 (온디바이스 실행)|
|비용|유료 (API 호출당 과금)|무료 (한 번 학습하면 재사용 가능)|
|프라이버시|클라우드 전송 필요|오프라인 가능|

GPT만 써도 되는 경우

|조건|설명|
|---|---|
|자연어 생성이 필요함|AI가 대화, 시나리오, 요약 등 "직접 말"해야 할 때|
|복잡한 문맥 이해|수십 문장 이상의 대화 흐름 추적|
|유연한 응답 필요|사전 정의된 답이 아닌 창의적이고 다양한 표현이 필요한 경우|
|빠른 개발 원함|학습 없이 곧바로 API 호출 가능|

Create ML + Core ML이 유용한 경우 (GPT와 보완 가능)

|상황|설명|
|---|---|
|로컬에서 작동해야 함|오프라인 환경 또는 프라이버시 보호 중요할 때|
|단순하고 반복적인 작업|감정 분석, 명령 분류, 키워드 추출 등|
|GPT 호출 줄이고 싶을 때|비용 줄이기 위해 로컬에서 미리 분류 후 GPT 최소 호출|
|디바이스에 저장 가능한 ML 모델 필요|앱 번들에 내장하여 빠르게 실행할 때|

## 1. Create ML 예제 – 모델 학습

> macOS에서 Swift Playground 또는 Xcode Playground에서 실행 가능 CSV 데이터 예시: text,label 컬럼 포함 (예: "오늘 너무 좋아",positive)

```swift
import CreateML
import Foundation

// CSV 파일에서 데이터 불러오기
let data = try MLDataTable(contentsOf: URL(fileURLWithPath: "/Users/yourname/Desktop/sentiment.csv"))

// 데이터 분할: 학습 80%, 테스트 20%
let (trainingData, testData) = data.randomSplit(by: 0.8, seed: 1)

// 텍스트 분류 모델 학습
let sentimentClassifier = try MLTextClassifier(trainingData: trainingData,
                                               textColumn: "text",
                                               labelColumn: "label")

// 테스트 정확도 출력
let metrics = sentimentClassifier.evaluation(on: testData)
print("정확도: \\\\\\\\(1 - metrics.classificationError)")

// 모델 저장
try sentimentClassifier.write(to: URL(fileURLWithPath: "/Users/yourname/Desktop/SentimentClassifier.mlmodel"))

```

### 출력 결과

`.mlmodel` 파일이 생성됨 → CoreML에서 사용 가능

## 2. CoreML 예제 – 앱에서 모델 사용

> 이 코드는 .mlmodel을 Xcode 프로젝트에 추가한 뒤 Xcode가 자동 생성해주는 클래스(SentimentClassifier)를 사용하는 방식입니다.

```swift
import CoreML

// 모델 인스턴스 생성
guard let model = try? SentimentClassifier(configuration: MLModelConfiguration()) else {
    fatalError("모델 로드 실패")
}

// 사용자 입력
let inputText = "오늘 너무 행복해!"

// 예측 수행
guard let prediction = try? model.prediction(text: inputText) else {
    fatalError("예측 실패")
}

print("감정 예측 결과: \\\\\\\\(prediction.label)")

```

### 예상 출력

```
복사편집
감정 예측 결과: positive

```

## SwiftUI에서 CoreML을 사용하는 간단한 예시

```swift
import SwiftUI

struct ContentView: View {
    @State private var inputText = ""
    @State private var predictionResult = ""

    var body: some View {
        VStack(spacing: 20) {
            TextField("문장을 입력하세요", text: $inputText)
                .textFieldStyle(RoundedBorderTextFieldStyle())
                .padding()

            Button("감정 분석") {
                predictSentiment()
            }

            Text("예측 결과: \\\\\\\\(predictionResult)")
        }
        .padding()
    }

    func predictSentiment() {
        guard let model = try? SentimentClassifier(configuration: MLModelConfiguration()),
              let prediction = try? model.prediction(text: inputText) else {
            predictionResult = "예측 실패"
            return
        }

        predictionResult = prediction.label
    }
}

```