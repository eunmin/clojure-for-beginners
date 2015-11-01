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
      (alter-var-root (var counter) inc))))

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

위에서 `alter-var-root`로 값을 바꾼 것처럼 작성해봤다. `alter-var-root` 함수가 값을 바꾸는 함수가
아니고 새 값에 var를 다시 연결하는 함수기 때문에 수행 할 때마다 새로운 값이 생기고 그 값에 연결된다.
다른 쓰레드는 새로 연결된 값을 가져와서 다시 증가시켜 다시 counter 심볼에 새로운 값을 연결한다.

`alter-var-root`의 마지막 인자는 함수인데 원래 값을 넘겨주는 함수를 넣어주면 된다.
`inc`는 값을 하나 받아 증가한 값을 리턴해주는 함수이다.

병렬처리에 대한 것을 다뤄본 사람이라면 쓰레드 동기화 문제가 있을 것이라고 생각할 것이다.
위 예제가 잘 동작한 이유는 `alter-var-root` 함수가 쓰레드 동기화가 되는 함수이기 때문이다.

클로저 `core.clj`에 있는 `alter-var-root`는 실제로 자바의 `Var.java`에 있는 `alterRoot` 메서드를 호출 하는데
이 메서드는 `synchronized`가 걸려있어 문제 없이 동작 하는 것이다.
하지만 이`synchronized`는 락 기반의 동기화이기 때문에 많은 여러 쓰레드에서 변경이 잦은 경우 그다지 효율적이지 않다.

클로저는 이런 데이터를 다루기 위해 특별한 기능을 몇가지 제공한다.
레퍼런스(`ref`)와 애텀(`atom`)그리고 에이전트(`agent`)이다.
각각의 특징들을 알아보면 언제 사용하면 좋을지 알 수 있다.

## 레퍼런스

레퍼런스는 `ref`라는 구문으로 생성할 수 있다.

다음은 위에서 작성한 예제를 레퍼런스로 바꾼 예다.

```clojure
(def counter (ref 0))

(defn new-thread []
  (Thread.
    (fn []
      (dosync
        (alter counter inc)))))

(defn run []
  (dotimes [_ 100]
    (.start (new-thread))))
```

```bash
user=> (run)
nil
user=> @counter
100
```

역시 결과는 잘 나온다. 위의 예제와 다른 점은

- counter 값을 `ref` 구문으로 싼다.
- 값을 변경할 때 `alter` 구문을 사용한다.
- 값을 변경하는 구문은 `dosync`로 감싼다.
- 값을 읽을 때는 `@`를 붙인다.

레퍼런스는 락을 사용해서 동기화하지 않고 데이터베이스에서 대부분 사용하고 있는 MVCC라는 방식으로 처리를 동기화 한다.
MVCC에 대한 자세한 설명을 위키등에서 참고 하면 되고 간단히 설명하면 다른 쓰레드가 데이터를 변경하는 것과 관계 없이
데이터의 복사 본을 만들고 복사본을 가지고 동기화 코드 부분을 처리한 후 마지막에 커밋을 해서 데이터를 변경하고
만약 컨플릿(다른 쓰레드가 변경한 것이 있음)이 나면 롤백하고 다시 처음 부터 수행한다. 컨플릿이 또 나면 다시 시작한다.

구현자는 내부 구현에 관계 없이 구현할 수 있을 것 같지만 주의 사항이 있다.
동기화 되는 코드 부분(`dosync`로 감싼 부분)은 여러번 수행 될 수 있다는 점이다.
클로저의 코드 대부분은 부수효과가 없기 때문에 여러번 수행되어도 관계 없지만 부수효과가 있는 코드가 `dosync`안에 있다면
원하지 않은 결과가 일어 날 수 있다.

그래서 부수효과가 있는 함수들은 여러번 실행되어 문제가 생기지 않게 `io!` 매크로로 감싸주면 좋다.

```clojure
(def counter (ref 0))

(defn inc-with-io [v]
  (io!
    (println "This is side effect.")
    (inc v)))

(defn new-thread []
  (Thread.
    (fn []
      (dosync
        (alter counter inc-with-io)))))

(defn run []
  (dotimes [_ 100]
    (.start (new-thread))))
```

```bash
user=> (run)
Exception in thread "Thread-2215" java.lang.IllegalStateException: I/O in transaction
	at concurrency.core$inc_with_io.invoke(core.clj:6)
  ...
```

## 애텀

## 에이전트
