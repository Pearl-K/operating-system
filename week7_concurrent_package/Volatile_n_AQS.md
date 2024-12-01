# Volatile
## Volatile이 필요할 때 = 메모리 가시성이 필요할 때

<img src="https://github.com/user-attachments/assets/f769aa76-286c-4c9b-b5a2-28a884d8927e" width=900>

멀티 스레드 환경에서 runFlag = false로 값을 변경하면, 해당 변경 사항이 while(runFlag == true) 작업을 실행하고 있는 다른 스레드에서 언제 보일까?


위 그림과 같이 멀티 스레드 환경에서 한 스레드가 변경한 값이 다른 스레드에서 언제 보이는지에 대한 문제를 메모리 가시성
(memory visibility)이라 한다. 이름 그대로 메모리에 변경한 값이 보이는가, 보이지 않는가의 문제이다.

+ 캐시 메모리를 사용하면 CPU 처리 성능을 개선할 수 있으나, 때로는 이런 성능 향상보다 여러 스레드에서 같은
시점에 정확히 같은 데이터를 보는 것이 더 중요할 수 있다.
+ 이를 위해 성능을 약간 포기하고, 값을 읽거나 쓸 때 메인 메모리에 직접 접근하여 해결할 수 있다.
+ 한 스레드에서 변경한 값이 다른 스레드에서 즉시 보일 수 있게 volatile 을 사용한다.


# AQS (Abstract Queued Synchronizer)
## AQS 내부 Volatile의 사용


## Volatile int state 를 사용한 다양한 상태 관리
### Semaphore


### ReentrantLcok


### CountDownLatch


#### (+ 참고) 그 외, ReentrantReadWriteLock, CyclicBarrier, Exchanger 가 존재한다.
+ ReentrantReadWriteLock: 읽기-쓰기 락으로, 여러 스레드가 읽기를 동시에 할 수 있으나 쓰기는 한 번에 하나의 스레드만 할 수 있도록 제한
+ CyclicBarrier: 여러 스레드가 특정 지점에서 만나서 동기화될 수 있도록 한다. 카운트다운을 사용하여 스레드들이 모두 준비될 때까지 기다리게 만든다.
+ Exchanger: 두 스레드가 데이터를 교환할 수 있도록 하는 동기화 도구



