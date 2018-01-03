# 데이터 구조

Clojure에 있는 데이터 구조에 대해 알아보자. REPL로 연습하면서 해보자. 아래 명령어로 REPL을 실행한다.

```clojure
$ clj
Clojure 1.9.0
user=>
```

## 불리언\(Boolean\)

참과 거짓 값을 표현하는 데이터 구조다. 참 값은 true, 거짓 값은 false로 표현한다.

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

## 숫자\(Number\)

숫자는 1, 2, 3... 과 같이 그냥 숫자이고 0.5, 1.5 같은 소수점도 있다. 숫자는 내부적으로 자바의 Long에 저장되고 소수는 Dobule에 저장된다.

```clojure
user=> 1
1
user=> 0.5
0.5
```

Ratio라는 분수 타입이 있는데 `1/3`과 같이 표현한다.

```clojure
user=> 1/3
1/3
```

사칙 연산 함수들을 제공하고 함수명은 각각 `+`, `-`, `*`, `/`이다.

함수는 `(함수명 파라미터값 파라미터값 파라미터값 ...)`형태로 실행한다.

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

## 문자\(Character\)

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

## 문자열\(String\)

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

자바와 같이 같은 static 문자열은 문자열 테이블에 있는 객체를 사용한다.

따라서 `=`가 재대로 비교하는지 아래와 같이 확인해 봐야한다.

```clojure
user=> (= "abc" (str "a" "b" "c"))
true
user=> (identical? "abc" (str "a" "b" "c"))
false
user=> (identical? "abc" "abc")
true
```

확인해 본 결과 값이 같은지 비교할때 `=`를 써도 된다는 것을 알수있다.

