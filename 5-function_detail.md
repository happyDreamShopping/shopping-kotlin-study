# 5장 함수 심화 학습

<br>

### 단일 표현 함수

기본적인 코틀린 함수 선언 방법

```kotlin
fun sum(a:Int, b:Int): Int {
    val result = a+b
    return result
}
```

<br>

아래와 같이 단일 표현으로 쓸 수 있다.

~~~kotlin
fun sum(a:Int, b:Int): Int = a + b

fun sum(a:Int, b:Int) = a + b  //타입 유추 사용으로 더 줄일수도 있다.
~~~

<br>

### 수정자 vararg

가변인자, JAVA 에서는 '...'로 표현

```kotlin
public static final void aVarargFun(String... names) {
  
---
  
fun aVarargFun(vararg names: String) {
  names.forEach(::println)
}

fun main(args: Array<String>) {
  aVarargFun()
  aVarargFun("행복", "하세요", "여러분")
}
```

<br>

- **vararg로 표시된 파라미터가 있는 함수는 0개 이상의 값으로 호출**할 수 있다.
- 함수는 **여러 개의 vararg 파라미터를 가질 수 없다.** 컴파일 에러남

<br>

### 람다

함수의 마지막 파라미터가 람다인 경우, **괄호 바깥과 중괄호 내부로 전달**될 수 있다.

```kotlin
fun unless(condition: Boolean, block: () -> Unit) {
    if (!condition) block()
}

fun main(args: Array<String>) {
    val securityCheck = false // some interesting code here

    unless(securityCheck) {
        println("You can't access this website")
    }
}
```

<br>

vararg 와 같이 쓰면? ==> 아래처럼 람다는 함수의 끝에 갈 수 있다.

```kotlin
fun <T, R> transform(vararg ts: T, f: (T) -> R): List<R> = ts.map(f)

transform(1, 2, 3, 4) { i -> i.toString() }
```

<br>

아래처럼 vararg 붙여서 람다를 여러 개 전달할 수도 있다. vararg로 보내는 경우, 괄호 안에서만 보내야함

```kotlin
fun <T> emit(t: T, vararg listeners: (T) -> Unit) = listeners.forEach { 
  listener -> listener(t)
}

//emit(1){i -> println(i)} // Compilation error
emit(1, ::println, { i -> println(i * 2) })
```

<br>

### 명명된 파라미터

너무 많은 파라미터를 갖는 함수는 사용하기 어렵다. 가독성 ㅠ

<br>

```kotlin
typealias Kg = Double
typealias cm = Int

data class Customer(val firstName: String,
               val middleName: String,
               val lastName: String,
               val passportNumber: String,
               val weight: Kg,
               val height: cm)
```

<br>

일반적인 호출

```kotlin
val customer1 = Customer("John", "Carl", "Doe", "XX234", 82.3, 180)
```

<br>

명명된 파라미터를 포함한 호출, 가독성 굿

```kotlin
val customer2 = Customer(
      lastName = "Doe",
      firstName = "John",
      middleName = "Carl",
      height = 180,
      weight = 82.3,
      passportNumber = "XX234")
```

<br>

vararg 결합해 사용할 수도 있다.

```kotlin

fun paramAfterVararg(courseId: Int, vararg students: String, roomTemperature: Double) {

}

paramAfterVararg(68, "Abel", "Barbara", "Carl", "Diane", roomTemperature = 18.0)
```

<br>

### 고차 함수의 명명된 파라미터

일반적으로 람다에 대한 파라미터의 이름을 짓지 않는다.

```kotlin
fun high(f: (Int, String) -> Unit) {
   f(1, "Romeo")
}

high{ q, w ->
		//do something
}
```

<br>

아래와 같이 age와 name이라는 파라미터를 가지게 할 수 있다.

~~~kotlin
fun high(f: (age:Int, name:String) -> Unit) {
   f(1, "Romeo")
}

//근데 이렇게 쓰면 컴파일 에러남
f(age=1, name="Romeo")
~~~

<br>

### 기본 파라미터

아래처럼 favouriteLanguage, yearsOfExperience 데이터 클래스는 기본 값을 가진다.

```kotlin
data class Programmer(val firstName: String,
                 val lastName: String,
                 val favouriteLanguage: String = "Kotlin",
                 val yearsOfExperience: Int = 0)
```

<br>

```kotlin
val programmer1 = Programmer("John", "Doe")

// val programmer2 = Programmer("John", "Doe", 12) //Error

//yearsOfExperience 를 전달하고 싶으면 명명된 파라미터로 전달
val programmer2 = Programmer("John", "Doe", yearsOfExperience = 12) //OK

//명명된 파라미터 사용하지 않으려면 올바른 순서로 전달
val programmer3 = Programmer("John", "Doe", "TypeScript", 1)
```

