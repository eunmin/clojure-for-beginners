# 병렬 처리에서 데이터 공유

클로저에서 쓰레드간에 하나의 값을 바꾼다면 어떻게 해야할까?
클로저에서 값을 변경하는 방법에 대해 다루지 않았기 때문에 각 쓰레드는 변경된 값을 새로 바인딩 하는 방법으로
데이터를 공유 할 수 있을 것이다.
루트 바인딩을 바꾸는 함수는 `alter-root-var`이다. 이 함수를 이용해서 여러 쓰레드가 `counter`를
증가 시키는 예제를 만들어 보자.

```clojure
(def counter 0)

(defn new-thread []
  (Thread.
    (fn []
      (alter-var-root (var counter) inc))))

(defn run []
  (dotimes [_ 100]
    (.start (new-thread))))

(run)

;; => counter
;; 100
```

`dotimes`라는 나왔는데 반복 횟수와 인덱스를 받아 구문을 실행해주는 매크로다. 절차형 언어의 반복문과 비슷하다.
`_`를 사용한 이유는 인덱스를 따로 바인딩하지 않겠다는 뜻이다.

예제는 예상대로 잘 동작하고 카운트도 동기화 문제 없이 100이 나왔다.
`alter-root-var`가 동기화에 문제가 생기지 않는 이유는 `alter-root-var`가 동기화 함수이기 때문이다.
(https://github.com/clojure/clojure/blob/master/src/jvm/clojure/lang/Var.java#L302)

클로저에는 쓰레드간 데이터 공유를 위해 새로운 값을 다시 바인딩하는 방법말고 실제 값을 바꿀 수 있는 몇 가지 기능을
제공한다.

가장 많이 사용하는 atom과 트랜젝션을 제공하는 ref와 agent가 있다.
하나씩 알아보자.

## atom

atom은 값을 바꿀 수 있는 가장 간단한 기능이다. atom 값을 만드는 것은 다음과 같다.

```clojure
(def counter (atom 0))
```

그리고 값을 읽는 방법은 `deref`를 사용하거나 `@` 구문을 사용하면 된다.

```bash
user=> (deref counter)
0
user=> @counter
0
```

값을 변경하려면 `reset!`을 하면 된다.

```clojure
user=> (reset! counter 1)
1
user=> @counter
1
```

값을 변경하는 다른 방법으로 클로저 `update` 함수처럼 이전 값을 받고 새로운 값을 리턴하는 방법도 있다.
`swap!`이라는 함수가 그런 일을 해준다.

```clojure
user=> (swap! atom-counter (fn [old-value] (+ 100 old-value)))
101
user=> @atom-counter
101
```

`atom`은 내부적으로 `java.util.concurrent.atomic.AtomicReference`를 사용하기 때문에 여러 쓰레드에서
변경해도 문제 없다. counter 예제를 `atom`으로 만들어 보자.

```clojure
(def atom-counter (atom 0))

(defn new-thread []
  (Thread.
    (fn []
      (swap! atom-counter inc))))

(defn run []
  (dotimes [_ 100]
    (.start (new-thread))))
;; (run)
;; => nil
;; @atom-counter
;; => 100
```

## ref

`atom`은 하나의 값을 한번에 변경할 때 사용한다. 만약 여러 값의 변경을 하나의 트랜잭션으로 묶어 하나의 변경처럼
다루고 싶다면 레퍼런스를 사용하면 된다. 레퍼런스를 만들고 읽는 것은 다음과 같다.

```clojure
(def ref-counter (ref 0))

;; => @ref-counter
0
```

레퍼런스의 값을 바꾸는 함수는 `ref-set` 구문 이다. `ref-set`은 그냥 사용할 수 없고 `dosync` 매크로로
감싸야한다. `dosync`는 안쪽에 있는 작업에 대해 트랜잭션을 보장해준다.

```clojure
user=> (def ref-counter (ref 0))
#'user/ref-counter
user=> (ref-set ref-counter 1)
IllegalStateException No transaction running  clojure.lang.LockingTransaction.getEx (LockingTransaction.java:208)
user=> (dosync (ref-set ref-counter 1))
1
user=> @ref-counter
1
```

레퍼런스 역시 `atom`의 `swap!` 함수처럼 값을 읽으면서 변경할 수 있는 `alter` 함수를 제공한다.

```clojure
user=> (def ref-counter (ref 0))
#'user/ref-counter
user=> (dosync (alter ref-counter inc))
1
```

`dosync`의 트랜잭션 방식은 락 방식이 아니고 버전 충돌이 났을 때 다시 실행하는 MVCC 방식이다.
따라서 `dosync`안에 있는 구문들은 여러번 실행 될 수 있다는 점을 항상 인지하고 있어야 한다.

다음은 `ref`로 만든 counter 예제다.

```clojure
(def ref-counter (ref 0))

(defn new-thread []
  (Thread.
    (fn []
      (dosync
        (alter ref-counter inc)))))

(defn run []
  (dotimes [_ 100]
    (.start (new-thread))))

;; (run)
;; => nil
;; @ref-counter
;; => 100
```

잘 동작한다. `dosync` 트랜잭션이 MVCC 방식으로 실행되는지 확인하기 위해서 `dosync` 안에서 `ref-counter` 값을
출력해보자.

```clojure
(defn new-thread []
  (Thread.
    (fn []
      (dosync
        (println @ref-counter)
        (alter ref-counter inc)))))
;; (run)
;; 1
;; 2
;; 2
;; 3
;; 3
;; ...
```

같은 `ref-counter`가 여러번 출력되는 것을 볼 수 있고 출력된 횟수도 `dotimes`에 정한 100번 이상 출력되었다.
따라서 부수 효과가 있는 함수가 `dosync`안에 있다면 예상하지 못한 문제가 발생할 수 있다.
이런 실수를 막기 위해 `io!`라는 매크로로 부수효과가 있는 부분을 감싸주면 `dosync`를 사용할 때 예외가 발생하게 할 수 있다.

```clojure
(def counter (ref 0))

(defn inc-with-io [v]
  (io!
    (println "This is side effect.")
    (inc v)))

;; (dosync (alter counter inc-with-io))
;; IllegalStateException I/O in transaction  mutation.core/inc-with-io (form-init8088134157352085595.clj:23)
```

버전 충돌로 재시작이 많이 되면 성능에 문제가 있을 수 있기 때문에 실행 순서가 보장되지 않아도 된다면 `alter` 대신 `commute`를 사용할 수 있다.
하지만 특성을 잘 알고 사용해야 문제가 없다.

## agent

`agent`는 값 변경을 비동기로 수행한다. `agent`를 만들고 값을 읽는 방법은 `atom`이나 `ref`와 같다.

```bash
user=> (def agent-counter (agent 0))
#'user/agent-counter
user=> @agent-counter
0
```

`agent` 값을 바꾸는 함수는 `send`다.

```bash
user=> (send agent-counter inc)
#object[clojure.lang.Agent 0x3b53811b {:status :ready, :val 1}]
user=> @agent-counter
1
```

`send` 함수는 비동기로 변경 작업을 수행하기 때문에 변경된 결과 값을 리턴하지 않고 `agent`값 자체를 리턴한다.
리턴된 `agent` 값을 바로 출력해보면 변경되지 않은 것을 확인할 수 있다.

```clojure
user=> (def agent-counter (agent 0))
#'user/agent-counter
user=> @(send agent-counter inc)
0
user=> @agent-counter
1
```

`agent`값은 값이 변경될 때까지 블럭 상태로 기다릴 수 있는 `await` 함수를 사용해 동기화 할 수 있다.

```clojure
(defn syncronized-send [v f]
  (await (send v f))
  @v)

;; @agent-counter
;; => 0
;; (syncronized-send agent-counter inc)
;; => 1
```
