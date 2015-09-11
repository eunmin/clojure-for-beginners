# 메타데이터 - 정리중

메타데이터는 값에 영향을 주지 않으면서 값에 부가적인 정보를 담을 수 있는 기능이다.

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

Var도 메타데이터를 담을 수 있다.

```clojure

```


