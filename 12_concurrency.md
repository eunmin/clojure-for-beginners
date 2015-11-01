# 병렬 처리에서 데이터 공유

하나의 데이터를 여러 쓰레드가 함께 사용하면서 작업하는 예를 들어보자.

다음은 숫자 값에 counter라는 심볼로 연결한 Var를 만들고 두개 쓰레드에서 출력하는 예제다.

```clojure
(def counter 0)

(defn new-thread []
  (Thread.
    (fn []
      (locking *out*
        (println "Thread:" (.getName (Thread/currentThread))
          "Counter:" counter)))))

(defn run []
  (.start (new-thread))
  (.start (new-thread)))
```

```bash
user=> (run)
nil
Thread: Thread-1 Counter: 0
Thread: Thread-2 Counter: 0
```

중간에 생소한 `locking` 매크로가 나오는데 이것은 println이 standout으로 쓸 때 내부적으로
버퍼 사이즈 만큼 나눠 쓰게 될텐데 쓰레드 간 순서 없이 쓰기 때문에 println의 결과가 내부 버퍼 사이즈 단위로
섞여 보여서 println을 쓸 때 lock을 걸고 쓰기를 동기화 해주기 위해서 사용했다.
출력 결과를 보기 좋게 하기 사용한 것이기 때문에 크게 신경 쓰지 않아도 된다.

위 예제에서 두개의 쓰레드를 만들고 실행시켜 counter 값을 출력 해보니 잘 나왔다.

이번에는 각 쓰레드에서 1씩 증가시켜 보려고 하는데 문제가 있다.
클로저의 값은 변경 불가하기 때문에 증가시킨 값을 넣을 수가 없다.

한가지 떠오르는 것은 `4. Var`에서 다룬 `alter-var-root`로 새로운 값으로 var를 다시 바인딩 하는 것이다.
예제에 집중하기 위해 출력하는 함수는 지우자. 그리고 여러개의 쓰레드를 편하게 실행하기 위해 `dotime`를 사용한다.

```clojure
(def counter 0)

(defn new-thread []
  (Thread.
    (fn []
      (Thread/sleep (rand-int 10))
      (alter-var-root (var counter) (fn [origin-value] (inc origin-value))))))

(defn run []
  (dotimes [_ 100]
    (.start (new-thread))))
```

```bash
user=> (run)
nil
user=> counter
100
```

Thread에 sleep을 준 이유는 쓰레드 생성과 동시에 start를 하기 때문에 명확하게 임의의 순서로 실행되도록 약간의 sleep을 줬다.

위에서 `alter-var-root`로 값을 바꾼 것처럼 작성해봤다. `alter-var-root` 함수가 값을 바꾸는 함수가
아니고 새 값에 var를 다시 연결하는 함수기 때문에 수행 할 때마다 새로운 값이 생기고 그 값에 연결된다.
다른 쓰레드는 새로 연결된 값을 가져와서 다시 증가시켜 다시 counter 심볼에 새로운 값을 연결한다.

병렬처리에 대한 것을 다뤄본 사람이라면 쓰레드 동기화 문제가 있을 것이라고 생각할 것이다.
위 예제가 잘 동작한 이유는 `alter-var-root` 함수가 쓰레드 동기화가 되는 함수이기 때문이다.

클로저 `core.clj`에 있는 `alter-var-root`는 실제로 자바의 `Var.java`에 있는 `alterRoot` 메서드를 호출 하는데
이 메서드는 `synchronized`가 걸려있어 문제 없이 동작 하는 것이다.
하지만 이`synchronized`는 락 기반의 동기화이기 때문에 많은 여러 쓰레드에서 변경이 잦은 경우 그다지 효율적이지 않다.

클로저는 이런 데이터를 다루기 위해 특별한 기능을 몇가지 제공한다.
레퍼런스(`ref`)와 애텀(`atom`)그리고 에이전트(`agent`)이다.
각각의 특징들을 알아보면 언제 사용하면 좋을지 알 수 있다.

## 레퍼런스

## 애텀

## 에이전트
