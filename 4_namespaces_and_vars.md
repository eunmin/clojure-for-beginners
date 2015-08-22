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

## Dynamic Vars

`binding` 구문으로 Var 값을 지역적으로 다른 값으로 연결해서 사용할 수 있다.

```clojure
user=> (def ^:dynamic a 1)
#'user/a
user=> (binding [a 2] (+ a 1))
3
user=> a
1
```

동적으로 바인딩 되는 Var는 Var를 만들 때 심볼 앞레 `^:dynamic`이라는 메타데이터를 준다. (메타데이터는 나중에 다룬다.)

클로저 세계에서는 dynamic var를 사용할때 조심하라고 심볼 앞뒤로 귀마게 표시(**)를 붙이는 네이밍 규칙을 쓴다.

```clojure
user=> (def ^:dynamic *a* 1)
#'user/*a*
user=> (binding [*a* 2] (+ *a* 1))
3
user=> *a*
1
```

## 전역 Var를 동적으로 다시 바인딩하기

`binding` 구문은 지역적으로 Var를 다시 연결 하는 기능을 하지만 전역 Var을 동적으로 다시 바인딩 하려면 `alter-var-root` 함수를 사용한다.


```clojure
user=> (def a 1)
#'user/a
user=> a
1
user=> (alter-var-root (var a) (fn [origin-value] 2))
2
user=> a
2
```

`alter-var-root`는 첫번째 인자로 Var를 두번째 인자로 연결될 새로운 값을 리턴하는 함수를 넘겨준다.

위의 예제에서 `var`는 심볼에 연결된 Var를 가져오는 구문이다.

짧게 쓸 수있는 구문도 제공하는데 `#'a`라고 써도 된다. `def`로 Var를 만들었을때 REPL에 출력되던 형식 `#'user/a`가 Var를 참조하는 구문이다.

앞에 `user`가 붙어 있는데 네임스페이스로 따로 다룬다.

클로저는 함수형 프로그래밍 스타일에 따라 대부분 값을 변경되지 않는 스타일을 사용하기 때문에 `alter-var-root`를 변수 처럼 사용하는 일은 없어야한다.

만약 스레드를 사용해 데이터처리를 병렬로 해야한다면 어쩔수 없이 값을 변경하고 공유해야하는 일이 생기는데 이때는 클로저에서 안전하게 값을 변경할 수있도록 제공하는 기능들을 사용하면 된다. (나중에 다룸)

### 덤

`alter-var-root` 예제에서 두번째 함수에 파라미터로는 원래 연결된 값이 넘어온다. 하지만 예제에서  그냥 `2`를 리턴하기 원래 값을 쓰지 않는다.

이때 `_`를 사용해 연결되는 값을 무시하게 할 수 있다.

```clojure
user=> (alter-var-root (var a) (fn [_] 2))
```





