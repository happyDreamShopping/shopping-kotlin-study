> 자바 API와 거의 비슷하지만 확장 함수와 조금의 추가가 있음

## 1. 스트림 소개

- 자바는 8 이후부터 스트림을 쓸 수 있지만 코틀린에서는 JDK6에서도 스트림 사용이 가능
```kotlin
fun main(args: Array<String>) {
    val stream = 1.rangeTo(10).asSequence().asStream()
    val resultantList = stream.skip(5).collect(Collectors.toList())
    println(resultantList)
}
```
- 5개의 아이템을 건너뜀 → List 인스턴스로 다시 수집
- IntRange 값을 생성 → 시퀀스 값을 생성 → 스트림 값을 얻음

## 2. 컬렉션과 스트림

### 컬렉션

1. 데이터 그룹을 저장하고 작업할 수 있게 하는 데이터 구조
2. 데이터 구조는 유한의 크기 제한이 있어야 함
3. 컬렉션 요소에 접근하는 동안 컬렉션을 재생성할 필요 없음
4. Collection API는 항상 사용될 준비가 된 채로 오브젝트를 구성
5. 다른 종류의 데이터 구조에서 데이터를 저장하는 데 사용됨

### 스트림

1. 데이터 구조가 아니며 아무것도 저장하지 않음, 파이프라인이나 IO 채널처럼 동작
2. 데이터 구조가 아니기 때문에 크기 제한이 필요하지 않음
3. 스트림의 요소는 스트림의 수명 동안 한 번만 만날 수 있
4. 필요에 따라 느긋한 방식으로 오브젝트를 생성
5. 큰 오브젝트 세트의 데이 계산에 사용

## 3. 스트림 작업

```kotlin
fun main(args: Array<String>) {
    val stream = 1.rangeTo(10).asSequence().asStream()
    val resultantList = stream.filter {
        it%2==0
    }.collect(Collectors.toList())
    println(resultantList)
}
```

- `filter()` Collection.map과 같은 방식으로 동작,지정된 것과 일치하는 스트림 요소로 구성된 스트림 값을 반환
- `map()` 스트림 각 요소에 지정된 함수를 적용한 결과로 구성된 스트림 값을 반환
- `mapToInt()` `mapToLong()` `mapToDouble()` 스트림 값을 반환 (각각 IntStream, LongStream, DoubleStream을 반환)
- `flatMap()` Collection.flatMap과 같은 방식으로 동작
- `flatMapToInt()` `flatMapToLong()` `flatMapToDouble()` flatMap과 같은 방식으로 동작하며 스트림 값을 반환 (각각 IntStream, LongStream, DoubleStream 값을 반환)
- `distinct()` Collection.distinct와 같은 방식으로 동작, distinct 요소의 스트림 값을 반환
- `peek()` 스트림의 요소로 구성된 스트림 값을 반환하고 추가적으로 제공되는 액션을 각 요소에 추가적으로 수행
- `anyMatch()` Collection.any와 비슷하게 스트림의 요소가 제공된 조건과 일치하는지의 여부를 반환
- `allMatch()` Collection.all과 비슷하게 이 스트림의 모든 요소가 제공된 수식과 일치하는지의 여부를 반환
- `noneMatch()` Collection.none과 비슷하게 스트림의 모든 요소가 제공된 수식과 일치하는지 여부를 반환

## 4. 프리미티브 스트림

- 프리미티브 스트림은 기본 데이터 타입에 일부 추가된 기능이 있으며 일반 스트림과 비슷하게 동작

```kotlin
fun main(args: Array<String>) {
    val intStream = IntStream.range(1,10)
    val result = intStream.sum()
    println("요소의 합계는 $result 이다")
}
```

- `IntStream.range()` 함수로 IntStream 값을 생성
    - range 함수는 두 개의 정수를 시작과 끝으로 사용하고, 둘 다 포함하는 정수에서 시작하는 스트림을 생성
- IntStream이 없으면 합을 계산하기 위해 모든 요소를 거치면서 루프를 돌아야 함

```kotlin
fun main(args: Array<String>) {
    val doubleStream = DoubleStream.iterate(1.5,{item -> item*1.3}) //(1)
    val avg = doubleStream
        .limit(10) //(2)
        .peek {
            println("아이템 $it")
        }.average() //(3)
    println("10개 아이템의 평균 $avg")
}
```

1. 팩토리 메소드 iterate()를 사용해 DoubleStream 값을 생성
2. 스트림의 요소에서 10개만 얻기 위해 limit 연산자를 사용
3. 평균을 계산

## 5. 스트림 팩토리 메소드

### 스트림 빌더

```kotlin
fun main(args: Array<String>) {
    val stream = Stream.builder<String>()
        .add("아이템 1")
        .add("아이템 2")
        .add("아이템 3")
        .add("아이템 4")
        .add("아이템 5")
        .add("아이템 6")
        .add("아이템 7")
        .add("아이템 8")
        .add("아이템 9")
        .add("아이템 10")
        .build()
    println("스트림 ${stream.collect(Collectors.toList())}")
}
```
```
스트림 [아이템 1, 아이템 2, 아이템 3, 아이템 4, 아이템 5, 아이템 6, 아이템 7, 아이템 8, 아이템 9, 아이템 10]
```

- Stream.builder() 메소드는 Streams.Builder의 인스턴스를 반환
- Bulder.add() 함수는 생성될 스트림 값에 대한 아이템을 받고 동일한 Stream.Builder의 인스턴스를 반환
- build 함수는 빌더에 저공된 아이템이 있는 스트림 인스턴스를 생성

