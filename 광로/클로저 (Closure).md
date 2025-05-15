
1. **Named Closure**
2. Unnamed Closure

쉽게 말하면 클로저도 함수임...

``` swift
func doSomething() {

    print("Somaker")

}

// 이 평범해 보이는 함수는 Named Closure이고
이를 클로저라 부르지 않고 그냥 함수라고 부르는 거임
```

그럼 Unnamed Closure는 뭐냐?


``` swift
let closure = { print("Somaker") }

// 이게 익명 함수임! Unnamed Closure

다시 말해 우리가 말하는 클로저는 Unnamed Closure
라고 생각 하면 된다
```

그리고 클로저도 '함수'의 일부분이기 때문에
==1급 객체 함수== 특성을 가지고 있음
(이거 정리  다해 놨는데.. 날림
별거 아니고 ㅇㄴ아ㅓ나어ㅏㄴㅇ

함수를 
1. 변수에 담고
2. 함수의 인자로 전달 가느 하고
3. 함수의 변환값을 사용 가능 한 것임
)

이런 것들임!!

그렇다면 클로저의 가벼운 표현식에 대해서 알아보자
![[스크린샷 2025-05-15 오후 5.31.07.png]]

뭐야?? 함수가 왜 이럼?? 이라고 생각 할 수 있는데...

아카데미 테크 1짱 Mini 말에 의하면
클로저는 "그냥 쉽게 사용하려고 만든거임!"
이라고 함 그러니깐 가볍게 쉽게 사용할려고 만드는 거임

위 부분에서

Closure Head , Closure Body 부분이 나뉘는데

이걸 구분 지어 주는게 ==in== 임

그리고 1급객체 함수 이기 때문에... 

파라미터와 Return 값이 없더라도 아래와 같이 사용 가능

``` swift
let closure = { () -> () in

    print("Closure")

}
// 진짜 개편한듯
```

만약 이걸 일반 함수였으면... 

``` swift
func closureFunction() {
    print("Closure")
}
```

이렇게 표현됨

양쪽의 장 단이 있음!

1. 함수 (Func) : 이름이 있어 명확하고 재사용 가능
2. 클로저 (Closure) : 이름 없이 변수에 담거나 인자로 전달할 . 수있어서 유연하게 쓰임

자 그러면 ==클로저를 파라미터로 넘기는 방법==

``` swift
let closure = { () -> () in
    print("Closure")
}

func perform(action: () -> Void) {
    print("Before")
    action()
    print("After")
}

perform(action: closure)

// 이렇게 하면 실행 결과가 어떻게 될까?

Before
Closure
After

다음과 같이 나옴!! 왜그런줄 암??

처음 closure를 보면 Void 타입의 함수처럼 생긴 클로저 인데
파라미터는 없고 return 값만 받지?
근데 호출 하면 Closure를 출력함

밑에 함수는 알거고!

그럼 perform(action: closure)
이부분에서

closure라는 클로저를 perform 함수의 action 파라미터에 전달 하면

그럼 action() == closure가 되고
그 안에서 print("Closure") 가 실행 되는 구조임


```