실제로 `=` 비교는 내부적으로 자바의 `equals`로 비교하기 때문에 문자열 값 비교가 정상적으로 동작한다. \([https://github.com/clojure/clojure/blob/master/src/jvm/clojure/lang/Util.java\#L33](https://github.com/clojure/clojure/blob/master/src/jvm/clojure/lang/Util.java#L33)\)

## \* 리스트\(List\)

순서가 있는 값의 목록으로 `(항목 항목 항목)`형태로 표현한다.

```clojure
user=> (0 1 2 3 4)
ClassCastException java.lang.Long cannot be cast to clojure.lang.IFn  user/eval17466 (form-init2102152485734565748.clj:1)
```

위의 리스트는 0 부터 4까지 값을 순서대로 갖는 자료 구조이다.  
REPL에서 코드를 입력하고 엔터를 치면 에러가 난다. REPL은 엔터를 치면 클로저 코드를 평가해 값을 계산하는데 리스트 자료 구조는 코드가 평가\(실행 또는 값을 구함\)될 때 함수로 동작하기 때문이다.

그래서 위의 예제는 `0`이라는 이름의 함수에 `1`, `2`, `3`, `4`를 인자로 넘기는 코드로 동작하고 `0`이라는 함수가 없기 때문에 에러가 난 것이다.

어떤 값이 평가 되는 것을 막기 위해서는 앞에 `'`를 붙이면 된다. 값이 평가되지 않으면 값 그자체로 사용된다.

```clojure
user=> '(0 1 2 3 4)
(0 1 2 3 4)
user=> '(0 1 2)
(0 1 2)
user=> '("클로저" "데이터" "형식")
("클로저" "데이터" "형식")
```

다양한 리스트 함수가 있다.

```clojure
user=> (first '(0 1 2))
0
user=> (second '(0 1 2))
1
user=> (last '(0 1 2))
2
user=> (rest '(0 1 2))
(1 2)
user=> (count '(0 1 2))
3
```

## 벡터\(Vector\)

리스트 처럼 순서 있는 항목으로 `[항목 항목 항목]` 형태로 표현한다.

리스트는 첫번째 항목이 함수로 실행되서 데이터로 사용하기 불편하기 때문에 주로 데이터를 담을 때 벡터를 쓴다.

벡터에도 리스트 함수를 쓸수 있다.

```clojure
user=> [0 1 2]
[0 1 2]
```

## 맵\(Map\)

'키/값'을 가지는 데이터 구조로 순서는 보장되지 않는다.

`{키 값, 키 값}` 형태로 표현한다.

```clojure
user=> {"id" 1, "name" "eunmin"}
{"id" 1, "name" "eunmin"}
```

'키/값'과 '키/값'을 구분하는데 사용하는 `,`는 생략할 수 있다.

```clojure
user=> {"id" 1 "name" "eunmin"}
{"id" 1, "name" "eunmin"}
```

한 맵에서 키 데이터 형식은 제한이 없다.

```clojure
user=> {34932 "httpd", "cpu" 98}
{"cpu" 98, 34932 "httpd"}
```

`get` 함수는 키로 값을 가져올 수 있다.

```clojure
user=> (get {"name" "eunmin", "level" 40} "name")
"eunmin"
user=> (get {"name" "eunmin", "level" 40} "id")
nil
```

`get`에서 키가 없다면 `nil`값이 리턴된다.

`assoc` 함수로 맵에 값을 넣거나 바꿀 수 있다.

```clojure
user=> (assoc {"name" "eunmin" "level" 40} "server" "asia")
{"server" "asia", "name" "eunmin", "level" 40}
user=> (assoc {"name" "eunmin" "level" 40} "level" 41)
{"name" "eunmin", "level" 41
```

## 키워드\(Keyword\)

주로 맵의 키로 사용되는 데이터 형식으로 `:이름`으로 표현한다.

키워드를 맵의 키로 사용할 때는 `,`를 생략하는 것이 가독성에 좋다.

```clojure
user=> :id
:id
user=> {:id 2232 :name "eunmin"}
{:id 2232, :name "eunmin"}
```

`keyword`라는 함수로 문자열로 키워드를 만들 수 있다.

```clojure
user=> (keyword "id")
:id
```

문자열과 다르게 같은 키워드는 항상 같은 인스턴스를 가리킨다.

따라서 같은 이름의 키워드가 많이 생겨도 하나의 인스턴스를 유지한다.

```clojure
user=> (identical? (keyword "id") (keyword (str "i" "d")))
true
```

키워드는 함수로 사용할 수 있다.

키워드를 함수로 사용하면 맵에서 자신을 키로 가지는 값을 가져온다.

```clojure
user=> (:id {:id 2232 :name "eunmin"})
2232
user=> (:name {:id 2232 :name "eunmin"})
"eunmin"
```

## 셋\(Set\)

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

`contains?` 함수로 값이 있는지 확인할 수 있다.

```clojure
user=> (contains? #{:id :name :level} :id)
true
```

## \* 심볼\(Symbol\)

그냥 문자들은 심볼 데이터이고 평가되면 연결된 값으로 바꾸는 동작을 하기 때문에 보통 이름으로 사용한다.

역시 평가되지 않게 하려면 리스트처럼 `'`를 앞에 붙여 주면 된다.

```clojure
user=> println
#object[clojure.core$println 0xbb97215 "clojure.core$println@bb97215"]
user=> 'a
a
```

UTF-8을 지원하기 때문에 한글도 사용할 수 있다.

```clojure
user=> '더하기
더하기
```

심볼은 값에 바인딩되어 값의 이름으로 사용될 수 있다.

## \* 함수\(Function\)

함수도 값이다.

## 값을 평가하는 법

아래 코드의 결과는 `2`다.

```
(+ 1 1)
```

위 코드는 리스트에 3개 항목이 들어 있는 데이터 구조다. +, 1, 1

데이터 구조이지만 코드이다. Clojure는 코드를 리스트 데이터 구조에 담아서 표현한다.

코드를 그 언어에서 쓰는 데이터 구조로 표현하는 것을 Homoiconicity\([https://en.wikipedia.org/wiki/Homoiconicity\)라고](https://en.wikipedia.org/wiki/Homoiconicity%29라고) 한다.

Homoiconicity 특징을 가지는 언어는 그 언어로 언어를 프로그래밍 하기 쉽다는 장점이 있다. \(메타 프로그래밍\)

위 코드에서 결과가 2가 되는 이유는

**리스트 타입 데이터 구조**는 첫번 째 항목을 함수 값이라고 생각하고 나머지를 그 함수의 인자로 평가하기 때문이다.

+는 심볼이지만 함수 값인 이유는 + 심볼과 함수값을 연결해 놨기 때문이다.

**심볼 타입 데이터 구조**는 그 심볼과 연결된 값으로 바꾼다.

심볼과 값은 Var라는 테이블에 연결 정보를 저장한다.

그외 다른 값은 그 값 그대로 평가된다.

