# Volatile
## Volatile이 필요할 때 = 메모리 가시성이 필요할 때

<img src="https://github.com/user-attachments/assets/f769aa76-286c-4c9b-b5a2-28a884d8927e" width=900>

멀티 스레드 환경에서 runFlag = false로 값을 변경하면, 해당 변경 사항이 while(runFlag == true) 작업을 실행하고 있는 다른 스레드에서 언제 보일까?


위 그림과 같이 멀티 스레드 환경에서 한 스레드가 변경한 값이 다른 스레드에서 언제 보이는지에 대한 문제를 **메모리 가시성
(memory visibility)** 이라 한다. 이름 그대로 메모리에 변경한 값이 보이는가, 보이지 않는가의 문제이다.

+ 캐시 메모리를 사용하면 CPU 처리 성능을 개선할 수 있으나, 때로는 성능 향상보다 여러 스레드에서 같은 시점에 정확히 같은 데이터를 보는 것이 더 중요할 수 있다. (멀티 스레드 환경에서 발생할 수 있는 상태 불일치 문제)
+ 이를 위해 성능을 약간 포기하고 값을 읽거나 쓸 때 메인 메모리에 직접 접근하여 해결할 수 있다.
+ 한 스레드에서 변경한 값이 다른 스레드에서 즉시 보일 수 있도록 volatile 키워드를 사용할 수 있다.
+ volatile은 메모리 가시성은 보장하나, 연산의 원자성을 보장하지는 못한다.
+ 원자성이 보장되어야 하는 연산일 경우 `synchronized` or `Lock`을 사용하여 보완할 수 있다. 


# AQS (Abstract Queued Synchronizer)
## AQS 내부 Volatile의 사용
`Volatile int state` 를 사용한 다양한 상태 관리를 할 수 있다.


즉, 멀티 스레드 환경에서 동시에 접근하려고 할 때, 항상 같은 값이 유지되어야 하는 상태를 표현할 때 사용한다. 


## Semaphore
- Semaphore는 자원의 개수를 관리하는 동기화 도구로, state는 세마포어의 남은 허용 가능한 횟수를 나타낸다.
- state의 값
   - state 변수는 세마포어가 허용할 수 있는 "남은 횟수"를 추적한다. 예를 들어, 세마포어가 3개의 스레드만 동시에 접근할 수 있도록 제한한다고 하면, state는 3으로 시작한다.
- 변경 방식: acquire() 메서드가 호출되면 state 값이 감소하고, release()가 호출되면 state 값이 증가한다. 이때 state가 0이 되면 더 이상 자원이 없다고 보고, 세마포어는 대기 중인 스레드를 큐에 대기시킨다.


## ReentrantLock
- ReentrantLcok은 동일한 스레드가 여러 번 락을 획득할 수 있도록 허용하는 락이다. state 변수는 락의 상태와 재진입 횟수를 나타낸다.
- state의 값
   - state는 락의 상태와 획득 횟수를 추적하는 데 사용된다. state 값의 하위 비트는 락의 소유 여부를 나타내고, 상위 비트는 락을 획득한 스레드의 횟수를 나타낸다.
   - 예를 들어, state가 0이면 락이 풀린 상태이고, 1 이상이면 해당 스레드가 락을 획득한 횟수를 나타낸다.
- 변경 방식: lock() 메서드가 호출되면 state 값이 증가하고, unlock() 메서드가 호출되면 state 값이 감소한다. 만약 현재 스레드가 이미 락을 획득한 상태에서 lock()을 호출하면, state 값은 증가하지만 다른 스레드는 이 락을 획득할 수 없게 된다.


## CountDownLatch
- CountDownLatch은 특정 수의 작업이 완료될 때까지 대기하는 동기화 도구이다. state는 남은 카운트를 추적하는 데 사용된다.
- state의 값
   - state 변수는 카운트다운을 통해 남은 카운트를 추적한다. 초기값은 설정한 카운트 값으로 시작하며, countDown() 메서드가 호출될 때마다 state 값이 감소한다. state 값이 0에 도달하면 대기 중인 스레드는 실행을 계속할 수 있다.
- 변경 방식: countDown() 메서드가 호출되면 state 값이 감소하며, await() 메서드는 state 값이 0이 될 때까지 대기한다.


#### (+ 참고) 그 외, ReentrantReadWriteLock, CyclicBarrier, Exchanger 가 존재한다.
+ ReentrantReadWriteLock: 읽기-쓰기 락으로, 여러 스레드가 읽기를 동시에 할 수 있으나 쓰기는 한 번에 하나의 스레드만 할 수 있도록 제한
+ CyclicBarrier: 여러 스레드가 특정 지점에서 만나서 동기화될 수 있도록 한다. 카운트다운을 사용하여 스레드들이 모두 준비될 때까지 기다리게 만든다.
+ Exchanger: 두 스레드가 데이터를 교환할 수 있도록 하는 동기화 도구



***


ref.
- [대 영 한의 실전 자바 강의 ^.^](https://www.inflearn.com/course/%EA%B9%80%EC%98%81%ED%95%9C%EC%9D%98-%EC%8B%A4%EC%A0%84-%EC%9E%90%EB%B0%94-%EA%B3%A0%EA%B8%89-1)
- [Java in MultiThreading w.JUC](https://nicklee1006.github.io/Java-Multithreading-14-Lock/)

