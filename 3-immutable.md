## 불변성이란 무엇인가

> 내옹이 바뀌지 않아도 되는 컬렉션을 사용하고 있다면, 명시적으로 `immutable` 한 컬렉션 클래스를 사용합시다. *-경일님의 코드리뷰 중*

### Java에서의 불변성

- 수많은 불변성 컬렉션들이 있다.
- 흔히 말하는 `Value Type` 이란 것도 많다. 

```java
...
List<String> studyMembers = Collections.unmodifiableList(Arrays.aslist("김슬아", "민세원", "이정혁", "이성준"));

//이렇게 선언된 리스트엔 요소를 추가할 수 없다. 
try {
    studyMembers.add("손흥민");
} catch(UnsupportedOperationException e) {
    log.error("정원 초과입니다." , e);
}
```

*간단한 밸류 타입 예시 - `Point`*

```java
public class Point {

    //final 키워드를 통해 이후 재할당을 막아야 한다.
    private final int x;
    private final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public int getX() {
        return this.x;
    }

    public int getY() {
        return this.y;
    }
}
```

### Kotlin 에서의 불변성

- 개념은 같다. 
- 자바보다 문법이 개선되었다. 

```kotlin 
...

//타입 추론에 의해 studyMembers의 타입이 List<String>으로 추론된다.
//listOf 정적 메소드는 불변성을 갖는 리스트를 돌려준다. 
val studyMembers = listOf("김슬아", "민세원", "이정혁", "이성준")

//Runtime exception이 아닌, 컴파일 에러가 발생한다. 
studyMembers.add("김준현")
```

**참조 불변성** 

Java 개발자에게도 익숙한 개념이다. 

참조 불변성 침해까지 컴파일 타임에 잡아내는 건 불가능하다. 

```java
public class NaverMan {

    private final TodayLunch lunchMenu;
    private final String name;
    private final int jjamba;

    (typical all-args constructor, getter, setter follows....)
}

public class TodayLunch {
    private String menuName;

    {get, set}
}

...

NaverMan man = new NaverMan(new TodayLunch("쏘세지야채볶음"), "정휘준", 1);

man.getLunchMenu.setMenuName("조기튀김");

//stdout: "조기튀김"
System.out.println(man.getLunchMenu.getMenuName());
```

```kotlin
val naverMans = listOf(
        NaverMan(TodayLunch("참치타다끼"), "이성준", Int.MAX_VALUE), 
        NaverMan(TodayLunch("짜게치"), "정휘준", Int.MIN_VALUE),
        NaverMan(TodayLunch("지파이버거"), "이정혁", 5))

        naverMans[1].lunchMenu.menuName = "닭가슴살샐러드"

        ...
```
- 참조하고 있는 객체의 값이 뒤바뀔 경우, 불변성은 깨질 수 밖에 없다. 

**불변 값**

- 값을 변경하지 않도록 강제한다. 

```kotlin
const val GOD_NAME = "이성준"
```

**주의!**
- Kotlin의 `const val` 키워드는 원시 타입과 문자열에만 적용할 수 있다. 

```kotlin
//이거 안됨
const val naverMan: NaverMan = NaverMan(...)
```

**`const val` 사용 경험 공유**

때는 약 10개월 전 인턴 마치고 모 교육 스타트업에서 서비스를 개발하고 있을 때였다. 

CTO님이 이미 코드를 거의 다 코틀린으로 바꿔놓은 시점....


```kotlin
const val TESTABLE_ENDPOINT = "https://dev.****...."
const val TEST_USER_NAME = "pythonjigi"
const val TEST_USER_EMAIL = "pythonjigi@kakaocorp.com"

@SpringBootTest
open class BaseIntegrationTest {
    (...)
}
```

- 테스트 코드에서 값(property)에 기반해 객체의 작동을 검증할 경우
    - 필연적으로 값을 명시해줄 때 중복이 발생한다. 
    - 다양한 테스트 케이스에서 같은 값을 반복적으로 사용해야 한다. 
- Java의 경우, 이 문제를 깔끔하게 해결하기 어렵다. 
    - 값을 둘 곳이 마땅히 없음. 
    - 전역적으로 접근 가능한 변수를 담아둘 그릇이 잘 없다.
- Kotlin의 경우, 위의 케이스처럼 **클래스 바깥에 변수를 선언하는 것이 가능하다.**
    - 파일에 클래스 선언이 없어도 상관없다. 
    - 한 파일을 잡아놓고, `const val` 만 주르륵 선언해놓는 것도 좋은 방법이다. 

이렇게 선언한 변수는, 전역적으로 접근 가능하다. 

```kotlin
val god = GOD_NAME

//stdout: "이성준"
println(god)
```

*사용해보니*

- 각 테스트 suite에 난잡하게 흩어져있던 값 선언부들을 정리할 수 있었다. 
    - 결과적으로 코드양을 획기적으로 줄였다. 
    - 값을 할당할 때 발생하는 **human error**를 조기에 잡아낼 수 있다. 
- 값을 꼭 **클래스에 담지 않아도 된다**
- 값은 값이다 






