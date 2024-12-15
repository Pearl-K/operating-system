## Future와 Callable
- **Future** : 비동기 작업의 결과를 나타내는 객체로, ExecutorService 같은 스레드 풀에서 Callable 작업을 Submit한 후 이 결과를 가져올 수 있게 해준다.
작업의 완료를 대기하거나 상태를 알 수 있다.
- **Callable** : 비동기 작업을 실행하고 결과를 반환하는 작업에 사용되는 객체이다.

```java
Future<Integer> future = es.submit(new MyCallable());
```

ExecutorService가 제공하는 submit() 메서드를 통해 Callable을 작업으로 전달할 수 있다.

예제 코드

```java
public static void main(String[] args) throws ExecutionException, InterruptedException {
         ExecutorService es = Executors.newFixedThreadPool(1);
         Future<Integer> future = es.submit(new MyCallable()); // 바로 Future 리턴
         
         // 결과가 있으면 바로 return
         // 없으면 Blocking
         Integer result = future.get();
         
         log("result value = " + result);
         es.close();
}
     
static class MyCallable implements Callable<Integer> {
    @Override
    public Integer call() {
        log("Callable 시작");
        sleep(2000);
        int value = new Random().nextInt(10); 
        log("create value = " + value); 
        log("Callable 완료");
        return value;
    }
}
```

ExecutorService에서 작업의 결과가 아닌 미래에 해당 작업이 끝났을 때 가져올 수 있는 Future라는 객체를 반환한다.
해당 객체의 get 메서드를 통해서 MyCallable에서의 call()의 결과를 반환 받을 수 있다.

### 동작

1. submit() 의 호출로 MyCallable 의 인스턴스를 전달한다.
2. 결과로 Future를 바로 리턴하게 된다. 왜냐하면 call()을 실행하는 건 별도의 스레드이기 때문에 언제 실행될 지 알 수 없다. Future를 리턴하게 되면 Main 스레드는 바로 다른 작업을 수행할 수 있다.
3. 이후에 get()을 호출하면 작업 완료 여부에 따라 Blocking 되거나 결과를 받게 된다.

→ 단순하게 정리하면, **Future** 는 전달한 작업의 미래 결과를 담고 있다고 생각하면 된다.
<img width="787" alt="2222222" src="https://github.com/user-attachments/assets/5b635924-9e06-444d-8053-552962f71c68">


submit()을 하게 되면 taskA가 BlockingQueue에 담기는 게 아니라 Future(FutureTask)로 감싸고 이를 큐에 넣게 된다.

### 왜 Future가 필요할까?
```java
Integer sum1 = es.submit(task1); // 여기서 블로킹 
Integer sum2 = es.submit(task2); // 여기서 블로킹
```

위 예시에서 보면 알 수 있듯이 실제 task1이 끝나야지만 sum1에 결과를 넣고 sum2를 처리하러 갈 수 있기 때문에 각 작업이 2초씩 걸린다고 가정하면 총 4초의 시간이 걸리게 된다.

```java
Future<Integer> sum1 = es.submit(task1); // 여기서 블로킹 
Future<Integer> sum2 = es.submit(task2); // 여기서 블로킹
```

반면 위 코드는 바로 Blocking 되지 않고 바로 Future 타입을 리턴받기 때문에 뒤의 sum2도 바로 계산되도록 할 수 있다.

※ 위의 코드의 문제점
- 단 위의 코드는 sum1.get()을 통해서 값을 가져오려고 시도하는 순간 블로킹돼서 해당 작업이 끝날 때까지 기다려야 한다.
만약에 뒤에 추가적인 작업이 있다면, 비동기 처리 과정에서 약간의 시간적인 손해를 보게 된다.
- 가장 원하는 건 사실 둘 다 끝나고 나서 둘의 결과 값을 가지고서 어떤 처리를 하되. 
둘 중에 가장 늦게 끝나는 시간에 맞춰서 해당 작업이 시작되길 바랄 것이다. 
하지만 Future에서는 아쉽게도 작업에 대해서 **조합이 불가능**하다.
- 또한, 해당 작업에서 문제가 생겨서 예외가 생겼을 때 처리를 해주는 것이 정말 힘들다.

### 주요 메서드
```java
  public interface Future<V> {
     boolean cancel(boolean mayInterruptIfRunning);
     boolean isCancelled();
     boolean isDone();
     V get() throws InterruptedException, ExecutionException;
     V get(long timeout, TimeUnit unit)
         throws InterruptedException, ExecutionException, TimeoutException;
     enum State {
         RUNNING,
         SUCCESS,
         FAILED,
         CANCELLED
    }
     default State state() {}
 }
```

