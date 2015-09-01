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

## 매크로로 하게 하자

매크로를 만드는 구문은 `defmacro`로 `defn`과 비슷하게 생겼다.

매크로로 `unless`을 다시 만들어보자.

```clojure
(defmacro unless [test then else]
  (if test
    else
    then))
```

그리고 실행해 보자.

```clojure
user=> (unless true (true-clause) (false-clause))
false-clause
7
user=> (unless false (true-clause) (false-clause))
true-clause
3
```

예상대로 잘 동작한다.

`unless` 코드를 보면 바뀐 것은 `defn`이 `defmacro`로 바뀐것 뿐이다.

하지만 동작은 달라졌다. 그럼 `defmacro`를 썻을 때 뭐가 달라지는지 알아보자.

## 매크로의 동작

생긴게 함수랑 비슷하게 생겨서 함수랑 차이점에 대해서 설명하면 조금 쉬울 것 같다.

### 매크로 인자 값

먼저 함수랑 가장 다른 점은 매크로에 넘기는 파라미터는 평가되지 않은 값이 넘어간다는 점이다.

아래 예를 보면 확인 해보자.

```clojure
(defn fn-test [a]
  (println "a:" a)
  (println "a type:" (class a))
  a)

(defmacro macro-test [a]
  (println "a:" a)
  (println "a type:" (class a))
  a)
```

파라미터 하나를 받고 값을 출력하고 타입을 출력한뒤 파라미터 값을 그대로 리턴하는 `fn-test` 함수와 `macro-test` 매크로를 만들었다.

위 함수와 매크로를 각각 `1`과 `"a"`라는 파라미터로 실행해보자.

```clojure
user=> (fn-test 1)
a: 1
a type: java.lang.Long
1
user=> (macro-test 1)
a: 1
a type: java.lang.Long
1
user=> (fn-test "a")
a: a
a type: java.lang.String
"a"
user=> (macro-test "a")
a: a
a type: java.lang.String
"a"
```

똑같다. 그럼 다음에는 `1`값은 `id`라는 이름에 연결한뒤에 `id`값을 넘겨보자.

```clojure
user=> (def id 1)
#'user/id
user=> (fn-test id)
a: 1
a type: java.lang.Long
1
user=> (macro-test id)
a: id
a type: clojure.lang.Symbol
1
```

뭔가 다르다. 함수는 먼저와 같은데 매크로로 실행한 경우는 결과는 잘 나왔는데 출력된 내용이 다르다.

매크로 코드를 다시보자.

```clojure
(defmacro macro-test [a]
  (println "a:" a)
  (println "a type:" (class a))
  a)
```

`a`를 출력했는데 `id`라는 글자가 찍혔고 그 글자는 타입은 심볼이다.

하나 예를 더 보자. 이번에는 파라미터로 `(+ 2 3)`을 넘겨보자.

```clojure
user=> (fn-test (+ 2 3))
a: 5
a type: java.lang.Long
5
user=> (macro-test (+ 2 3))
a: (+ 2 3)
a type: clojure.lang.PersistentList
5
```

이번에도 함수는 `2`와 `3`을 더한 `5`가 넘어갔고 타입은 숫자다.

하지만 매크로는 넘어온 파라미터가 `(+ 2 3)`이고 타입은 리스트다.

클로저 코드는 리스트이기 때문에 `+`, `2`, `3`이 들어있는 리스타가 넘어왔다.

결국 매크로는 파라미터가 평가되지 않고 그대로 넘겨진다.

그럼 결과들은 왜 잘 나온걸까?

그냥 값을 넘겼을 때 매크로의 리턴 값은 값이기 때문에 잘 나왔는데

`id`나 `(+ 2 3)`을 넘겼을 때는 리턴 값이 `id`나 `(+ 2 3)`인데 왜 잘 나온 걸까?

매크로의 리턴 값이 평가됬기 때문이다. 

먼저 리턴 값이 `id` 심볼인 경우 당연히 `id` 심볼이 코드에 있는 것과 같기 때문에 `id`를 평가한 결과인 `1`이 나왔다.

마찬가지로 `(+ 2 3)` 리스트가 리턴되었고 이것을 평가하면 첫번째 항목이 함수이기 때문에 결과인 `5`가 나온것이다.

```clojure
user=> 1
1
user=> id
1
user=> (+ 2 3)
5
```

함수와 두번째로 다른 점은 매크로의 실행 시점이다. 이걸 알면 왜 매크로의 리턴 값이 평가되었는지 알 수 있다.

### 매크로는 언제 실행되나?







