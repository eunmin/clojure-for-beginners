# 매크로

매크로는 코드가 실행되기 전에 코드를 만드는 기능이다.

클로저는 함수로 코드를 추상화 하는데 함수로 할 수 없는 일들은 매크로로 추상화 할 수 있다.

하지만 대부분의 추상화는 함수로 가능하기 때문에 매크로는 꼭 필요한 부분에만 사용하는 것이 좋다.

## 함수로 할 수 없는 일

클로저에는 이미 `if` 구문과 반대로 동작하는 `if-not`이라는 구문이있다.

하지만 연습삼아 `if-not`과 똑같고 이름만 다른 `unless`를 만들어보자.

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

먼가 이상하다. 두번째 인자가 `true`기 때문에 `(false-clause)` 구문의 결과 `7`이 잘 나온다.

하지만 `(true-clause)` 구문도 실행 되었다.

이렇게 동작하는 이유는 클로저에서 함수 안에 있는 구문들을 평가해 실행하기 때문이다.

위 예제 처럼 부수 효과가 발생하는 코드가 있다면 의도하지 않게 `(true-clause)`가 실행되어 문제가 발생할 수 있다.

또 조건에 따른 재귀 함수가 있다면 재대로 동작하지 않을 것이다.

따라서 클로저 함수로는 실용적인 `unless` 함수를 만들 수 없다.

## 매크로로 unless를 만들자

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

하지만 동작은 달라졌다. 그럼 `defmacro`를 쓸 때 뭐가 달라지는지 알아보자.

## 매크로의 동작

생긴게 함수랑 비슷하게 생겨서 함수에 비교해서 설명하면 조금 쉬울 것 같다.

### 매크로 인자 값

먼저 함수랑 가장 다른 점은 매크로에 넘기는 파라미터가 평가되지 않는 다는 점이다.

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

파라미터 하나를 받고 값과 타입을 출력한뒤 파라미터 값을 그대로 리턴하는 `fn-test` 함수와 `macro-test` 매크로를 만들었다.

위에서 만든 함수와 매크로를 각각 `1`과 `"a"`라는 파라미터로 실행해보자.

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

똑같다. 다음에는 `1`값을 `id` 이름에 연결해서 Var를 만들어 `id`값을 넘겨보자.

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

다르다. 함수는 먼저와 같은데 매크로로 실행한 경우는 결과는 잘 나왔는데 출력된 내용이 다르다.

매크로 코드를 다시보자.

```clojure
(defmacro macro-test [a]
  (println "a:" a)
  (println "a type:" (class a))
  a)
```

`a`를 출력했는데 `id` 심볼이 그대로 출력되었고 타입 역시 심볼로 나왔다.

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

클로저 코드는 리스트이기 때문에 `+`, `2`, `3`이 들어있는 리스트가 넘어왔다.

결국 매크로는 파라미터가 평가되지 않고 그대로 넘겨진다.

그럼 결과들은 왜 잘 나온걸까?

매크로는 코드가 실행되기 전에 먼저 실행되서 코드를 만들고 그 다음에 코드가 실행되기 때문이다.

먼저 예제에서 리턴 값이 `id` 심볼인 경우 `id` 코드를 생성하고 `id`를 평가해 `1`이 나왔다. 

마찬가지로 `(+ 2 3)` 리스트가 리턴되었고 이것을 평가하면 첫번째 항목이 함수이기 때문에 결과인 `5`가 나온것이다.

```clojure
user=> 1
1
user=> id
1
user=> (+ 2 3)
5
```

### 매크로는 언제 실행되나?

매크로는 코드가 컴파일되기 전에 실행되어 모든 매크로의 결과가 코드로 바뀐후에 코드가 컴파일 된다.

C언어의 `define`문과 비슷하다고 생각하면 실행시점을 이해하기 쉽다.

`macroexpand-1` 함수를 써서 매크로가 실제 코드로 변환된 결과를 얻을 수 있다.

```clojure
user=> (macroexpand-1 '(macro-test (+ 1 2)))
a: (+ 1 2)
a type: clojure.lang.PersistentList
(+ 1 2)
```

위에 보는 것처럼 실제로 `(macro-test (+ 1 2))` 구문은 

```cloure
a: (+ 1 2)
a type: clojure.lang.PersistentList
(+ 1 2)
```

위의 코드를 손으로 작성한 것과 같이 동작한다.

`macroexpand-1`을 사용할 때 파라미터로 코드를 넘기는데 `'`를 코드 앞에 붙여 평가되지 않도록 해야 코드 구문 자체가 넘어간다.

평가되면 매크로가 실행된 결과가 넘어가기 때문에 매크로가 코드로 바뀐 결과를 재대로 확인 할수 없다.

## 매크로로 코드 만들기

