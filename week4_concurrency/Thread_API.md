## pthread_join의 역할은 무엇이고 어떠한 경우에 활용할 수 있나?

- Java에서 `pthread_join`과 유사한 역할을 하는 메서드는 `Thread.join()`이다.
- `Thread.join()`은 특정 스레드가 완료될 때까지 현재 스레드를 대기시키는 메서드이다.
- join()을 통해 여러 스레드 중 하나가 완료되기 전까지, 다른 스레드가 해당 작업을 기다리도록 만들 수 있다. 즉, 스레드 간의 종료 동기화를 보장한다.

### 활용 예시

- 스레드가 병렬로 작업한 후, 최종적으로 메인 스레드나 다른 스레드가 그 결과를 사용해야 할 때
- 여러 스레드가 데이터를 병렬로 처리한 후, 메인 스레드에서 그 결과를 통합하거나 후속 작업을 진행해야 하는 상황에서 join()을 사용하여 모든 스레드가 완료될 때까지 기다린다.
- join() 으로 종료 동기화가 보장되면 결과를 이용하거나 후속 작업에 필요한 행동을 미리 종료시키는 용도이다.

### join() 을 하지 않았을 때 사고가 날 수 있는 경우

- 메인 스레드가 아직 작업이 완료되지 않은 스레드의 결과를 사용하려 할 때 데이터 불일치나 미완성된 작업을 처리하려고 하는 문제가 생길 수 있다.



## Condition Variable에 대해서

- Java에서는 Condition 객체가 조건 변수의 역할을 수행한다. (java.util.concurrent.locks.Lock 함께 사용)
- 특정 작업을 기다릴 때 sleep 상태로 전환하여 CPU 낭비를 줄이기 위해 condition variable(조건 변수)를 사용할 수 있다.
- 조건 변수는 스레드가 특정 조건이 만족되지 않았을 때 큐 안에서 대기하게 만들어준다.
- 다른 스레드에서 큐 안에서 sleep 상태로 대기 중인 스레드를 깨우면 해당 스레드는 큐에서 나와서 다시 실행 가능한 상태가 됩니다. (해당 동작은 3번에서 더 자세히 설명한다.)

#### +@ 대기 상태의 스레드를 관리하는 Queue

- 조건 변수를 사용하여 일시적으로 멈춘 스레드들을 관리하는 자료 구조로, 조건이 충족되면 대기열에 있는 스레드 중 하나(또는 여러 개)가 깨어나서 다시 실행된다.
- 이 큐는 OS나 라이브러리 수준에서 제공되며, 스레드들이 조건 변수가 충족될 때까지 안전하게 대기하고 다시 실행할 수 있도록 관리한다.



## pthread_cond_wait와 pthread_cond_signal의 역할

Java에서는 `pthread_cond_wait`와 `pthread_cond_signal`에 해당하는 메서드로 `Condition.await()`와 `Condition.signal()` 또는 `Condition.signalAll()`을 사용한다.

### `pthread_cond_wait` → `Condition.await()`

#### 역할: 스레드를 조건 변수에서 대기 상태로 만든다.

스레드는 특정 조건이 충족될 때까지 block되고, 이때 Lock 객체와 함께 사용되어 락을 자동으로 해제하고 대기 상태에 들어간다.

### `pthread_cond_signal` → `Condition.signal()`

#### 역할: 대기 중인 스레드 중 하나를 깨워서 작업을 이어가도록 만든다.

만약, 여러 스레드가 대기 중이라면 `signal()`은 하나의 스레드만 깨우고, `signalAll()`은 대기 중인 모든 스레드를 깨운다.

### 사용 예시

#### Producer/Consumer (생산자/소비자) 문제가 생겼을 때

- 생산자-소비자 문제는 동기화와 조건 변수 사용의 대표적인 예시가 될 수 있다.
- 생산자와 소비자는 공유 자원(ex. 버퍼)을 통해 데이터를 주고받으며, 서로가 일정한 규칙에 따라 동작해야 한다.
- 여기에서 Condition.await()와 Condition.signal()을 활용한다.

### 문제 상황

- 생산자는 데이터를 생산하고 공유 버퍼에 넣는다. (생산)
- 소비자는 버퍼에서 데이터를 가져가서 처리한다. (소비)
- 이 때 버퍼가 꽉 찼을 경우, 생산자는 더 이상 데이터를 넣을 수 없으므로, 소비자가 버퍼에서 데이터를 가져가서 버퍼를 비우기 전까지 생산자는 대기 상태로 있어야 한다.
- 반대로 버퍼가 비었을 경우, 소비자는 가져갈 데이터가 없으므로 생산자가 버퍼에 데이터를 넣을 때까지 대기해야 한다.