<br>

### 확장 함수

```kotlin
fun String.sendToConsole() = println(this)

fun main(args: Array<String>) {
	"Hello world! (from an extension function)".sendToConsole()
}
```

<br>

적절한 멤버 함수가 인스턴스 내부의 모든 것에 접근할 수 있는 것과는 달리 **확장 함수는 this의 private 멤버에 접근할 수 없다.**

```kotlin
class Human(private val name: String)

fun Human.speak(): String = "${this.name} makes a noise" //Cannot access 'name': it is private in 'Human'
```

<br>

### 확장 함수와 상속

일반적인 상속 예

```kotlin
open class Canine {
   open fun speak() = "<generic canine noise>"
}

class Dog : Canine() {
   override fun speak() = "woof!!"
}

fun printSpeak(canine: Canine) {
   println(canine.speak())
}

printSpeak(Canine())
printSpeak(Dog())

-------

<generic canine noise>
woof!!
```

<br>

확장 함수로 하면..

```kotlin
open class Feline

fun Feline.speak() = "<generic feline noise>"

class Cat : Feline()

fun Cat.speak() = "meow!!"

fun printSpeak(feline: Feline) {
   println(feline.speak())
}

printSpeak(Feline())
printSpeak(Cat())

--------

<generic feline noise>
<generic feline noise>
```

Feline의 speak() 를 두번 호출한 셈이다.

<br>

```kotlin
open class Primate(val name: String)

fun Primate.speak() = "$name: <generic primate noise>"

open class GiantApe(name: String) : Primate(name)

fun GiantApe.speak() = "${this.name} :<scary 100db roar>"

fun printSpeak(primate: Primate) {
  println(primate.speak())
}

printSpeak(Primate("aaa"))
printSpeak(GiantApe("bbb"))
```

<br>

### 멤버로서의 확장 함수

```kotlin
open class Caregiver(val name: String) {
  open fun Feline.react() = "PURRR!!!"
  private fun Primate.react() = "*$name plays with ${this@Caregiver.name}*"

  fun takeCare(feline: Feline) {
    println("Feline reacts: ${feline.react()}")
  }

  fun takeCare(primate: Primate) {
    println("Primate reacts: ${primate.react()}")
  }
}

val adam = Caregiver("Adam")
val fulgencio = Cat()
val koko = Primate("Koko")

adam.takeCare(fulgencio)
adam.takeCare(koko)

----

Feline reacts: PURRR!!!
Primate reacts: *Koko plays with Adam*

------

Feline reacts: PURRR!!!
Primate reacts: *Koko plays with Koko*
```

name 변수 충돌이 있는 멤버에게 접근하려면 확장 리시버(Primate)가 우선권을 가진다.

디스패처 리시버(Caregiver)의 멤버에 접근하려면 제한된 this 구문을 사용해야 한다.

<br>

this의 다양한 뜻을 혼동하지 말자

```kotlin
class Dispatcher {
   val dispatcher: Dispatcher = this
   fun String.extension() {
      val receiver: String = this
      val dispatcher: Dispatcher = this@Dispatcher
   }
}
```

<br>

더 나아가..

```kotlin
open class Vet(name: String) : Caregiver(name) {
    override fun Feline.react() = "*runs away from $name*"
}

val brenda = Vet("Brenda")

listOf(adam, brenda).forEach { caregiver ->
                              println("${caregiver.javaClass.simpleName} ${caregiver.name}")
                              caregiver.takeCare(fulgencio)
                              caregiver.takeCare(koko)
                             }

------


main$Caregiver Adam
Feline reacts: PURRR!!!
Primate reacts: *Koko plays with Adam*
main$Vet Brenda
Feline reacts: *runs away from Brenda*
Primate reacts: *Koko plays with Brenda*
```

<br>

### 충돌하는 이름을 가진 확장 함수

<br>

확장 함수가 멤버 변수와 같은 이름을 가진 경우?

```kotlin
class Worker {
   fun work() = "*working hard*"
   private fun rest() = "*resting*"
}

fun Worker.work() = "*not working so hard*"

fun <T> Worker.work(t:T) = "*working on $t*"

fun Worker.rest() = "*playing video games*"

val worker = Worker()

	println(worker.work())

	println(worker.work("refactoring"))

	println(worker.rest())



---


*working hard*
*working on refactoring*
*playing video games*
```

<br>

멤버 함수와 같은이름 가진 함수를 서명하는 것은 문제가 되지 않지만 **멤버 함수가 언제나 우선권을 가지므로 확장 함수는 호출되지 않는다. 이 기능은 멤버 함수가 private인 경우에 변경된다.**

<br><br>