**boolean cancel(boolean mayInterruptIfRunning)**
<br>기능: 아직 완료되지 않은 작업을 취소한다.
<br>매개변수: mayInterruptIfRunning

- cancel(true) : Future 를 취소 상태로 변경한다. 이때 작업이 실행중이라면 Thread.interrupt() 를 호출해서 작업을 중단한다.
- cancel(false) : Future 를 취소 상태로 변경한다. 단 이미 실행 중인 작업을 중단하지는 않는다.


- 반환값 : 작업이 성공적으로 취소된 경우 true , 이미 완료되었거나 취소할 수 없는 경우 false
- 설명 : 작업이 실행 중이 아니거나 아직 시작되지 않았으면 취소하고, 실행 중인 작업의 경우 mayInterruptIfRunning 이 true 이면 중단을 시도한다.

**boolean isCancelled()**
- 기능 : 작업이 취소되었는지 여부를 확인한다.
- 반환값 : 작업이 취소된 경우 true , 그렇지 않은 경우 false
- 설명 : 이 메서드는 작업이 cancel() 메서드에 의해 취소된 경우에 true 를 반환한다.


**State state()**
- 기능 : Future 의 상태를 반환한다. 자바 19부터 지원한다.
  - RUNNING : 작업 실행 중
  - SUCCESS : 성공 완료
  - FAILED : 실패 완료
  - CANCELLED : 취소 완료

**get(long timeout, TimeUnit unit)**
- 기능 : get() 과 같은데, 시간 초과되면 예외를 발생시킨다.
- **매개변수**
  - timeout : 대기할 최대 시간
  - unit : timeout 매개변수의 시간 단위 지정
  - 반환값 : 작업의 결과

위에서는 Future에 대해서 어느정도 정의를 하고 있다. Java 5버전을 사용하고 있다면 해당 방법을 최대한 활용을 하는 게 맞다.
하지만, Java 8 이상의 버전을 사용하는 경우에는 위의 Future 클래스의 문제점을 해결할 수 있는 CompletableFuture를 사용하는 걸 권장한다.

Java5에서 제공하는 Future의 단점을 정리해보면 다음과 같다.
- 외부에서 완료시킬 수 없고 get의 타임아웃 설정으로만 완료 가능
- 블로킹 코드(get)을 통해서만 이후의 결과를 처리할 수 있다.
- 여러 Future에 대해서 조합하는 것이 불가능하다.
- 예외가 발생하면 해당 쓰레드에만 적용이 되며 일의 순서가 꼬인다.

CompletableFuture에서 위의 문제를 해결하기 위해 지원하는 것은 아래와 같다.
- 비동기 작업 실행(supplyAsync라는 메서드를 통해서 Supplier를 인수로 받아 비동기적으로 결과를 생성한다.)
- 작업 콜백(작업을 join으로 한꺼번에 모든 작업이 끝날 때까지 기다렸다가 가져올 수 있다.)
- 작업 조합(thenCompose(), thenApply()와 같은 메서드를 통해 이루어진다.)
- 예외 처리(타임아웃이 났을 때 completeOnTimeout과 같은 메서드를 통해 Default 설정을 할 수 있다, 아니면 exceptionally를 사용해 예외일 때 처리도 할 수 있다.)

다음 내용부터는 모던 자바인액션 15,16 장의 내용을 정리한 내용이며, 해당 내용에 대해서 더 학습하고 싶다면 해당 내용을 보기를 권장한다.

최저 가격 애플리케이션에서 DB를 통해 상점의 가격 정보를 조회해오는 외부 서비스에 접근한다고 해보자. 
이 때, 해당 접근 시간은 대략 1초가 걸린다. 그럼 이와 유사한 기능을 하는 Shop Class를 만든다면 아래와 같이 작성할 수 있다.

```java
public class Shop {
  public boule getPrice(String product) {
    return calculatePrice(product);
  }

  public static void delay() {
    try {
      Thread.sleep(1000L);
    } catch(InterruptedException e) {
      throw new RuntimeException(e);
    }
  }

  private double calculatePrice(String product) {
    delay();
    return random.nextDouble() * product.charAt(0) + product.charAt(1);
  }
}
```

이제 calculatePrice()라는 동기적인 메서드를 CompletableFuture를 사용해 비동기 메서드로 변환을 해보자.
그러면 아래와 같은 방법으로 변환을 시킬 수 있다.

