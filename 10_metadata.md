# 메타데이터 - 정리중

메타데이터는 값에 영향을 주지 않으면서 값에 부가적인 정보를 담을 수 있는 기능이다.

자바에 있는 어노테이션과 같은 목적의 기능이지만 어노테이션 보다 더 많은 데이터를 담을 수 있기 때문에 다양한 방법으로 활용할 수 있다.

클로저는 Var와 컬랙션 데이터에 메타데이터를 담을 수 있다.

## 메타데이터 담기

메타데이터는 맵 형태다. 메타데이터를 추가하려면 값 앞에 `^맵`을 주면 된다.

다음 예제를 보자.

```clojure
user=> (def numbers ^{:sorted true} [1 2 3])
#'user/numbe
user=> numbers
[1 2 3]
```

위 예제에서 `[1 2 3]` 벡터 값에 `{:sorted true}` 맵 형식의 메타데이터를 가지는 값을 `numbers`에 바인딩 했다.

그리고 값을 평가해보니 `[1 2 3]` 값이 그대로였다.

## 메타데이터 가져오기

`meta`로 메타데이터를 가져올 수 있다.

```clojure
user=> (meta numbers)
{:sorted true}
```

이렇게 `meta`로 메타데이터를 가져올 수 있기 때문에 값에 영향을 주지 않는 다른 동작을 구현할 수 있다. 

```clojure
(defn max-value [v]
  (if (:sorted (meta v))
    (last v)
    (last (sort v))))
```

`v`값 메타데이터에 `:sorted`값이 `true`라면 그냥 마지막 항목을 가장 큰 값이라고 생각하고 리턴하고 아니면 정렬 후에 마지막 값을 리턴하는 함수다.

이해를 돕기위한 예제로 만든것이니 실제로 이렇게 쓰려고 하지말자.

동작을 확인해보면 아래와 같다.

```bash
user=> (max-value [1 9 3 2 3 4 5])
9
user=> (def value ^{:sorted true} [2 3 4 5 9 3 1])
#'user/value
user=> (max-value value)
1
```

마지막에 `value`는 메타 데이터에 `sorted`를 `true`로 주고 함수를 실행해서 그냥 마지막 값인 `1`이 나왔다.

# Var 메타데이터

Var도 메타데이터를 담을 수 있다. 생성된 Var는 기본적으로 메타데이터를 포함하고 있다. 

아래와 같이 확인해보자.

```clojure
(ns metadata-sample.core)

(defn foo
  "I don't do a whole lot."
  [x]
  (println x "Hello, World!"))
```

```bash
metadata-sample.core=> (meta (var foo))
{:arglists ([x]), 
 :doc "I don't do a whole lot.", 
 :line 3, 
 :column 1, 
 :file ".../metadata-sample/src/metadata_sample/core.clj", 
 :name foo, 
 :ns #object[clojure.lang.Namespace 0x4c0a4063 "metadata-sample.core"]}
```

위의 예제는 `foo`라는 심볼에 연결된 함수에 대한 Var의 메타데이터를 가져온 결과이다.

메타데이터에 포함된 내용을 보면 함수의 인자 목록, 문서, 함수가 포함된 파일 경로, 라인, 컬럼, 함수명, 네임스페이스등의 정보를 가지고 있다.

이 메타데이터를 활용하는 예제를 만들어 보자.

클로저에는 함수를 파라미터 벡터로 실행할 수 있는 `apply`라는 함수가 있다.

```clojure
(defn add [x y]
  (+ x y))
```

```bash
user=> (apply add 1 2)
3
```

만약 인자의 개수가 다르면 아래와 같이 예외가 발생한다.

```bash
user=> (add 1)
ArityException Wrong number of args (1) passed to: calc/add  clojure.lang.AFn.throwArity (AFn.java:429)
```

아래는 Var에 있는 기본 메타데이터를 이용해서 인자 갯수를 확인해서 같을 때만 실행하고 다를 때는 `nil`을 리턴하는 `apply!` 매크로다.


```clojure
(defmacro apply! [fn-name & args]
  `(let [arglist# (first (:arglists (meta (var ~fn-name))))]
     (when (= (count ~(vec args)) (count arglist#))
       (~fn-name ~@args))))
```

```bash
user=> (add 1 2)
3
user=> (add 1)
nil
```

예제를 살펴보면 첫번째 `let` 구문에서 함수 이름(심볼)에 대한 Var를 `var`함수를 통해 가져와서 `meta` 함수로 Var에 대한 메타데이터를 가져와서 그중 `:arglists`키로 인자 목록을 가져온다.

여기서 `first`가 적용되어 있는데 함수는 오버로딩 될수 있기 때문에 시그네쳐(인자가 다른것)가 여러개 있을 수 있다. 위 예제는 그냥 `first`라고 해서 첫번째 시그네쳐만 가져오도록 되어있어 오버로딩된 함수는 정상적으로 동작하지 않는 문제가 있다. 이 부분은 과제로 남겨둔다.

최종적으로 `args`의 개수와 `arglist`의 개수를 비교해 같은 경우만 함수를 실행하도록 하는 매크로다.



