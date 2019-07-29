# 6. 코틀린의 델리게이트

<br>

## 6.1. 위임 소개
- [Delegate Pattern](https://en.wikipedia.org/wiki/Delegation_pattern)
    - 한 객체의 기능 일부를 다른 객체로 넘겨 대신 수행하게 하는 것
    - 한 객체의 변경이 다른 객체에 미치는 영향을 줄일 수 있음
    - 코틀린은 언어 수준에서 delegate 기능을 지원하며, Java 와 같이 지원하지 않는 경우라도 원본 Object 를 델리게이트에 인자 / 파라미터인 메서드로 전달하는 식으로 사용 가능
    - 예시
        ```kotlin
        class Electronics {
            fun turnOn() = println("Turn it on")
        }

        class Refrigirator(val e: Electronics) {
            fun turnOn() = e.turnOn()
        }

        fun main() {
            val electronics = Electronics()
            electronics.turnOn()

            val refrigerator = Refrigirator(electronics)
            refrigerator.turnOn()
        }
        ```

<br>

### 6.1.1. 위임의 이해
- Delegate 패턴은 상속의 좀 더 나은 대안으로 입증됨
    - 상속의 제한사항
        - 자식 클래스가 자신의 부모 클래스를 프로그램 실행 중에 변경 불가
        - 부모 클래스에 변경이 발생하면 자식 클래스에 그 변경이 직접 전파
    - 위임은 비교적 유연
        - 런타임 시 Delegate 변경 가능
        - 한 Object 가 메서드 호출로 다른 Object 로 전달되고, Delegate 를 호출하는 여러 Object 의 합성으로 볼 수 있음

<br>

## 6.2. 코틀린의 Delegate
- 코틀린은 언어 차원에서 Delegate 를 지원
    1. 속성 위임
    2. 클래스 위임

<br>

## 6.3. 속성 위임 (표준 Delegate)
- 위임: 메서드 전달 / 전송 테크닉
    - 속성: Getter / Setter 호출을 Delegate로 전달
    - Delegate: 속성 자체를 대신해 이 호출을 처리

- 대표적인 표준 Delegate
    1. `Delegates.notNull` (함수), `lateinit` (예약어)
    2. Lazy 함수
    3. `Delegates.Observable` 함수
    4. `Delegates.vetoable` 함수

### 6.3.1. `Delegates.notNull` 함수와 `lateinit`
- 둘다 `var` 키워드에만 사용 가능함에 유의
- null 을 허용하지 않는 변수를 초기값 지정 없이 선언하고 싶을 때 사용
    ```kotlin
    var notNullStr: String by Delegates.notNull<String>() // Delegate 를 이용해 초기값 없이 선언

    fun main(args: Array<String>) {
        notNullStr = "사용 전 초기값 지정 필수! 안하면 IllegalStateException 발생!"
        println(notNullStr)
    }
    ```
- 또는 `lateinit` 키워드를 대신 사용할 수 있음
    ```kotlin
    lateinit var notNullStr: String

    fun main(args: Array<String>) {
        notNullStr = "사용 전 초기값 지정 필수! 안하면 IllegalStateException 발생!"
        println(notNullStr)
    }
    ```


<br>

### 6.3.2. Lazy 함수
- `val` 키워드에만 사용 가능함에 유의
- 선언 시 **변수를 초기화하는 방법까지 지정** -> 변수가 실제로 사용될 때까지 초기화 호출 지연
    ```kotlin
    val currentStatus: String by lazy {
        println("초기화 수행")
        "행복합니다"
    }

    fun main(args: Array<String>) {
        println("아직 초기화 안됨")
        println(currentStatus) // 호출되는 순간에 초기화 수행 (Lazy)
    }
    ```
- 메모리 절약의 이점이 있음


<br>

### 6.3.3. `Delegates.Observable` 을 사용해 속성 값의 변경을 감지
- 변수의 getter / setter 호출을 위임하는 기능
- 특정 변수의 값 변경이 이뤄지는 즉시 어떤 작업을 수행해야 하는 경우에 적합
    ```kotlin
    /**
    * param1: 초기값
    * param2: 값 변경 감지 시 실행될 람다
    *  lambda_param1: KProperty<out R> 인스턴스
    *  lambda_param2: 속성의 이전 값
    *  lambda_param3: 속성의 최신 값
    */
    var myVal: String by Delegates.observable("초기값") {
            property, oldValue, newValue -> "변경 감지시 수행될 로직"
    }
    ```
- 예시
    ```kotlin
    var myStatus: String by Delegates.observable("불행") {
            property, oldValue, newValue -> println("${property.name}: 예전엔 ${oldValue}했지만 이제는 ${newValue}합니다.")
    }

    fun main() {
        myStatus = "행복"
        myStatus = "더 행복"
    }
    ```

<br>

### 6.3.4. `Delegates.vetoable` 로 조건 만족 시 수행하게 하기
- `boolean` 조건 체크를 통해 `true` 면 값 변경 수행, `false` 면 무시되게 하는 표준 Delegate
    ```kotlin
    var myVal: String by Delegates.vetoable("초기값") {
            property, oldValue, newValue -> "조건 참 / 거짓 여부와 상관 없이 수행될 로직"
        /* boolean 조건식 -> 참이여야 변수 값이 수정됨 */
    }
    ```
- 예시
    ```kotlin
    var myStatus: String by Delegates.vetoable("불행") {
            property, oldValue, newValue -> println("지금 ${newValue}한가요?")
        "행복" == newValue
    }

    fun main(args: Array<String>) {
        myStatus = "행복"
        println("네. 저는 ${myStatus}합니다")
        myStatus = "불행"
        println("아니요. 저는 ${myStatus}합니다")
    }
    ```

<br>

## 6.4. Delegate Map
- 함수 / 클래스 생성자에서 여러 파라미터 대신 맵에 담아 하나의 파라미터로 전달할 수 있음
    ```kotlin
    data class OccApplicant(val delegate: Map<String, Any?>) {
        val name: String by delegate
        val currTeam: String by delegate
        val wishTeam: String by delegate
        val age: Int by delegate
    }

    fun main(args: Array<String>) {
        val info = mapOf(
            "name" to "읍읍읍",
            Pair("currTeam", "쇼핑"),
            Pair("wishTeam", "페이플랫폼")
        )

        val occApplicant = OccApplicant(info)
        println("이름: ${occApplicant.name}")
        println("현재 부서: ${occApplicant.currTeam}")
        println("희망 부서: ${occApplicant.wishTeam}")
        println("나이: ${occApplicant.age}") // Map 으로 인자 안넘긴 값을 접근 시도 -> NoSuchElementException
    }
    ```

## 6.5. 커스텀 Delegate
- var 속성에 대한 Delegate 를 만드려면 `ReadWriteProperty` 인터페이스를 구현해야 함
    - 이 인터페이스는 `getValue` 와 `setValue` 를 오버라이드 해야 함
- 홀수면 자동으로 다음 짝수를 변수에 할당하는 커스텀 Delegate 예제
    ```kotlin
    import kotlin.properties.ReadWriteProperty
    import kotlin.reflect.KProperty

    abstract class MakeEven(initVal: Int) : ReadWriteProperty<Any?, Int> {
        private var value: Int = initVal

        override fun getValue(thisRef: Any?, property: KProperty<*>) = value

        override fun setValue(thisRef: Any?, property: KProperty<*>, value: Int) {
            val oldValue = value
            val wasEven = if (value % 2 == 0) {
                this.value = value
                true
            } else {
                this.value = value + 1
                false
            }
            afterAssignmentCall(property, oldValue, value, wasEven)
        }

        /* 로깅용 */
        abstract fun afterAssignmentCall(property: KProperty<*>, oldValue: Int, newValue: Int, wasEven: Boolean)
    }

    inline fun makeEven(
        initVal: Int,
        crossinline onAssignment: (property: KProperty<*>, oldValue: Int, newValue: Int, wasEven: Boolean) -> Unit):
            ReadWriteProperty<Any?, Int> = object : MakeEven(initVal) {

        override fun afterAssignmentCall(property: KProperty<*>, oldValue: Int, newValue: Int, wasEven: Boolean)
                = onAssignment(property, oldValue, newValue, wasEven)
    }

    var myEven: Int by makeEven(0) {
        property, oldValue, newValue, wasEven -> println("${property.name} $oldValue -> $newValue, Even: $wasEven")
    }

    fun main(args: Array<String>) {
        myEven = 6
        println("myEven: $myEven")
        myEven = 3
        println("myEven: $myEven")
        myEven = 5
        println("myEven: $myEven")
        myEven = 8
        println("myEven: $myEven")
    }
    ```


<br>


## 6.6. Local Delegate
- 로컬 변수에도 Delegate 사용 가능
    ```kotlin
    fun useDelegate(areYouHappy: Boolean) {
        // Delegate 를 로컬 변수에도 사용 가능
        val yourEmotion by lazy {
            "HAPPY"
        }

        if (areYouHappy) {
            println("YES! You are $yourEmotion!")
        }

        println("END")
    }

    fun main() {
        useDelegate(true)
    }
    ```

<br>

## 6.7. Class Delegate
- 함수 / 속성 뿐만 아니라 클래스도 Delegation 이 가능함
    ```kotlin
    interface Developer {
        fun printStatus()
    }

    class DeveloperImpl(val status: String): Developer {
        override fun printStatus() {
            println(status)
        }
    }

    class HappyDeveloper(val developer: Developer): Developer by developer {
        override fun printStatus() {
            print("당신의 상태는 => ")
            developer.printStatus()
        }
    }

    fun main() {
        val developer = DeveloperImpl("행복")
        developer.printStatus()

        val happyDeveloper = HappyDeveloper(developer)
        happyDeveloper.printStatus()
    }
    ```
