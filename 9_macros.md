# 매크로 - 정리중

매크로는 코드를 생성하는 기능이다.

클로저는 함수로 코드를 추상화 하는데 함수로 할 수 없는 일들은 매크로로 추상화 할 수 있다.

하지만 대부분의 추상화는 함수로 가능하기 때문에 매크로는 꼭 필요한 부분에만 사용하는 것이 좋다.

## 함수로 할 수 없는 일

클로저에는 이미 `if` 구문과 반대로 동작하는 `if-not`이라는 구문이있다.

하지만 `if-not`과 같은 `unless`를 연습삼아 만들어보자.

`unless` 함수는 첫번째 인자로 `true` 또는 `false`를 받는다.

두번째 인자로는 첫번째 인자가 `false`일 때 결과가 될 구문을 받는다.

세번째 인자로는 첫번째 인자가 `true`일 때 결과가 될 구문을 받는다.

이런 함수는 아래와 같이 만들 수 있다.

```clojure
(defn unless [test then else]
  (if test
    else
    then))
```

실행해보면 아래와 같이 잘 동작한다. 

```clojure
user=> (unless true 1 2)
2
user=> (unless false 1 2)
1
```

위의 코드는 논리적으로 `unless`문을 잘 만들었다.

하지만 문제가 있다.

다음 코드를 보자

```clojure
(defn true-clause []
  (println "true-clause")
  (+ 1 2))
  
(defn false-clause []
  (println "false-clause")
  (+ 3 4))
```

```clojure
user=> (unless true (true-clause) (false-clause))
true-clause
false-clause
7
```

먼가 이상하다. 두번째 인자가 `true`기 때문에 `(false-clause)` 구문의 결과인 `7`이 잘 나왔다. 

그런데 `(true-clause)` 구문도 실행이 되었다.

이렇게 동작하는 이유는 클로저에서 함수 안에 있는 구문들을 먼저 실행하기 때문에 그렇다.

위 예제 처럼 부수 효과가 발생하는 코드가 있다면 의도하지 않게 `(true-clause)`가 실행되어 문제가 발생할 수 있다.

또 조건에 따른 재귀 함수가 있다면 문제가 될 수도 있다.

그래서 클로저에서 함수로는 실용적인 `unless` 함수를 만들 수 없다.

## 매크로

매크로를 만드는 구문은 `defmacro`로 `defn`과 비슷하게 생겼다.

매크로로 `unless`을 다시 만들어보자.

```clojure


```

함수와 거의 비슷하게 생겼다.







