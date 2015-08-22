# Vars

클로저도 다른 언어처럼 이름으로 값을 참조할 수 있다.

클로저는 심볼과 값을 직접 연결하지 않고 Var라는 것을 만들어 심볼과 값을 연결한다.

## Var 만들기

`def` 구문으로 Var를 만들 수 있다. 

```clojure
user=> (def a 1)
#'user/a
user=> a
1
```

간단한 `def` 구문은 `(def 심볼 값)` 형식을 가진다.

클로저는 심볼을 만나면 먼저 Var로 연결된 값을 찾는다. 만약 값이 연결되지 않은 심볼을 만나면 에러가 발생한다.

```clojure
user=> b

CompilerException java.lang.RuntimeException: Unable to resolve symbol: b in this context, compiling:(NO_SOURCE_PATH:0:0) 
```

같은 심볼에 새로운 값으로 Var을 만들면 심볼은 새로운 Var를 가리킨다.

```clojure
user=> (def a 1)
#'user/a
user=> a
1
user=> (def a 2)
#'user/a
user=> a
2
```

## 함수값과 Var

함수값도 Var를 만들어 심볼로 참조할 수 있다.

```clojure
user=> (def add (fn [x y] (+ x y)))
#'user/add
user=> (add 1 2)
3
```

함수값을 Var로 연결하는 일은 자주 일어나기 때문에 `defn` 구문을 쓰면 코드가 간단해진다.

```clojure
user=> (defn add [x y] (+ x y))
#'user/add
user=> (add 1 2)
3
```

## Docstrings

Var는 연결된 값에 대한 설명을 넣을 수 있는 기능이 있다.

```clojure
user=> (def a "A value." 1)
#'user/a
```

심볼 뒤에 문자열로 설명을 넣으면 된다.

Var 설명을 보려면 `doc` 함수로 볼 수 있다.

```clojure
user=> (doc a)
-------------------------
user/a
  A value.
nil
```

함수값도 역시 설명을 넣을 수 있다. 

```clojure
user=> (def add "Add function" (fn [x y] (+ x y)))
#'user/add
user=> (doc add)
-------------------------
user/add
  Add function
nil
```

클로저 제공하는 함수는 docstring을 가지고 있기 때문에 사용법등을 보려면 `doc` 함수에 심볼을 넘기면 된다.

```clojure
user=> (doc +)
-------------------------
clojure.core/+
([] [x] [x y] [x y & more])
  Returns the sum of nums. (+) returns 0. Does not auto-promote
  longs, will throw on overflow. See also: +'
nil
```

`defn` 구문도 같은 형식으로 docstring을 넣을 수 있다. 

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

## let

Var의 범위는 전역 스코프를 가진다.

```clojure
user=> (def x 1)
#'user/x
user=> (def y 2)
#'user/y
user=> (defn add [] (+ x y))
#'user/add
user=> (add)
3
```

만약 지역적으로 사용될 이름이 필요하다면 `let`구문을 쓰면 된다. 

```clojure
user=> (def a 1)
user=> (let [b 2 c 3] (+ b c))
5
user=> b

CompilerException java.lang.RuntimeException: Unable to resolve symbol: b in this context, compiling:(NO_SOURCE_PATH:0:
user=> c

CompilerException java.lang.RuntimeException: Unable to resolve symbol: c in this context, compiling:(NO_SOURCE_PATH:0:0)
```

`let` 구문은 `(let 바인딩벡터 본문)` 형식으로 작성한다.

바인딩 벡터 형식은 `[심볼 값 심볼 값 ...]` 으로 심볼 뒤에 나오는 값과 심볼이 연결된다. 따라서 벡터가 홀수개의 항목을 가질 수 없다.

Var 심볼과 같은 이름의 심볼을 `let` 안에서 사용하면 Var 심볼은 가려져서 `let` 안에서 사용할 수 없다. 하지만 `let` 바깥에서는 여전히 Var 심볼을 사용할 수 있다.

```clojure
user=> (def x 1)
#'user/x
user=> (def y 2)
#'user/y
user=> (defn add [] (let [x 3 y 4] (+ x y)))
#'user/add
user=> (add)
7
user=> x
1
user=> y
2
```

함수 파라미터 벡터에 있는 심볼도 Var가 생성되지 않고 지역적인 스코프를 가지는 연결을 만든다.

```clojure