```java
public Future<Double> getPriceAsync(String product) {
	CompletableFuture<Double> futurePrice = new CompletableFuture<>();
	new Thread(() -> {
		double price = calculatePrice(product);
		futurePrice.complete(price); // 오래 걸리는 계산이 완료되면 Future에 값을 설정
	}).start();
	return futurePrice;
}
```

그러면, 해당 작업이 끝나는 것에 관계없이 별도의 쓰레드에서 비동기적으로 작업이 처리되고, 그 동안 다른 요청을 처리할 수 있게 된다.
그리고 다른 요청이 완전히 끝났을 때, 비동기적으로 실행 시킨 작업의 결과값을 받아와 최종 결과를 내놓으면 된다.


```java
Shop shop = new Shop("BestShop");
long start = System.nanoTime();
Future<Double> futurePrice = shop.getPriceAsync("my favorite product");
doSomethingElse(); // 가격을 찾는 동안 다른 작업 수행
try {
	double price = futurePrice.get();
} catch(Exception e) {
	throw new RuntimeException(e);
}
```

기존 Future에서는 Future.get() 호출 시 작업이 실패하면 ExecutionException을 발생시킨다.
이를 통해 예외를 확인할 수는 있으나, 처리를 하는 것은 힘들었다.
이는 ExecutionException으로 확인이 가능은 한데, 실시간 처리가 불가능하다.
정확하게 작업 예외를 할 수 있는 시점은 get() 메서드로 결과를 가져올 때만 예외를 확인할 수 있기에 해당 과정에서 추가적인 처리를 하지 못한다.


하지만 CompletableFuture는 이 비동기 처리를 하는 동안의 에러가 발생했을 때 명확하게 에러를 잡아와 처리할 수 있게 해준다.
다음과 같이 코드를 작성하면 completeExceptionally를 통해 클라이언트에게 에외를 전달할 수 있다.

```java
public Future<Double> getPriceAsync(String product) {
	CompletableFuture<Double> futurePrice = new CompletableFuture<>();
	new Thread(() -> {
		try {
			double price = calculatePrice(product);
			futurePrice.complete(price); // 계산이 정상적으로 종료 되면 Future에 가격 정보를 저장한 채로 Future를 종료한다.
		} catch(Exception ex) {
			futurePrice.completeExceptionally(ex); // 도중에 문제가 발생하면 발생한 에러를 포함시켜 Future를 종료한다.
		}
	}).start();
	return futurePrice;
}
```

아니면 CompletableFuture에서 체이닝으로 exceptionally를 통해서 예외 상황에서 후속 작업을 명확하게 정의하여 실시간 처리를 할 수 있게 할 수 있다.

```java
CompletableFuture<Double> futurePrice = CompletableFuture.supplyAsync(() -> {
    if (product.equals("error")) {
        throw new RuntimeException("상품 정보 없음");
    }
    return 123.45;
}).exceptionally(ex -> {
    System.out.println("예외 발생: " + ex.getMessage());
    return 0.0;
});

System.out.println(futurePrice.join());
```

다른 방법으로는 javascript에서 성공 시, 실패 시 콜백 같은 걸 호출하듯이 성공, 실패에 따른 값을 조작할 수 있게 해주는 handle이라는 메서드도 있다.

```java
@ParameterizedTest
@ValueSource(booleans =  {true, false})
void handle(boolean doThrow) throws ExecutionException, InterruptedException {
    CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
        if (doThrow) {
            throw new IllegalArgumentException("Invalid Argument");
        }

        return "Thread: " + Thread.currentThread().getName();
    }).handle((result, e) -> {
        return e == null
                ? result
                : e.getMessage();
    });

    System.out.println(future.get());
}
```

또한, thenCompose()와 thenCombine()을 사용해서 작업을 조합할 수 있다.

- thenCompose
  - 두 작업이 이어서 실행되도록 조합하며, 앞선 작업의 결과를 받아서 사용
  함수형 인터페이스 Function을 파라미터로 받음.
- thenCombine
  - 두 작업을 독립적으로 실행하고 둘 다 완료됐을 때 콜백을 실행함.

