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

### apply

함수와 시퀀스를 받아 시퀀스를 함수의 인자로 넘겨 실행한다.

```clojure
user=> (apply + [1 2 3])
6
```

### take

시퀀스 앞에서 부터 지정한 개수만큼 항목을 리턴한다.

```clojure
user=> (take 2 [1 2 3 4 5])
(1 2)
```

### sort

시퀀스 항목이 정렬된 새로운 시퀀스를 리턴한다.

```clojure
user=> (sort [3 2 1])
(1 2 3)
```

정렬 조건 함수를 넘겨 조건을 바꿀 수 있다. 정렬 조건 함수가 없으면 `compare`의 결과를 사용한다.

조건 함수는 `java.util.Comparator`를 구현한다. 

```clojure
user=> (sort #(compare %2 %1) [1 2 3])
(3 2 1)
```

### range

지정한 범위로 시퀀스를 생성한다.

```clojure
user=> (range 1 5)
(1 2 3 4)
user=> (range 1 10 2)
(1 3 5 7 9)
```

### map

시퀀스에 모든 항목에 지정한 함수를 적용한 결과를 담은 시퀀스를 리턴한다.

```clojure
user=> (map (fn [e] (+ e 1)) [1 2 3 4 5])
(2 3 4 5 6)
user=> (map inc [1 2 3 4 5])
(2 3 4 5 6)
```

지정한 함수의 인자로 항목이 넘어오고 리턴값이 결과 시퀀스에 들어가는 값이 된다.

### reduce

시퀀스의 모든 항목에 지정한 함수를 적용한 결과값을 리턴한다.

```clojure
user=> (reduce (fn [r e] (+ r e)) [1 2 3 4 5])
15
user=> (reduce + [1 2 3 4 5])
15
```
지정한 함수는 두개의 인자를 받고 첫번째 인자는 앞에 항목을 적용한 리턴 값이 넘어오고 두번째 인자는 항목이 넘어온다.

단 첫번째 항목은 앞의 항목이 없기 때문에 첫번째 인자로 첫번째 항목이 들어오고 두번째 인자로는 두번째 항목이 들어온다.

첫번째 항목에 초기값을 주면 첫번째 인자에 초기값이 들어온다.

```clojure
user=> (reduce + 100 [1 2 3 4 5])
115
```

### filter

시퀀스에서 조건에 만족하는 항목만 남긴 새로운 시퀀스를 리턴한다.

```clojure
user=> (filter #(> % 0) [1 -1 2 -9 3 4 0 5])
(1 2 3 4 5)
(filter pos? [1 -1 2 -9 3 4 0 5])
(1 2 3 4 5)
```

첫번째 파라미터로 항목을 판단하는 함수를 넣어주면된다.

함수의 인자로 항목이 들어오고 리턴값이 `true`면 나중에 결과에 그 항목의 값을 포함한다.

### emtpy?

시퀀스가 비어있는지 판단하는 함수

```clojure
user=> (empty? {})
true
user=> (empty? [1])
false
user=> (empty? "")
true
```

### every?

모든 항목이 조건 함수에 만족하는지 여부를 리턴해주는 함수

```clojure
user=> (every? pos? [1 2 3])
true
user=> (every? pos? [1 2 3 0])
false
```

## 더 많은 시퀀스 함수

클로저 시퀀스 페이지(<http://clojure.org/sequences>)에 더 많은 시퀀스 함수들이 정리되어있다.