### 해결 방법

- 생산자는 버퍼가 꽉 차면 `Condition.await()`를 호출하여 대기 상태에 들어가고, 소비자가 버퍼에서 데이터를 가져가 버퍼에 여유 공간이 생기면 소비자가 `Condition.signal()`을 호출하여 대기 중인 생산자를 깨운다.
- 소비자도 마찬가지로 버퍼가 비었을 때 `Condition.await()`를 호출하여 대기 상태로 들어가고, 생산자가 데이터를 넣으면 생산자가 `Condition.signal()` 호출로 대기 중인 소비자를 깨워 버퍼에서 데이터를 가져가게 한다.

```java
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class Buffer {
    private final int[] buffer;
    private int count, in, out;
    private final Lock lock;
    private final Condition notFull, notEmpty;

    public Buffer(int size) {
        buffer = new int[size];
        count = in = out = 0;
        lock = new ReentrantLock();
        notFull = lock.newCondition();
        notEmpty = lock.newCondition();
    }

    public void produce(int value) throws InterruptedException {
        lock.lock();
        try {
            while (count == buffer.length) {
                notFull.await(); // 버퍼가 꽉 차면 생산자는 대기
            }
            buffer[in] = value;
            in = (in + 1) % buffer.length;
            count++;
            notEmpty.signal(); // 버퍼가 비지 않았다고 소비자에게 알림
        } finally {
            lock.unlock();
        }
    }

    public int consume() throws InterruptedException {
        lock.lock();
        try {
            while (count == 0) {
                notEmpty.await(); // 버퍼가 비었으면 소비자는 대기
            }
            int value = buffer[out];
            out = (out + 1) % buffer.length;
            count--;
            notFull.signal(); // 버퍼가 가득 차지 않았다고 생산자에게 알림
            return value;
        } finally {
            lock.unlock();
        }
    }
}
```



1. Buffer 클래스가 생산자-소비자 문제를 해결하기 위한 버퍼 역할을 한다.
2. `produce()` 메서드는 생산자가 버퍼에 데이터를 추가하는 작업을 수행한다.
3. `consume()` 메서드는 소비자가 버퍼에서 데이터를 가져가는 작업을 수행한다.
4. `Condition.await()`와 `Condition.signal()`을 이용해 버퍼의 상태에 따라 스레드 간 동기화 한다.
   → 이러한 메커니즘으로 스레드 간 race condition이나 데드락을 방지할 수 있고, 자원의 일관성 있는 처리를 보장할 수 있다.



## 추가 자료: Java 에서 `Object.wait()` 와 `Condition.await()` 의 차이

Object `wait()`와 `notify()`는 Java 객체에 내장된 기본 동기화 메커니즘으로, 스레드를 구분하여 통지하는 것이 불가능하다.
Condition은 더 유연하고 세분화된 동기화 제어를 제공하여 이 문제를 해결한다.

| **특징**             | **`Object.wait()`**                                        | **`Condition`**                                              |
| -------------------- | ---------------------------------------------------------- | ------------------------------------------------------------ |
| **락 사용**          | `synchronized` 블록 내에서 사용, 자동으로 모니터 락을 사용 | `Lock` 객체와 함께 사용                                      |
| **복수 조건 처리**   | 하나의 객체에 대해 하나의 조건만 처리 가능                 | 하나의 `Lock`에 여러 `Condition`을 사용해 복수 조건 관리 가능 |
| **유연성**           | 기본적이고 단순한 동기화, 복잡한 조건 처리에는 부적합      | 더 유연하고 세밀한 동기화 가능                               |
| **대기 스레드 관리** | `wait()`는 대기 중인 스레드에 대해 구체적인 제어가 어려움  | `await()`는 더 세밀한 대기 스레드 제어 가능                  |
| **신호 메서드**      | `notify()`, `notifyAll()`                                  | `signal()`, `signalAll()`                                    |
| **호출 위치**        | `synchronized` 블록 내부에서만 사용 가능                   | `Lock`이 사용되는 임의의 코드 블록에서 사용 가능             |

복잡한 스레드 동기화나 여러 조건을 관리해야 하는 경우에는 Condition을 사용하는 것이 더 적합하다.
(ex. 여러 조건에 따라 스레드를 분기 처리해야 하거나 더 정밀한 락 제어가 필요한 경우)