위에 대한 설명을 읽어보면 알겠지만, thenCombine()은 작업을 동시에 실행 시키고 가장 늦게 끝나는 게 완전히 끝났을 때 실행할 콜백을 정의하는 메서드이다.
반면, thenCompose()는 작업 간의 관계가 있는 경우, 만약에 비동기 c 작업을 위해서 a,b라는 비동기 작업이 끝나야 되는 거면
a,b 작업이 끝나길 기다렸다가 a에게 b 값을 전달하건 b에게 a값을 전달해서 c라는 비동기 작업을 시작하는 Future를 하나 새로 정의하는 개념에 가깝다.

그래서 각각 받는 함수형 인터페이스도 thenCombine의 경우엔 BiFunction을 thenCompose에서는 Function을 받는다.
책에서는 예시가 문맥이 많이 필요해서 이건 망나니 개발자님의 예시가 더 좋을 것 같아서 가져왔다.

```java
// thenCompose 방식은 완료된 작업을 전달받아서 처리한다.
@Test
void thenCompose() throws ExecutionException, InterruptedException {
  CompletableFuture<String> hello = CompletableFuture.supplyAsync(() -> {
    return "Hello";
  });

  // Future 간에 연관 관계가 있는 경우
  CompletableFuture<String> future = hello.thenCompose(this::mangKyu);
  System.out.println(future.get());
}

private CompletableFuture<String> mangKyu(String message) {
  return CompletableFuture.supplyAsync(() -> {
    return message + " " + "MangKyu";
  });
}

// thenCombine은 독립적으로 실행한 뒤에 BiFunction으로 작업을 합친다.
@Test
void thenCombine() throws ExecutionException, InterruptedException {
  CompletableFuture<String> hello = CompletableFuture.supplyAsync(() -> {
    return "Hello";
  });

  CompletableFuture<String> mangKyu = CompletableFuture.supplyAsync(() -> {
    return "MangKyu";
  });

  CompletableFuture<String> future = hello.thenCombine(mangKyu, (h, w) -> h + " " + w);
  System.out.println(future.get());
}
```
CompletableFuture를 사용하면 timeout과 같은 경우에 더 다양한 조합을 가능하게 해준다. 
만약에 거래를 하는데 외국에 있는 물품을 살 수 있다고 해보자. 
그러면 해당 상품을 조회해서 가격을 알아내고 해외와의 환율도 알아내어 거래에 대해서 계산을 해야한다.

그런데 둘 다 외부에서 정보를 가져오는 것이다보니 둘 다 어느정도까지 기다릴 지에 대해서 결정을 해야 한다.
환율은 1초, 전체 상품 조회에서 가격을 알아내는데까지 3초라면 기존 Future로는 정의하기가 어려웠다.
하지만, CompletableFuture를 사용하면 다음과 같이 정의할 수 있다. 
심지어 아래에는 환율을 조회하는 것에 대해서 timeout이 난 경우 default로 계산도 할 수 있게 해준다.

```java
Future<Double> futurePriceInUSD =
        CompletableFuture.supplyAsync(() -> shop.getPrice(product))
                .thenCombine(
                        CompletableFuture.supplyAsync(
                                () -> exchangeService.getRate(Money.EUR, Money.USD))
                                .completeOnTimeout(DEFAULT_RATE, 1, TimeUnit.SECONDS),
                        (price, rate) -> price * rate
                ).orTimeout(3, TimeUnit.SECONDS);
```

CompletableFuture를 사용한 비동기처리를 잘 만드는 방법은 무엇일까?라고 묻는다면 CompletableFuture를 쓰레드 풀에 맞게 커스텀해서 사용하기에 좋다.
모던자바인액션에서의 저자는 CompletableFuture를 스트림에 활용할 거면 스트림의 게으른 연산에 관해서 명확하게 이해를 하고 어떻게 스트림을 분리할 지 고민하고 사용하라는 뉘앙스로 언급을 하며,
해당 예시를 들어준다. 또한, stream 라이브러리에서 제공하는 병렬 스트림을 함부로 사용하지 않을 걸 권장한다.

그 이유는 다음과 같다. 우선 에시를 들기 위해서 다음과 같이 상점 목록이 있다고 해보자.
그리고 그 상점의 가격을 가져오는데 각각 1초가 걸린다고 하자.
또한 우리가 원하는 건 해당 작업을 통해 가격을 얻어내고 가게의 이름과 물품의 가격을 리스트로 출력하는 것이다.

```java
private final List<Shop> shops = Arrays.asList( // 상점 목록
        new Shop("BestPrice"),
        new Shop("LetsSaveBig"),
        new Shop("MyFavoriteShop"),
        new Shop("BuyItAll"));
            
public List<String> findPrices(String product);
```

