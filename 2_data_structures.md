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

## 리스트

순서가 있는 값의 목록으로 `(항목 항목 항목)`형태로 표현한다.

```clojure
user=> (+ 1 2)
3
```

위의 리스트는 첫번째 항목이 `+` 함수, 두번째 항목이 `1`, 세번째 항목이 `2` 값을 갖는 리스트다.

첫번째 항목은 함수로 실행하기 때문에 위의 REPL에서는 실행 결과인 3을 표시해줬다.

리스트가 실행이 되는 것을 막기 위해서는 앞에 `'`를 붙여줘서 평가를 하지 않도록 할 수 있다.

```clojure
user=> '(+ 1 2)
(+ 1 2)
user=> '(0 1 2)
(0 1 2)
user=> '("클로저" "데이터" "형식")
("클로저" "데이터" "형식")
```

리스트를 위한 다양한 함수들이 있다.

```clojure
user=> (first '(0 1 2))
0
user=> (first '(+ 1 2))
+
user=> (second '(+ 1 2))
1
user=> (last '(+ 1 2))
2
user=> (rest '(+ 1 2))
(1 2)
user=> (count '(+ 1 2))
3
```

## 벡터

리스트와 같이 순서가 있는 항목으로 `[항목 항목 항목]` 형태로 표현한다.

리스트는 첫번째 항목이 평가되 함수로 실행되어 데이터로 사요하기 번거롭기 때문에 주로 데이터를 담을 때 벡터를 이용한다.

리스트 함수를 모두 사용할 수 있다.

```clojure
user=> [0 1 2]
[0 1 2]
```

## 맵

키/값 형식을 가지는 데이터 구조로 순서는 보장되지 않는다.

`{키 값, 키 값}` 형태로 표현한다.

```clojure
user=> {"id" 1, "name" "eunmin"}
{"id" 1, "name" "eunmin"}
```

중간에 사용하는 `,`는 생략할 수 있다.

```clojure
user=> {"id" 1 "name" "eunmin"}
{"id" 1, "name" "eunmin"}
```

한 맵에서 키 데이터 형식은 섞어 쓸 수 있다.

```clojure
user=> {34932 "httpd", "cpu" 98}
{"cpu" 98, 34932 "httpd"}
```

`get` 함수를 써서 키를 가지고 값을 가져올 수 있다.

```clojure
user=> (get {"name" "eunmin", "level" 40} "name")
"eunmin"
user=> (get {"name" "eunmin", "level" 40} "id")
nil
```

`get`에서 키가 없다면 `nil`값이 리턴된다. 

`assoc` 함수로 값을 추가하거나 변경할 수 있다.

```clojure
user=> (assoc {"name" "eunmin" "level" 40} "server" "asia")
{"server" "asia", "name" "eunmin", "level" 40}
user=> (assoc {"name" "eunmin" "level" 40} "level" 41)
{"name" "eunmin", "level" 41
```

## 키워드

주로 맵의 키로 사용되는 형식으로 `:이름`으로 표현한다.

키워드로 맵의 키를 사용할 때는 가독성을 위해 `,`를 생략한다.

```clojure
user=> :id
:id
user=> {:id 2232 :name "eunmin"}
{:id 2232, :name "eunmin"}
```

`keyword`라는 함수로 문자열을 가지고 키워드를 만들 수 있다.

```clojure
user=> (keyword "id")
:id
```

문자열과 다르게 같은 키워드는 같은 인스턴스를 가리킨다.따라서 같은 이름의 키워드가 많이 생겨도 하나의 인스턴스를 유지한다.

```clojure
user=> (identical? (keyword "id") (keyword (str "i" "d")))
true
```

키워드는 함수로 맵에서 자신을 키로 가지는 값을 가져온다.

```clojure
user=> (:id {:id 2232 :name "eunmin"})
2232
user=> (:name {:id 2232 :name "eunmin"})
"eunmin"
```

## 셑

중복되지 않는 항목을 가지는 데이터 형태로 `#{항목 항목 항목}` 으로 표현한다. 맵처럼 순서는 보장되지 않는다.

```clojure
user=> #{3 2 1}
#{1 3 2}
```

중복된 값을 넣으면 예외가 발생한다.

```clojure
user=> #{1 2 3 1}

IllegalArgumentException Duplicate key: 1  clojure.lang.PersistentHashSet.createWithCheck (PersistentHashSet.java:68)
```

맵처럼 `get`이나 키워드로 항목을 가져올 수 있다.

```clojure
user=> (get #{1 2 3} 1)
1
user=> (:id #{:id :name :title})
:id
user=> (:level #{:id :name :title})
nil
```

`contains?` 함수로 값이 있는지 확인 할수 있다.

```clojure
user=> (contains? #{:id :name :level} :id)
true
```

## 심볼

그냥 문자들은 심볼 데이터이고 주로 이름에 사용된다

REPL에서 심볼은 평가하려고 한다.

```clojure
user=> a

CompilerException java.lang.RuntimeException: Unable to resolve symbol: a in this context, compiling:(NO_SOURCE_PATH:0:0)
```

REPL에서 평가되지 않게 하려면 리스트처럼 `'`를 앞에 붙여 주면 된다.

```clojure
user=> 'a
a
```

UTF-8을 지원하기 때문에 한글 심볼도 사용할 수 있다.

```clojure
user=> '더하기
더하기
```

심볼은 값에 바인딩되어 값의 이름으로 사용될 수 있다.






