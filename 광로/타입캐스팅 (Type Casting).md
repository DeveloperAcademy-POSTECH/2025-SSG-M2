
- Swift로 코드를 짤 때 , 다양한 타입 간 변환이 필요한 순간이 있는데, 그때 사용하는 것!!!

-> 인스턴스의 타입을 확인하거나, 다른 타입으로 취급하고! 해당 인스턴스를 슈퍼 클래스나 하위 클래스로 취급하는 방법입니다.

- Swift에서 타입 캐스팅은  "is" , "as" 연산자로 구현되며,
  타입 캐스팅을 사용 하면 타입이 프로토콜에 적합한지도 확인이 가능함

(솔직히 is, as GPT가 써줘서 ...그냥 문득문득 코드만 본거 같음)

그럼 is, as가 뭐냐..

### is, as

``` swift
표현식 is Type
```

타입을 채크하는 연산자로, 런타임 시점에 실제 체크가 이루어 집니다.

1) 표현식이 type와 동일하거나, 표현식이 Type의 서브 클래스인 경우 -> true
2) 이 외의 경우엔 -> false

여기서 is 연산자는 타입을 '체크'하는 연산자로, 반환 값은 Bool 형임!!


``` swift
let char: Character = "A"

char is Character       // true
char is String          // false

let bool: Bool = true

bool is Bool            // true
bool is Character       // false
```

내가 원하는 타입인지 확인할 때 쓸수 있거나,
동일한 타입을 확인할 때도 쓸 수 있음!

위 예시에서는 표현식이 Type의 "서브 클래스"인 경우에도 true를 반환

이게 무슨 ...xxx 소리 인가 싶지만


```swift
class Human { }
class Teacher: Human { }

let teacher: Teacher = .init()
teacher is Teacher      // true
teacher is Human        // true
```

위 예시에서 Human 클래스를 Teacher란 클래스가 '상속'받을 경우,
teacher란 인스턴스는 Human 클래스의 서브 클래스 이기 때문에,
이런 경우엔 Human으로 타입 체크를 해도 true가 된단 말임