위에서 알아 본 것 처럼 매크로는 코드가 실행되기 전에 모두 실행되어 결과가 코드로 바뀌기 때문에 패턴화 되는 코드를 만드는데 사용할 수 있다.

하지만 처음에 이야기한것처럼 함수로 작성할 수 있다면 매크로보다 함수로 작성하는 것이 더 좋다.

`(str "Hi! I'm " 입력 값)` 형태의 코드를 만드는 매크로를 만들어 보자.

`"eunmin"` 대신에 매크로에 파라미터를 받게 만들어보자.

```clojure
user=> (defmacro code-greeting [me]
  #_=>   (list str "Hi! I'm " me))
user=> (macroexpand-1 '(code-greeting "eunmin"))
(#<core$str clojure.core$str@784edff5> "Hi! I'm " "eunmin")
```

`(code-greeting "eunmin")` 매크로의 실행결과는 컴파일되기 전에 `(str "Hi! I'm" "eunmin")` 코드로 대치되고 그대음 코드가 평가되서 "Hi! I'm eunmin"이라는 결과가 나온다.

위 예에서 매크로의 결과가 코드여야 하기 때문에 리스트에 코드를 담았다.

리스트에 담지 않고 `(str "Hi! I'm" me)`라고 했다면 매크로의 결과가 코드 형태가 아닌 `str`의 결과 값이 된다.

## 매크로 템플릿

매크로가 코드를 리턴하게 하는 또 다른 방법은 `'`로 괄호 안에 있는 구문을 평가하지 않도록 하는 것이다.

`(+ 1 2)`라는 코드를 리턴하는 매크로를 `list`와 `'`표시로 만들어보자.

```clojure
(defmacro code-list-one-plus-two []
  (list + 1 2))
  
(defmacro code-quote-one-plus-two []
  '(+ 1 2))
```

```bash
user=> (macroexpand-1 '(code-list-one-plus-two))
(#<core$_PLUS_ clojure.core$_PLUS_@542aa3b8> 1 2)

user=> (macroexpand-1 '(code-quote-one-plus-two))
(+ 1 2)
```

둘 중에 `'`표시로 만든 매크로가 더 간단해 보인다. 

만들어진 코드의 모양도 `list`로 만든 경우 `+`함수가 평가된 함수 값이 들어 있고 

`'`표시로 만든 경우 `+` 평가되지 않고 `+` 심볼이 그대로 나온것을 볼 수 있다.

만약 `list`로 만들면서 `+`가 평가되지 않게 하려면 `+` 앞에만 `'`를 붙여주면 된다.

```clojure
(defmacro code-list-one-plus-two []
  (list '+ 1 2))
```

```bash
user=> (macroexpand-1 '(code-list-one-plus-two))
(+ 1 2)
```

이번에는 `'`표시로 앞서 만든 `code-greeting` 매크로를 만들어 보자.

```clojure
(defmacro code-greeting [me]
  '(str "Hi! I'm " me))
```

```bash
user=> (macroexpand-1 '(code-greeting "eunmin"))
(str "Hi! I'm " me)
user=> (code-greeting "eunmin")

CompilerException java.lang.RuntimeException: Unable to resolve symbol: me in this context, compiling:(NO_SOURCE_PATH:2:4) 
user=> me

CompilerException java.lang.RuntimeException: Unable to resolve symbol: me in this context, compiling:(NO_SOURCE_PATH:0:0)
```

매크로의 만든 코드가 `(str "Hi! I'm " "eunmin")` 대신 `(str "Hi! I'm " me)`가 나왔다.

코드를 평가한 결과도 오류가 발생했다. `me`라는 심볼은 현재 네임스페이스인 `user`에 없는 Var이기 때문이다.

왜 이런 일이 발생한 건가?

`'`표시는 안에 있는 모든 코드를 평가하지 않기 때문에 `me`값이 평가되지 않고 심볼 그대로 리턴이됬기 때문이다.

이런 경우 처음에 만든 예제 처럼 `list`를 사용해야한다.

`list`를 사용하면 앞에 붙은 `list` 때문에 코드가 간결해 보이지 않고

또 안쪽에 평가되어야 할 심볼과 그렇지 않은 심볼의 구분이 `'`표시로 되기 때문에 잘 눈에 띄지 않는다.

클로저에 매크로를 템플릿 처럼 사용할 수 잇는 기능을 쓰면 `list`를 쓰지 않고 보기 좋게 매크로를 작성할 수 있다.

`'`표시와 비슷하지만 살짝 다른 syntax-quote라고 부르는 ```표시를 코드 앞에 해주면 된다.

```표시로 `code-greeting`을 만들어 보자.

```clojure
(defmacro code-greeting [me]
  `(str "Hi! I'm " me))
