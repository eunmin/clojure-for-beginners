# 클로저 데이터 형식

클로저에도 데이터 형식이 여러가지 있다. 

아래 코드를 테스트 해보기 위해 REPL을 실행한다.
REPL을 그냥 Clojure JAR를 가지고 실행할 수 도 있지만 보통 Leiningen 스크립트를 받았다면 `lein repl` 명령어로 실행할 수 있다. 

```clojure
lein repl
```

## true와 false
다른 언어에서도 볼 수 있는 true와 false로 true false라고 쓴다.

```clojure
user=> true
true
user=> false
false
```

true인지 false인지 확인해주는 함수도 있다.

```clojure
user=> (true? true)
true
user=> (false? false)
true
```

## nil
널 값으로 아무 값도 없는 값을 말한다.

```clojure
user=> nil
nil
```

역시 `nil`인지 확인해주는 `nil?` 함수가 있다.

```clojure
user=> (nil? nil)
true
user=> (nil? false)
false
```

## 숫자

숫자는 1, 2, 3... 과 같이 그냥 숫자이고 0.5, 1.5 같은 소수점도 있다. 숫자는 내부적으로 자바의 Long에 저장되고 소수는 Dobule에 저장된다.

```clojure
user=> 1
1
user=> 0.5
0.5
```

분수 타입이 있는 것이 특이한데 `1/3`과 같이 표현한다.

```clojure
user=> 1/3
1/3
```

사칙 연산 함수들을 제공하고 함수명은 각각 `+`, `-`, `*`, `/`이다. Hello World에서 봤던 함수 실행 형태를 다시 설명하면 `(함수명 파라미터값 파라미터값 파라미터값 ...)`형태로 실행한다.

```clojure
user=> (+ 1 2)
3
user=> (- 2 1)
1
user=> (- 1 2)
-1
user=> (* 2 3)
6
user=> (/ 10 2)
5
user=> (/ 1 3)
1/3
```

사칙 연산 함수는 인자를 여러개 가질 수 있다. 

```clojure
user=> (+ 1 2 3 4 5)
15
```

값이 같은지 비교하는 `=` 함수도 있다.

```clojure
user=> (= 1 1)
true
user=> (= 1 1.5)
false
```

## 문자

단일 문자로 `\문자` 형태로 사용한다.

```clojure
user=> \a
\a
```

UTF-8을 지원하기 때문에 한글도 사용할 수 있다.

```clojure
user=>\가
\가
```

## 문자열

문자열은 `"`로 감싸서 표현한다.

```clojure
user=> "clojure"
"clojure"
```

다양한 문자열 함수들을 제공한다.

```clojure
user=> (str "Hello" " " "World")
"Hello World"
user=> (format "Hello %s" "World")
"Hello World"
user=> (count "Hello")
5
```

문자열 비교도 `=` 함수로 하면 된다.

```clojure
user=> (= "clojure" "clojure")
true
user=> (= "clojure" "eunmin")
false
```

자바에서와 같이 같은 static 문자열은 문자열 테이블에 있는 객체를 사용하기 때문에 `=`가 재대로 비교하는지 의심이 들겠지만 아래의 테스트로 실제 값이 같은지를 `=`로 비교해도 된다는 것을 알 수 있다.

```clojure
user=> (= "abc" (str "a" "b" "c"))
true
user=> (identical? "abc" (str "a" "b" "c"))
false
user=> (identical? "abc" "abc")
true
```












