# 시퀀스

클로저는 시퀀스라는 추상 데이터 형식을 가지고 많은 데이터를 같은 방식으로 처리 할 수 있다.

## 시퀀스 함수

자주 사용하는 시퀀스 함수들을 소개한다.

### first

```clojure
user=> (first [1 2 3])
1
user=> (first '(1 2 3))
1
```

시퀀스의 첫번째 항목을 리턴한다.

```clojure
user=> (first {:id 1 :name "eunmin"})
[:name "eunmin"]
```

리스트나 벡터는 순서가 있기 때문에 첫번째 항목이 `1`이 리턴된다.

맵은 순서가 없기 때문에 `[:name "eunmin"]`가 리턴된다.

그렇다고 함수를 부를 때마다 다른 값이 리턴되지는 않고 데이터가 같다면 항상 같은 항목이 리턴된다.

맵은 벡터 처럼 보이는 키와 값 쌍이 리턴되었다.

키와 쌍 값은 `MapEntry`라는 형식으로 `key` 함수와 `val` 함수로 키 또는 값을 가져올 수 있다.

```clojure
user=> (def first-element (first {:id 1 :name "eunmin"}))
#'user/first-element
user=> first-element
[:name "eunmin"]
user=> (class first-element)
clojure.lang.MapEntry
user=> (key first-element)
:name
user=> (val first-element)
"eunmin"
```

`MapEntry`도 시퀀스기 때문에 `first` 함수로 키를 가져올 수 있다.

```clojure
user=> (first first-element)
:name
```

문자열도 시퀀스 함수를 사용 할 수 있다.

```clojure
user=> (first "Hello World")
\H
```

문자열은 문자가 순서대로 있는 시퀀스로 다뤄지기 때문에 위 예제에서는 첫번째 문자인 `\H`가 리턴됬다.

### second

시퀀스의 두번째 항목을 가져온다.

```clojure
user=> (second [1 2 3])
2
```

### nth 

지정한 인덱스에 있는 시퀀스 항목을 리턴한다.

```clojure
user=> (nth [1 2 3] 2)
3
user=> (nth "Hello World" 5)
\space
```

### rest

첫번째 항목을 제외한 나머지 항목을 가져온다.

```clojure
user=> (rest [1 2 3])
(2 3)
user=> (first (rest [1 2 3]))
2
```

### last

마지막 항목을 가져온다.

```clojure
user=> (last [1 2 3])
3
```


