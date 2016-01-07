# 함수

클로저의 함수는 값이다.

## 함수 부르기

함수를 부르는 방법은 `(함수값 파라미터 파라미터 파라미터...)`과 같은 방법으로 부른다.

리스트 첫번째 항목에 함수값과 나머지 항목에 파라미터를 넣으면 코드가 평가될때 함수로 실행된다.

함수를 부르면 결과 값이 나온다.

```clojure
user=> (str "Hello" " " "World")
"Hello World"
user=> (+ 1 2)
3
```

함수 안에 함수를 부를 수도 있다. 클로저는 내부적으로 중첩된 괄호가 있을 때 안쪽 부터 평가된다. 하지만 논리적으로 코드는 실행 순서가 없기 때문에 안쪽 부터 하던지 바깥쪽 부터 하던지 결과는 같다.

```clojure
user=> (+ (+ 1 2) 3)
6
```

## 함수 만들기

새로운 함수 값을 만드는 방법은 `fn`이라는 구문으로 만든다.

```clojure
user=> (fn [x y] (+ x y))
#<user$eval14994$fn__14995 user$eval14994$fn__14995@43bed306>
```

함수를 만드는 간단한 형태는 리스트 첫번째 항목에 `fn` 구문과 두번째 항목에 벡터 형식의 파라미터 목록, 나머지 항목에 실행할 본문을 넣어 주면 된다.

그리고 마지막 실행한 내용이 함수의 리턴 값이 된다.

위의 예제는 두 개의 파라미터를 받아 `+` 함수를 실행한 결과를 리턴하는 함수다.

아직 값에 이름을 붙이는 방법을 설명하지 않았기 때문에 함수값에 이름이 없다.

그래서 실행하려면 함수 값을 리스트 첫번째 항목에 넣고 실행한다.

```clojure
user=> ((fn [x y] (+ x y)) 1 2)
3
```

함수의 두번째 파라미터인 `[x y]` 백터는 `x`와 `y` 심볼이 들어 있다.

`x`와 `y`는 함수가 실행될 때 파라미터 값에 순서대로 연결되어 함수 본문에서 쓸 수 있다.

파라미터가 없는 함수는 빈 벡터를 넣어주면 된다.

```clojure
user=> (fn [] nil)
#<user$eval15002$fn__15003 user$eval15002$fn__15003@ac491e8>
user=> ((fn [] nil))
nil
```

위 함수는 `nil`을 리턴하는 함수다. 파라미터가 없기 때문에 리스트 첫번째 항목에 함수 값 만 넣어주면 실행된다.

함수를 간단히 만들 수 있는 `#()` 구문이 있다.

`#()` 구문은 `fn`과 파라미터 벡터를 생략하고 함수 본문을 바로 쓸 수 있다. 파라미터는 하나일 때 `%`, 두 개 이상일 때 첫번째 파라미터는 `%1`, 두번째 파라미터는 `%2`와 같이 쓴다.

```clojure
user=> #(+ %1 %2)
#<user$eval15014$fn__15015 user$eval15014$fn__15015@6aea0e5c>
user=> (#(+ %1 %2) 1 2)
3
user=> (#(println %) "Hello World")
Hello World
nil
```

## 함수값과 Var

함수값을 Var로 연결하는 일은 자주 일어나기 때문에 `defn` 구문을 쓰면 코드가 간단해진다.

```clojure
user=> (defn add [x y] (+ x y))
#'user/add
user=> (add 1 2)
3
```

`defn` 구문도 docstring을 넣을 수 있다. 

```clojure
user=> (defn add "Add function" [x y] (+ x y))
#'user/add
user=> (doc add)
-------------------------
user/add
([x y])
  Add function
nil
```

## 함수 파라미터의 스코프

함수 파라미터 벡터에 있는 심볼도 Var가 생성되지 않고 지역적인 스코프를 가지는 연결을 만든다.

```clojure
user=> (defn add [x y] (+ x y))
#'user/add
user=> (add 1 2)
3
user=> x

CompilerException java.lang.RuntimeException: Unable to resolve symbol: x in this context, compiling:(NO_SOURCE_PATH:0:0) 
user=> y

CompilerException java.lang.RuntimeException: Unable to resolve symbol: y in this context, compiling:(NO_SOURCE_PATH:0:0) 
```

파라미터 심볼과 같은 이름의 심볼이 `let` 구문에 연결되었다면 파라미터 심볼도 가려진다.

```clojure
user=> (defn add [x y] (let [x 0 y 0] (+ x y)))
#'user/add
user=> (add 1 2)
0
```

## 값으로 동작하는 함수

함수는 값이기 때문에 함수에 파라미터로 전달 할 수 있다.

```clojure
user=> (defn apply-one-two [f] (f 1 2))
#'user/apply-one-two
user=> (defn add [x y] (+ x y))
#'user/add
user=> (apply-one-two add)
3
```

위의 예제는 어떤 함수인지 모르지만 함수를 하나 받아 1과 2를 넘겨 실행하는 `apply-one-two` 함수를 만들었다.

그리고 `x`와 `y`를 받아 더해주는 `add`도 만들었다.

최종적으로 `apply-one-two` 함수에 `add`를 넘겨 결과를 출력해줬다.

함수는 값이기 때문에 리턴 값으로 사용할 수 있다.

```clojure
user=> (defn increase [x] (+ x 1))
#'user/increase
user=> (defn get-increase-function [] increase)
#'user/get-increase-function
user=> ((get-increase-function) 1)
2
```

위 예제는 파라미터 하나를 받아 1을 더하는 `increase` 함수와 `incease` 함수를 리턴하는 `get-increase-function`을 만들고 `get-incease-function`를 평가한 결과(`increase` 함수) 에 `1`을 넘겨 `2`라는 결과를 얻었다. 

## Closure

함수에 파라미터 값을 사용하는 함수를 리턴해도 그 파라미터가 유지된다.

```clojure
user=> (defn add [x] (fn [y] (+ x y)))
#'user/add
user=> ((add 1) 2)
3
```

위 예제는 `x`를 인자로 받아 `y`를 인자로 받아 `x`와 `y`를 더하는 함수인 `add` 함수를 만들었다.
 
여기서 `y`값은 일반 함수 처럼 함수가 종료되고 나서 사라지지 않고 결과 함수가 사용되는 곳에서 계속 유지된다.

위 예제는 커링 예제다. 커링이란 파라미터를 여러개 받을 수 있는 함수만드는 방법이다.

## 가변 인자를 받는 함수

가변 인자를 받는 함수를 만들 때는 마지막 인자 앞에 `&`을 붙여서 만든다. 

가변 인자로 사용된 마지막 인자는 벡터로 넘어온다. 넘기지 않는다면 `nil`이 넘어온다.

```clojure
user=> (defn get-ys [x & y] y)
#'user/get-ys
user=> (get-ys 1)
nil
user=> (get-ys 1 2)
(2)
user=> (get-ys 1 2 3)
(2 3)
```

## Overloading

가변인자 함수는 인자 개수가 명시적이지 않기 때문에 정해진 갯수의 인자를 제공하는 함수의 경우에는 함수 오버로딩을 사용하는 것이 좋다.

```clojure
user=> (defn sum ([x] x) ([x y] (+ x y)))
#'user/sum
user=> (sum 1)
1
user=> (sum 1 2)
3
user=> (sum 1 2 3)

ArityException Wrong number of args (3) passed to: user/sum  clojure.lang.AFn.throwArity (AFn.java:429)
```



