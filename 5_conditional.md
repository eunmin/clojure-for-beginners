# 조건문

`if` 구문은 조건에 따라 다른 값을 표시할 수 있다.

`if` 구문의 형태는 `(if 조건 참값 거짓값)`다.

```clojure
user=> (if true 1 2)
1
user=> (if false 1 2)
2
```

거짓값은 생략할 수 있고 생략하면 `nil`값을 나타낸다.

```clojure
user=> (if true 1)
1
user=> (if false 1)
nil
```

거짓값을 생략하면 코드가 명확하지 않기 때문에 `if`를 사용하는 것보다 `when`구문을 사용하는 것이 좋다.

```clojure
user=> (when true 1)
1
user=> (when false 1)
nil
```

`if` 구문은 값을 조건에 따른 값을 나타내는 구문이기 때문에 값이 쓰이는 곳이면 어디든 쓸 수 있다.

```clojure
user=> (let [x (if true 1 2)] (+ x 1))
2
user=> (let [x (if false 1 2)] (+ x 1))
3
user=> (+ (if true 1 2) 3)
4
user=> (+ (if false 1 2) 3)
5
```

`nil`은 거짓으로 판단되고 그렇지 않는 값은 참으로 판단된다.

```clojure
user=> (if nil 2 3)
3
user=> (if 1 2 3)
2
```

## 테스트 구문

조건을 판단해주는 `=`, `>`, `<`, `>=`, `<=` 등이 있다.

```clojure
user=> (if (= 1 1) 1 2)
1
user=> (if (= "abc" "abc") 1 2)
1
user=> (if (> 1 2) 1 2)
2
user=> (if (< 1 2) 1 2)
1
user=> (if (= 1 1) 1 2)
1
user=> (if (= "abc" "abc") 1 2)
1
```

조건이 맞지 않는 것을 판단해주는 `not`이 있다.

```clojure
user=> (if (not (= 1 1)) 1 2)
2
user=> (if (not (< 1 2)) 1 2)
2
```

같지 않은 것을 판단하는 일은 많이 있기 때문에 `not=`을 제공한다.

```clojure
user=> (if (not= 1 2) 1 2)
1
```

## and와 or

여러 조건을 조합하기 위해 `and`와 `or`를 제공한다.

```clojure
user=> (if (and (= 1 1) (= 2 2)) "O" "X")
"O"
user=> (if (and (= 1 1) (= 2 3)) "O" "X")
"X"
user=> (if (or (= 1 1) (= 2 3)) "O" "X")
"O"
user=> (if (or (= 1 2) (= 2 3)) "O" "X")
"X"
```

`and`와 `or` 구문은 따로 쓸 수도 있다.

`and`는 항목의 값을 하나씩 판단하는데 먼저 나오는 거짓값을 나타낸다.

```clojure
user=> (and (+ 1 2) (+ 3 4) nil (+ 5 6))
nil
user=> (and false (+ 1 2) (+ 3 4) (+ 5 6))
false
```

위에 있는 첫번째 예제에서 `and`는 `nil`이 나오기 전까지만 확인해보고 이후에 있는 값은 무시한다. 

두번째 예제는 바로 false라 아무것도 확인하지 않는다.

`or`도 따로 쓸 수 있다.

```clojure
user=> (or 1 2)
1
user=> (or nil 2)
2
```

`or`는 처음 나온는 참값을 나타낸다. 때에 따라서 값이 없을 때 기본값을 사용하기 좋다.

```clojure
user=> (def params {:limit 10})
#'user/params
user=> (let [limit (or (:limit params) 50)] limit)
10
user=> (def params {})
#'user/params
user=> (let [limit (or (:limit params) 50)] limit)
50
```

## 조건문은 함수 인가?

다음은 조건이 참이면 `참`을 출력하고 조건이 거짓이면 `거짓` 을 출력하는 함수다.

```clojure
(defn print-bool [bool]
  (if bool (println "참") (println "거짓")))
  
user=> (print-bool true)
참
nil
user=> (print-bool false)
거짓
nil  
```

만약 `if`가 함수라면 함수를 평가하는 동작 처럼 안쪽에 있는 구문들이 순서대로 실행되어야 하고 그렇다면 `참`과 `거짓` 모두 출력 되어야 한다.

`if`를 다음과 같이 `my-if`라고 만들어보자.

```clojure
(defn my-if [condition true-form false-form]
  (if condition
    true-form
    false-form))
    
user=> (my-if (= 1 1) (println "참" (println "거짓))
참
거짓
nil
```

기본적인 함수의 동작과 같이 `참`과 `거짓` 모두 출력되었다.

이것은 `if`라는 구문이 일반적인 함수가 아니라는 뜻이다. 
실제로 `if` 구문은 클로저의 매크로라고 하는 기능으로 구현 되어 있고 매크로로 `my-if`를 구현할 수 있다. 

매크로는 뒤에서 다룬다.

## if-let

아래는 `get-user`라는 함수로 가져온 값을 `user`에 로컬 바인딩하고 `user`값이 있으면 `:id`값을 리턴하고 없으면 "user not found"라는 문자열을 리턴하는 예제다.

이번에는 코드가 좀 길어 보기 좋으라고 중간 중간 개행을 해줬다.

```clojure
user=> (defn get-user []
  #_=>   {:id 1 :name "eunmin"})
#'user/get-user

user=> (let [user (get-user)]
  #_=>   (if user
  #_=>     (:id user)
  #_=>     "user not found"))
1
```

예제에서는 `get-user`가 항상 값을 리턴하기 때문에 `:id`값이  `1`이 나왔다.

하지만 `get-user`가 `nil`을 리턴 할수도 있다면 위와 같이 사용해서 `nil`인 경우에 대한 처리를 해줘야한다.

이런 경우에 간단히 사용할 수 있는 것이 `if-let` 구문이다.

```clojure
user=> (if-let [user (get-user)]
  #_=>   (:id user)
  #_=>   "user not found")
1
```

조건과 바인딩을 동시해 해서 코드를 조금더 간단하게 쓸 수 있다.

다만 `let`과 같이 여러개를 바인딩 할 수는 없다.

`if-let` 비슷하게 `when-let`도 있다. `when-let`은 거짓에 대한 값이 없는 경우 사용할 수 있다.

```clojure
user=> (when-let [user (get-user)]
  #_=>   (:id user))
1
```

## 조건 함수에 대한 네이밍

클로저에서는 `true` 또는 `false`를 리턴하는 조건 함수들은 `?`로 끝나는 네이밍 규칙을 사용한다.

```clojure
user=> (if (zero? 0) 1 2)
1
user=> (if (zero? 1) 1 2)
2
```

## 예제

```clojure
(def http-default-port 80)

(def config {:production {:host "10.0.0.1"
                          :port http-default-port}
             :development {:host "127.0.0.1"
                           :port 8080}})

(defn url [conf]
  (str "http://" (:host conf)
    (when-not (= http-default-port (:port conf))
      (str ":" (:port conf)))))

(url (:production config))
; => "http://10.0.0.1"

(url (:development config))
; => "http://127.0.0.1:8080"
```