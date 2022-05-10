# 커피숍 주문하기

## 👥 Person 만들기
1. 구조체를 선택한 이유
- 사람이라는 특성을 생각하여 선택했다. A와 B라는 사람이 일란성 쌍둥이거나, 도플갱어라고 가정했을 때, 둘의 특성과 하는 동작이 같아도 다른 사람이다. A가 C가 된다고 해서 B가 C가 되지 않는다.  
_즉, 속성이나 동작을 복사했을 뿐, 이후 변경사항이 생겼을 때 상호작용하지 않기 때문에 값타입으로 설정하였다._
2. pastime에 옵셔널을 사용한 이유
- ~~여가시간이 없을 수도 있고, 있어도 어떤 활동을 할지 못 정했을 수도 있으니까.~~
    - pastime을 옵셔널로 지정해주면, 이니셜라이저를 두번하여 옵셔널인 파라미터가 포함됐을 때와 안되어있을 때를 구분해야하는 것으로 잘못알고 있었다. 물론 이렇게 해도 되지만 Person타입에서 pastime을 빼고 이니셜라이징을 한 다음, 인스턴스를 변수로 선언하고,나중에 pastime을 호출하면 여가시간을 해당 인스턴스에 지정할 수 있다.사람의 여가활동은 언제든 바뀔 수 있다고 생각하기 때문에 후자의 방식으로 수정했다
- 그래서 `enjoyLeisureTime`함수에서 옵셔널을 제거하는 `if let`을 써주고 여가시간을 즐기는 버전과  
여가시간을 즐기지 않는 버전을 작성하였다.

## ☕️ 카페차리기
1. 클래스타입으로 선택한 이유
- 카페는 프랜차이즈를 낸다. 본사와 프랜차이즈 간의 상호작용도 존재한다. 때문에 참조타입으로 상속이 가능한 클래스 타입을 선택했다.
2. ~~바리스타는 카페의 필수요소이기 때문에 암시적 추출 옵셔널로 작성하였다.~~
    - 꼭 필요한 값은 암시적 추출 옵셔널로 지정해야하는 것으로 알고 있었는데, 
    내가 만든 카페는 처음부터 바리스타가 필요하므로 인스턴스 생성시 값을 넣어주는 것이 더 좋을 것 같아 수정했다.
3. 가격에 쉼표로 천단위 구분
```
        let numberFormatter = NumberFormatter()
        numberFormatter.numberStyle = .decimal
        let price = price
        let result = numberFormatter.string(for: price)!
        let count : String = "가격은 \(result)원입니다."
```
- `let price = price`해준 이유는 아래 매출 누적연산을 위한 코드`salesRevenue += price`는 `Int` 타입이기 때문이다.
- `NumberFormatter()`은 옵셔널처리를 해주기 때문에 `result`에서 옵셔널을 강제추출해준다.
- `sayTodaySalesRevenue()`에서도 똑같이 쉼표로 천단위를 구분하여 매출을 말하게한다.
4. `salesRevenue += price`은 계산하는 순간 매출로 잡히게 하기위함이다.
---
## 🪐NumberFormatter
### Declaration
`class NumberFormatter : Formatter`
### Overview
`NumberFormatter`의 인스턴스는 `NSNumber 객체`를 포함하는 셀의 원문 표현을 포맷하고 숫자 값의 원문 표현을 NSNumber 객체로 변환한다. 이 표현은 정수, 부동소수 및 복수를 포함하며, 부동소수 및 복수는 지정된 소수점 위치로 포맷할 수 있다. 

>  `NumberFormatter`는 `Formatter`의 하위클래스이다. 공식문서에서 `Formatter`을 보면 포맷터는 하위 분류를 위한 것이다. 사용자 지정 포맷터는 **입력을 제한하고 새로운 방식으로 데이터 표시를 향상**시킬 수 있다. 예를 들어 사용자가 입력한 일련 번호가 미리 정의된 형식을 준수하는지 확인하는 사용자 정의 포맷터를 사용할 수 있다.
>> `NSNumber`는 숫자를 표현하기 위해 사용하는 방식 중 하나. (Foundation 프레임워크의 NSNumber 클래스 인스턴스를 생성함. 이후 공부 필요)

### 정리
1. NumberFormatter로 처리된 값은 옵셔널이다. 
    - `strint(for:price)`이 옵셔널이기 때문이다. 
        - `func string(for obj: Any?) -> String?`
    - `Any?`에 들어오는 개체가 올바른지 판단하고 그렇지 않으면 `nil`을 반환한다.
    - 🤔 그럼 `string(for:price)`을 쓰지 않고 바로 `NumberFormatter`으로 값을 반환해줄 수 있지 않을까? 
        - 시도해봤는데 이상한 문자가 출력됐다. 아마 사용자가 식별가능하고 활용할 수 있게 문자열타입으로 변환시키는 것 같다. 
2. 숫자 값과 텍스트 표현 간에 변환하는 형식
3. `.decimal`을 사용하면 뒤의 소수점은 세자기까지만 표현가능하다.
