1. [ JVM Thread. ](#1)
2. [ Coroutine. ](#2)

<a name="1"></a>
## 1. Thread
- Thread는 동시성의 가장 기본이 되는 단위로 비동기 동시성을 제공하기 위한 빌딩 블록이다. 
- Thread는 상호배제(mutual exclusion) 원칙에 따라 특정 데이터에 접근은 오직 하나의 Thread만 가능하다
- OS의 Preemping Scheduler 에 의해 Thread를 결정하고 작업을 수행함으로써 동시성을 보장한다.
  - Thread 생성/삭제
  - Context Switching
```
import java.util.concurrent.Executor
import java.util.concurrent.TimeUnit

fun main(args: Array<String>){
  val executor = Executors.newFixedThreadPool(1024)
  repeat(10000){
    executor.submit {
      Thread.sleep(1000)
      print('.')
    }
  }
  executor.shutdown()
}
```
- FixedThreadPool(1024)을 사용하여 10,000 task를 실행한다.


<a name="2"></a>
## 2. Coroutine
- Coroutine은 Thread와 동일하게 Concurrency를 제공하며, 하나 이상의 진입 지점을 갖는 Subroutine이다.
  - Subroutine
    - 하나의 진입 지점을 갖는 실행 가능한 코드 블럭으로, 언어에 따라 precedure, function, routine, method, subprogram 등으로 불린다.
      - return a computed value to its caller
      - have side effects
      - call itself recursively
- Stackful coroutines(=Fibers) 은 별도의 stack을 갖고 couroutine 객체는 JVM Heap에 할당된다.
- 사용자 스케쥴러에 의해 life-time을 갖는다.
- 여러 Thread에서 하나의 coroutine이 실행될 수 있다.
- Thread와 달리 coroutine 끼리는 선점형(preemptive)이 아니라 협력형(cooperate) 멀티태스킹 방식을 사용한다.
- 동일한 Thread에서 동시에 실행될 수 없다.
- OS가 아닌 User에 의해 context switching이 발생한다. 물론 OS에 의해 fiber가 동작 중인 thread가 해제될 순 있다. 
- fiber에서 수행되는 I/O 작업의 경우 만드시 비동기로 해야 다른 fiber가 blocking 되지 않는다. 

- ![Alt text](./resources/chapter-7-coroutine_thread.PNG)

    
## 2-1. Deep dive Coroutine
#### Stackful coroutines

#### Stackful coroutines flow