### 빈 스트림 생성: Stream.empty()

```kotlin
fun main(args: Array<String>) {
    val emptyStream = Stream.empty<String>()
    val item = emptyStream.findAny()
    println("아이템 $item")
}
```
```
아이템 Optional.empty
```

- Stream.empty()를 통해 emptyStream을 생성
- 스트림에서 랜덤하게 선택된 모든 요소를 담기 위해 findAny() 함수를 사용
    - 무작위로 선택된 아이템이 있는 Optional  값을 반환 (비었으면 빈 Optional을 반환)

### 요소를 전달해 스트림 만들기: Stream.of()

```kotlin
fun main(args: Array<String>) {
    val stream = Stream.of("아이템 1",2,"아이템 3",4,5.0,"아이템 6")
    println("Items in Stream = ${stream.collect(Collectors.toList())}")
}
```
```
Items in Stream = [아이템 1, 2, 아이템 3, 4, 5.0, 아이템 6]
```

- of 함수에 요소를 전달해 스트림 인스턴스를 얻음

### 스트림 생성: Stream.generate()

```kotlin
fun main(args: Array<String>) {
    val stream = Stream.generate {
        (1..20).random()
    }
    val resultantList = stream
        .limit(10)
        .collect(Collectors.toList())
    println("resultantList = $resultantList")
}
```
```
resultantList = [10, 2, 10, 9, 8, 17, 14, 2, 7, 13]
```

- 파라미터로 람다/supplier 인스턴스를 받고 아이템이 요청될 때마다 아이템을 생성하기 위해 사용
- 무한 스트림도 생성 가능

## 6. 컬렉터와 Stream.collect: 스트림 수집

```kotlin
import java.util.stream.Collectors
```

- 스트림에서 데이터 구조로 요소를 리패키징 해야 하는 경우 사전에 정의된 컬렉터 구현을 사용

### Collectors.toList(), Collectors.toSet(), Collectors.toCollection()

- `Collectors.toList()` 스트림의 요소를 리스트로 수집
- `Collectors.toSet()` 스트림의 요소를 세트로 수집
- `Collectors.toCollection()` toList와 toSet의 보완 버전. 리스트를 누적하는 커스텀 컬렉션을 제공할 수 있음

```kotlin
fun main(args: Array<String>) {
    val resultantSet = (0..10).asSequence().asStream()
        .collect(Collectors.toCollection{LinkedHashSet<Int>()})
    println("resultantSet $resultantSet")
}
```
```
resultantSet [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

### 맵에 수집: Collectors.toMap()

- `Collectors.toMap()` 스트림을 맵 구현으로 리패키징, 많은 커스터마이징을 제공
    - 가장 간단하게는 두 개의 람다를 허용 (첫번째는 맵 엔트리의 키를 결정, 두번째는 맵 엔트리의 값을 결정)
    - 두 개의 람다는 스트림의 각 요소를 개별적인 반복으로 가져오고 이를 기반으로 키와 값을 생성

```kotlin
fun main(args: Array<String>) {
    val resultantMap = (0..10).asSequence().asStream()
        .collect(Collectors.toMap<Int,Int,Int>({
            it
        },{
            it*it
        })
    println("resultantMap = $resultantMap")
}
```
```
resultantMap = {0=0, 1=1, 2=4, 3=9, 4=16, 5=25, 6=36, 7=49, 8=64, 9=81, 10=100}
```

- 첫번째는 전달된 것과 동일한 값을 반환할 엔트리를 위한 키를 결정, 두 번째는 전달된 값의 제곱을 계산하고 반환
- 두 개의 람다 모두 동일한 파라미터를 가짐

### 문자열 스트림의 결합: Collectors.joining()

- `Collectors.joining()` 문자열이 포함된 스트림의 요소를 결합함
    - delimiter, prefix, postfix의 세 가지 선택적인 파라미터가 존재

```kotlin
fun main(args: Array<String>) {
    val resultantStreing = Stream.builder<String>()
        .add("아이템 1")
        .add("아이템 2")
        .add("아이템 3")
        .add("아이템 4")
        .add("아이템 5")
        .add("아이템 6")
				.build()
				.collect(Collectors.joining(" - ","여기서 시작함=>","<=여기서 끝남"))
    println("resultantString $resultantString")
}
```
```
resultantString 여기서 시작함=>아이템 1 - 아이템 2 - 아이템 3 - 아이템 4 - 아이템 5 - 아이템 6<=여기서 끝남
```

### 스트림 요소 그룹화: Collectors.groupingBy()

- `Collectors.groupingBy()` 스트림의 요소를 그룹화하는 동안 Map 함수로 수집할 수 있음
    - 이 함수와 Collectors.toMap의 차이점은 이 함수는 `Map<K,List<T>>` 함수를 만들게 함
    - 각 그룹의 값을 List 값으로 보유하는 Map 함수를 생성할 수 있음

```kotlin
fun main(args: Array<String>) [
    val resultantSet = (1..20).asSequence().asStream()
        .collect(Collectors.groupingBy<Int,Int> { it % 5 })
    println("resultantSet $resultantSet")
}
```
```
resultantSet {0=[5, 10, 15, 20], 1=[1, 6, 11, 16], 2=[2, 7, 12, 17], 3=[3, 8, 13, 18], 4=[4, 9, 14, 19]}
```