```

```bash
user=> (macroexpand-1 '(code-greeting "eunmin"))
(clojure.core/str "Hi! I'm " user/me)
user=> (code-greeting "eunmin")

CompilerException java.lang.RuntimeException: No such var: user/me, compiling:(NO_SOURCE_PATH:1:1) 
```

이번에도 `me`는 `"eunmin"`으로 평가되지 않았다.

마찬가지로 `me`를 현재 네임스페이스인 `user`에 없기 때문에 오류가 발생한다.

조금 다른 것은 `str`과 `me`와 같은 심볼 앞에 심볼이 있는 네임스페이스를 붙여 `clojure.core/str`, `user/me`와 같이 생성된 점이다. 

```표시 안에 있는 심볼을 평가해 값으로 바꿀 수 있는 `~`표시가 있다.

심볼 앞에 `~`표시를 붙이면 평가되어 값으로 바뀐다.

그럼 동작하도록 다시 만들어 보자.

```clojure
(defmacro code-greeting [me]
  `(str "Hi! I'm " ~me))
```

```bash
user=> (macroexpand-1 '(code-greeting "eunmin"))
(clojure.core/str "Hi! I'm " "eunmin")
user=> (code-greeting "eunmin")
"Hi! I'm eunmin"
```

이제 `me`심볼이 값으로 바뀐 코드가 생성되었다.

`list`로 만들었을 때는 안쪽의 코드가 기본적으로 평가되고 `'`표시를 해서 평가하지 않도록 했고

```표시로 만들었을 때는 안쪽의 코드가 기본적으로 평가되지 않고 `~`표시로 평가하도록 했다.

## 매크로에서 심볼 사용하기

위에서 본 것 처럼 ```표시로 매크로를 만들면 심볼앞에 네임스페이스가 붙는다.

`let`구문에 `a`와 `b`를 만들어 더하는 코드를 생성하는 매크로를 만들어보자.

```clojure
(defmacro code-local-bind-add []
  `(let [a 1 b 2] (+ a b)))
```

```bash
user=> (macroexpand-1 '(code-local-bind-add))
(clojure.core/let [user/a 1 user/b 2] (clojure.core/+ user/a user/b))
user=> (code-local-bind-add)

CompilerException java.lang.RuntimeException: Can't let qualified name: user/a, compiling:(NO_SOURCE_PATH:1:1) 
```

`user` 네임스페이스에서 매크로를 생성했기 때문에 `a`와 `b` 심볼은 `user/a`와 `user/b`로 대치되었다.

하지만 `user` 네임스페이스에 `a`와 `b`가 없기 때문에 오류가 발생했다.

이런 경우에 네임스페이스가 앞에 붙지 않도록 하기 위해 꼼수를 써야한다.

이상하게 보이지만 `a`를 심볼로 생성하기 위해 `'` 평가되지 않는 기호를 붙이고 그 앞에 또 다시 `~` 평가하게 하면 결국 `a` 심볼을 얻을 수 있다.

```clojure
(defmacro code-local-bind-add []
 `(let [~'a 1 ~'b 2] (+ ~'a ~'b)))
```

이런 방법은 코드를 알아보기 힘들다.

매크로 안에 심볼을 만들어 써야하는 경우 `심볼#`이라는 구문을 쓰면 겹치지 않는 심볼을 만들 수 있다.

```clojure
(defmacro code-local-bind-add []
  `(let [a# 1 b# 2] (+ a# b#)))
```

```bash
user=> (macroexpand-1 '(code-local-bind-add))
(clojure.core/let [a__15173__auto__ 1 b__15174__auto__ 2] (clojure.core/+ a__15173__auto__ b__15174__auto__))
user=> (code-local-bind-add)
3
```

매크로가 만들어내는 코드를 보면 심볼 뒤에 자동으로 만들어낸 글자를 붙여 새로운 심볼로 생성하는 것을 볼 수 있다.

## 시퀀스 풀어 대치하기

시퀀스 형태의 값을 순서대로 대치해주는 `~@심볼`라는 기호가 있다. 예를 들어 `[1 2 3]`이라는 값으로 들어오는 매크로의 파라미터가 있다면 `~@심볼`로 풀어서 `1 2 3`으로 대치해줄 수 있다.

```clojure
(defmacro code-fail-add [& args]
  `(+ ~args))
  
(defmacro code-good-add [& args]
  `(+ ~@args))
```

```bash
user=> (macroexpand-1 '(code-fail-add 1 2 3))
(clojure.core/+ (1 2 3))

user=> (macroexpand-1 '(code-good-add 1 2 3))
(clojure.core/+ 1 2 3)
```

## 다시 강조

매크로는 일반 코드가 평가되기 전에 먼저 실행되고 그 결과 코드는 일반 코드들과 함께 실행된다!

함수로 만들 수 있으면 매크로를 쓰지 말자!