그러면 이 작업을 stream으로 실행을 하려고 하면 stream은 각 요소 별로 해당 시점에 대해서 게으르게 동작한다는 특징 때문에
요소의 개수만큼 시간이 걸려서 위의 경우에 총 처리는 4초가 걸린다.

```java
public List<String> findPrices(String product) {
	return shops.stream()
            .map(shop -> String.format("%s price is %.2f", 
                    shop.getName(), shop.getPrice(product)))
            .collect(toList());
```

물론 이걸 병렬 스트림으로 바꾸면? 문제는 위의 경우에는 생기지 않는다. 
하지만 가게의 개수가 많아지면? 1초 안에 안 끝날 수 있다. 그 이유는 parallelStream에서는 쓰레드 개수를 임의로 설정할 수 없기 때문이다.
그래서 만약에 4개의 쓰레드가 주어진다면 5개일 땐 위의 경우 2초가, 9개일 땐 3초가 걸리게 된다. 
4개의 쓰레드가 동시에 동작하고 반환된 게 다음 작업을 잡기 때문이다.

따라서, 커스텀 executor를 사용하고 대기 시간과 계산 시간의 비율을 결정할 것을 권고하며 병렬 프로그래밍에서 제시하는 스레드 풀 조절 방안을 사용할 걸 권고한다.
아래는 책에서 나온 예시를 그대로 가져온 것이다.

Nthreads = Ncpu * Ucpu * (1 + W/C)

- Ncpu는 Runtime.getRuntime().availableProcessors()가 반환하는 코어의 개수
- Ucpu는 0과 1 사이의 값을 갖는 CPU 활용 비율
- W/C는 대기시간과 계산 시간의 비율

따라서 상점의 응답을 대략 99퍼센트만큼 기다린다고 하면 W/C의 비율을 100으로 간주할 수  있다.
즉 대상 CPU 활용률이 100퍼센트라면 400개의 스레드를 가진 스레드 풀을 만들어야 한다.
하지만, 상점 수보다 많은 스레드를 가지고 있는 건 자원 낭비이다. 따라서 상점 수만큼의 스레드를 갖도록 executor를 설정할 수 있다.

- 주의사항 1: 스레드 수가 너무 많으면 서버가 크래시될 수 있으므로 하나의 Executor에서 사용할 스레드의 최대 개수는 100개 이하로 설정하는 게 바람직하다.
- 주의사항 2: 데몬 스레드를 활용해서 Executor를 생성한다. 이는 작업이 끝나지 않는 어떤 쓰레드가 있으면 자바 프로그램이 종료되지 않는 문제가 발생하기 때문이다.

따라서 다음과 같이 우선 쓰레드 풀을 정의를 한다.

```java
private final Excecutor executor =
        Executors.newFixedThreadPool(Math.min(shops.size(), 100), new ThreadFactory() {
            public Thread new Thread(Runnable r) {
                Thread t = new Thread(r);
                t.setDaemon(true); // 프로그램의 종료를 방해하지 않는 데몬 스레드를 사용한다.
            return t;
            }
        });
```

그리고나서 일반 스트림으로 작업을 정의를 하되, 해당 작업에 대해서 마지막에 결과를 모두 합치는 부분은 별도의 스트림으로 분류를 하고,
그렇지 않은 부분은 작업을 조합을 하건 해서 지지고 볶건 상관없이 먼저 실행한다. 그러면 예시 코드는 다음과 같이 나올 것이다.

```java
public List<String> findPrices(String product) {
	List<CompletableFuture<String>> priceFutures = 
            shops.stream()
                    .map(shop -> CompletableFuture.supplyAsync(
                            () -> shop.getPrice(product), executor))
                    .map(future -> future.thenApply(Quote::parse))
                    .map(future -> future.thenCompose(quote -> 
                            CompletableFuture.supplyAsync(
                                    () -> Discount.applyDiscount(quote), executor))).collect(toList());
						
	return priceFutures.stream()
                            .map(CompletableFuture::join)
                            .collect(toList());
}
```

그러면 위와 같은 경우, 작업 조합이 있어서 가격을 가져오는 시간 + 파싱을 하고 작업을 조합해서 할인 값을 적용한 값을 가져오는 것까지 
가장 오래 걸리는 녀석의 시간만큼만 걸릴 것이고, 그 해당 값을 조합을 할 수 있게 된다.

---
※ 참고자료
- 모던자바인액션
- https://mangkyu.tistory.com/263
- 김영한, 자바 고급편