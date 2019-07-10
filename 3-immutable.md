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




