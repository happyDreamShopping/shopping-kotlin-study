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

---

<br>

### 중위 함수

하나의 파라미터만 가진 함수는 **infix로 표기할 수 있**으며, 중위 표기법과 함께 사용할 수 있다.

많은 infix을 연결해 내부 DSL을 만들 수 있다.

> DSL(Domain Specific Language)은 특정 도메인에 특화된 언어다.

```kotlin
infix fun Int.superOperation(i: Int) = this + i

object Belong

object Us

object Base {
    infix fun are(belong: Belong) = this
}

object All {
    infix fun your(base: Pair<Base, Us>) {
    }
}

fun main(args: Array<String>) {
    println(1 superOperation 2) //이와 같이 중위표기법 사용가능
    println(1.superOperation(2))

    All your (Base are Belong to Us)
    All your (Pair(Base are Belong, Us))
}
```

<br>

infix 확장 함수 <K,V>K.to(v:V) 와 같은 형태로도 쓸 수 있다.

```kotlin
/**
 * Creates a tuple of type [Pair] from this and [that].
 *
 * This can be useful for creating [Map] literals with less noise, for example:
 * @sample samples.collections.Maps.Instantiation.mapFromPairs
 */
public infix fun <A, B> A.to(that: B): Pair<A, B> = Pair(this, that)
```

<br>

### 연산자 오버로딩

연산자 오버로딩은 다형성의 한 가지 형태이다.

파라미터 타입에 따라 동작이 바뀐다. --> 숫자 값에서 더하기(+)는 합 연산이고, 문자열에서는 연결이다.

```kotlin
class Wolf(val name: String) {
    operator fun plus(wolf: Wolf) = Pack(mapOf(name to this, wolf.name to wolf))
}


val talbot = Wolf("Talbot")
val northPack: Pack = talbot + Wolf("Big Bertha") // talbot.plus(Wolf("Big Bertha"))
print(northPack.members)
---

{Talbot=Wolf@28a418fc, Big Bertha=Wolf@5305068a}
```

<br>

### 바이너리 연산자

Pack.plus 확장 함수는 Wolf 파라미터를 받고 새로운 Pack을 반환한다.

153 page, 155 page 참조

> x + y —> x.plus(y)
> x - y —> x.minus(y)
>
> ...

```kotlin
operator fun Pack.plus(wolf: Wolf) = Pack(this.members.toMutableMap() + (wolf.name to wolf))

val biggerPack = northPack + Wolf("Bad Wolf")

---

{Talbot=Wolf@8efb846, Big Bertha=Wolf@2a84aee7, Bad Wolf=Wolf@a09ee92}
```

<br>

### Invoke

invoke  연산자는 이름 없이 호출 될 수 있다.



```kotlin
class Wolf(val name: String) {
    operator fun plus(wolf: Wolf) = Pack(mapOf(name to this, wolf.name to wolf))

    operator fun invoke(action: WolfActions) = when (action) {
        WolfActions.SLEEP -> "$name is sleeping"
        WolfActions.WALK -> "$name is walking"
        WolfActions.BITE -> "$name is biting"
    }
}

val talbot = Wolf("Talbot")
println(talbot(WolfActions.SLEEP))

---

Talbot is sleeping
```

<br>

### 인덱싱된 접근

코틀린 데이터 구조는 get 연산자의 정의가 있다.

 ==> **x[y] == x. get(y) page 156**

```kotlin
//class Pack(val members: Map<String, Wolf>)
operator fun Pack.get(name:String) = members[name]!!

val badWolf = biggerPack["Bad Wolf"]
```

<br>

set 연산자도 마찬가지로 다음과 같다

 ==> **x[y] = z == x.set(y, z)**

<br>

### Unary 연산자

Unary 연산자에는 파라미터가 없으며, 디스패처에서 직접 작동한다.

```kotlin
operator fun Wolf.not() = "$name is angry!!!"

!talbot
```

> +x —> x.unaryPlus()
>
> -x —> x.unaryMinus()
>
> ...

<br>

### 인라인 함수

- 성능이 우선인 경우, 인라인으로 고차 함수를 표시 할 수 있다.

- 모든 할 수를 인라인을 사용하여 내부로 컨버팅되어지길 원치 않을 수 있다. 이런 경우 람다함수 중 사용하지 않을 함수에 noinline 키워드를 붙여줍니다.

http://blog.naver.com/PostView.nhn?blogId=yuyyulee&logNo=221389623237&categoryNo=22&parentCategoryNo=0&viewDate=&currentPage=1&postListTopCurrentPage=1&from=search

<br><br>




