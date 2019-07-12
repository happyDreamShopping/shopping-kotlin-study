# 4장 함수, 함수 타입, 부수효과

## 목차

1. 코틀린의 함수
2. 함수 타입
    - 람다
    - 고차 함수
    - 부수효과와 순수함수의 이해

### 코틀린의 함수
- 코틀린 함수 선언 방법
    - return이 void 타입이라면 생략가능.  
```kotlin
fun add(a:Int, b:Int): Int {
    val result = a+b
    return result
}
```

- 코틀린에서는 함수의 비즈니스로직이 한줄로 끝날 수 있다면 아래와 같이도 변경 가능.
    - 개인적으로 아래 형식을 허용하는 이유는 코드의 간결함도 있겠지만 보다 더 함수는 한가지 일만 하라는 것을 강조하는 의미같다.
```kotlin
fun add(a:Int, b:Int) = a+b
```

- 함수에서 2개, 3개의 값 반환.
    - 코틀린에서는 Pair, Triple을 사용하여 반환값을 2개, 3개가 가능하다.
    - 도메인 클래스와 같이 의미가 있지 않을때 사용하면 좋을거 같다.
```kotlin
fun getUser() = Pair(1, "리뷰")

fun getTriangle() = Pair(1, 2, 3)

fun main(args: - <String>) {
    val (userId, userName) = getUser()
    println("userId = $userId t userName = $userName")
    val (x, y, z) = getTriangle()
    println("x = $x , y = $y, z = $z")
}
```
- 확장 함수
    - 코틀린에서는 코드를 명시적으로 선언하기 위해 아래와 같은 함수 형식을 지원한다.
    - 개인적으로 함수에서 this를 사용하는 점이 재밌기도 하고 명시적이여서 좋은거 같다.
```kotlin
fun countWord(text: String)  = text.trim().split(" ").size

fun String.countWord2()  = this.trim().split(" ").size

fun main(args: - <String>) {
    println("geon yeong kim".countWord2())
    println(countWord("geon yeong kim"))
}
```

- 기본인수
    - 코틀린에서는 파이썬과 같이 함수의 파라미터의 기본값을 정의할 수 있다.
```kotlin
fun Int.isGreaterThan(number: Int = 0)  = this > number

fun main(args: - <String>) {
    5.isGreaterThan(6)
    5.isGreaterThan()
}
``` 

- 중첩함수
    - 코틀린에서는 nested 함수를 지원하며 이 함수는 부모 함수에서만 유지된다.
    - 개인적으로는 굳이... 이렇게까지 만들어야되나 싶다. 이 함수를 다른 함수에서도 사용할수 있으니 class 함수로 선언하는게 나중을 위해 좋지 않을까라는 생각이 든다.
```kotlin
fun main(args: Array<String>) {
    fun nested() = "중첩함수"
    println("${nested()}")
}
```

### 함수 타입

- 람다
    - 정의 = 이름없는 익명 함수.
    - 자바에서의 람다 예제

```java
 public class LambdaIntroClass {
    interface SomeInterface {
        void doSomeStuff();
    }
    private static void invokeSomeStuff(SomeInterface someInterface) {
        someInterface.doSomeStuff();
    }

    public static void main(String[] args) {
        invokeSomeStuff(() -> {
            System.out.println("doSomeStuff 호출");
        });
    }
}
```

   - 코틀린에서의 람다 예제
   - 사실상 이것은 인자로 함수를 전달받아 실행하는 것이다(자바 스크립트와 비슷)

```kotlin
fun invokeSomeStuff(doSomeStuff: () -> Unit) { // 인자, 반환값이 없는 함수.
    doSomeStuff()
}
fun main(args: Array<String>) {
    invokeSomeStuff { println("doSomeStuff") }
}
```

- 속성으로서의 함수
    - 함수를 속성으로 사용한다는 의미이다.(이도 자바스크립트와 비슷)
    - 코드로 보면 이해가 간다.
    - 코틀린은 단일 파라미터에 대해 기본 it로 접근가능하게 허용한다(직접 명시적으로 선언해도 된다.)
```kotlin
val printFunc : (Int) -> Unit = {println(it)}

println("${printFunc(123)}")
println("${printFunc(456)}")
```

- 고차함수
    - 다른 함수를 파라미터로 받거나 반환하는 함수를 일컫는다.
    - 함수를 인자로 받는 예제
```kotlin
fun plusOperation(plus: (Int) -> Unit, number: Int)  = op(number) // Int를 인자로 반환값이 없는 형태의 함수를 전달받아 수행하는 고차함수.

plusOperation ({number -> println(number +1) }, 3)
```
  - 함수를 반환하는 예제
```kotlin
fun getAnotherFunction(n: Int) : (String) -> Unit = {println("n: $n it:$it")}

getAnotherFunction(0)("geonyeong") // getAnotherFunction(0)의 반환값이 String 하나를 인자로 받는 함수 반환 후 바로 "geonyeong"인자로 수행.
```

- 부수효과와 순수효과
    - 부수효과(side effect) = 자신의 범위 외부의 값을 변경시키는것을 일컫는다.

    - 순수함수 = 파라미터에 완전히 의존하는 함수를 일컫는다. -> 그냥 부수효과를 일으키지 않으면 순수함수라고 생각하면 된다.

