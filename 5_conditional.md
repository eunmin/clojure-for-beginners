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


