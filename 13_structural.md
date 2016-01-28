# 조금 더 유연하고 견고하게 만들기 - 정리중

## 멀티메서드

클로저는 자바의 다형성과 같이 같은 함수 이름이지만 인자에 따라 다르게 동작하도록 만들 수 있는 멀티메서드라는
기능을 제공한다. 클로저의 멀티메서드는 자바의 다형성 보다 더 유연하다. 자바의 다형성이 인자의 타입에 따라 다르게
동작 한다면 클로저의 멀티메서드는 인자의 타입 뿐만 아니라 인자의 값에 따라 다르게 동작하도록 만들 수 있다.

예제로 멀티메서드의 사용 방법을 알아보자. 먼저 언어 데이터를 가지는 맵을 인자로 그 언어에 맞는 인사말을 리턴하는
`greeting`이란 함수를 만들어 보자. 간단하게 만들 수 있는 방법은 `case` 구문을 사용하는 것이다.

```clojure
(def english-map {"id" "1", "language" "English"})
(def french-map {"id" "2", "language" "French"})

(defn greeting [language-map]
  (case (language-map "language")
    "English" "Hello!"
    "French" "Bonjour!"))

(greeting english-map)
;; => "Hello!"
(greeting french-map)
;; => "Bonjour!"
```

`case` 구문을 사용해서 잘 동작하도록 만들었다. 이 예제에서 새로운 언어를 추가해야 한다면 `greeting` 함수를 고쳐
`case` 구문에 추가 해줘야한다. 멀티메서드는 조금 더 유연하게 확장 가능한데 위 예제를 멀티메서드로 만들어보자.

* 예제는 https://clojuredocs.org/clojure.core/defmethod 에서 가져옴

```clojure
(defmulti greeting
  (fn[x] (x "language")))

(defmethod greeting "English" [params]
 "Hello!")

(defmethod greeting "French" [params]
 "Bonjour!")

(def english-map {"id" "1", "language" "English"})
(def french-map {"id" "2", "language" "French"})

(greeting english-map)
;; => "Hello!"
(greeting french-map)
;; => "Bounjour!"
```

멀티메서드는 `defmulti`로 함수명과 함수 하나를 인자로 받는다. 인자로 받는 함수는 실제 함수가 불릴 때
받을 인자를 받아 어떠한 값을 리턴하는 함수를 작성해주면 된다. 이 함수의 리턴 값에 따라 이름은 같고 동작을 달리하는
여러개의 함수를 만들 수 있다.

`defmethod` 매크로로 실제 함수의 구현을 만들어 주는데 함수명과 다음에 `defmulti`에서 리턴되어 매칭될
값을 작성해주면 된다. 그리고 나머지 부분을 일반 함수와 같다.

멀티메서드는 자바의 다형성 처럼 함수를 추가하는 방식이기 때문에 `case`문 보다 확장성이 좋다.
또 자바와 메서드와 다르게 특정 클래스에 제한되지 않기 때문에 더 유연하다.

멀티메서드에 `case`의 `default`와 같은 역할을 하는 기능이 있는데 어떤 값도 매칭되지 않았을 때 실행될
함수를 `:default`라는 값으로 작성할 수 있다. 다음 예제는 구현되어 있지 않은 언어로 `greeting` 함수를
실행 했을 때 예외를 발생시키는 예제다.

```clojure
(def english-map {"id" "1", "language" "English"})
(def french-map {"id" "2", "language" "French"})
(def spanish-map {"id" "3", "language" "Spanish"})

(defmulti greeting
  (fn[x] (x "language")))

(defmethod greeting "English" [params]
 "Hello!")

(defmethod greeting "French" [params]
 "Bonjour!")

(defmethod greeting :default [params]
 (throw (IllegalArgumentException.
          (str "I don't know the " (params "language") " language"))))

(greeting english-map)
;; => "Hello!"
(greeting french-map)
;; => "Bounjour!"
(greeting spanish-map)
;; => java.lang.IllegalArgumentException: I don't know the Spanish language
```

## 레코드

클로저의 레코드는 맵을 좀 더 정형화 해서 사용할 수 있는 기능이다. 맵은 맵 안에 있는 데이터에 대한 제약이 없기 때문에
어떤 함수에서 맵에 인자로 들어 왔을 때 그 맵이 그 함수에서 사용할 수 있는 형태의 맵인지 따로 확인하지 않으면
오류가 발생할 수 도 있다. 다음 예를 보자.

```clojure
(def todd {:id 1 :name "todd" :age 10})
(def seoul {:id 1 :name "Seoul"})

(defn add-age [user age]
  (+ (:age user) age))

(add-age todd 10)
;; => 20
(add-age seoul 10)
;; => NullPointerException   clojure.lang.Numbers.ops (Numbers.java:1013)

`add-age`에 `seoul` 맵을 넣었을 때 에러가 발생한다. `add-age` 함수는 `user` 형태의 맵을 처리하도록
만들었기 때문이다.

레코드는 맵에 어떤 값을 가질 수 있는지 정의하고 타입을 가질 수 있는 기능이다.
다음과 같이 user 맵의 형태를 레코드로 정의 할 수 있다.

```clojure
(defrecord User [id name age])
```

레코드는 `defrecord` 매크로로 정의하고 레코드 이름과 레코드에 포함될 키워드를 심볼로 표현해서 벡터에 담아준다.
보통 레코드명은 첫글자를 대문자로 한다.
레코드의 인스턴스를 만드는 방법은 두가지가 있는데 다음과 같다.

```bash
user=> (->User 1 "todd" 10)
#struct_sample.core.User{:id 1, :name "todd", :age 10}
user=> (map->User {:id 1 :name "todd" :age 10})
#struct_sample.core.User{:id 1, :name "todd", :age 10}
```

첫번째 방법은 `->` 표시와 레코드명을 붙여 함수처럼 사용하는 방법인데 레코드 정의에 나온 벡터의 순서대로 인자를
레코드 인스턴스가 리턴된다.
두번째 방법은 `map->` 표시와 레코드명을 붙여 함수처럼 사용하는 방법인데 인자를 맵 형식으로 받을 수 있다.

리턴된 값을 보면 일반 맵과 다르게 생겼는데 `class`로 타입을 조회해 보자.

```bash
user=> (def todd (->User 1 "todd" 10))
#struct_sample.core.User{:id 1, :name "todd", :age 10}
user=> (class todd)
user.User
```

`class`가 맵이 아니고 네임스페이스가 포함된 레코드 명을 가진다 하지만 레코드 타입은 일반 맵 처럼 사용할 수 있다.

```bash
user=> (def todd-with-level (assoc todd :level 1))
#'user/todd-with-level
user=> (:level todd-with-level)
1
user=> (class todd-with-level)
struct_sample.core.User
```

위에서 한가지 이야기하고 넘어갈 부분이 있는데 User 레코드에 `assoc` 함수로 정의되지 않은 `:level`이라는
키워드를 추가했다는 점이다. 레코드는 포함하고 있는 키들을 정의하긴 하지만 이것을 제약하지는 않는 것을 알 수 있다.
이를 제약 하기 위해서는 뒤에서 다룰 타입 라이브러리들을 사용할 수 있다.

그럼 위에서 만든 `User` 레코드를 가지고 `add-age`를 다시 만들어 보자.

```clojure
(defrecord User [id name age])

(def todd (map->User {:id 1 :name "todd" :age 10}))
(def seoul {:id 1 :name "Seoul"})

(defn add-age [user age]
  (when-not (= (class user) User)
    (throw (IllegalArgumentException.)))
  (+ (:age user) age))

(add-age todd 10)
;; => 20
(add-age seoul 10)
;; => IllegalArgumentException   user/add-age (...)
```

알아 본 것과 같이 record는 맵 데이터를 더 제약적으로 사용할 수 있도록 해준다. 보통 어플리케이션의 모델을 작성할 때
레코드를 사용하는 것을 추천하고 라이브러리의 인터페이스로는 유연성이 떨어지기 때문에 레코드 타입을 사용하지 않는 것을
권장한다. (Clojure Applied)

## 프로토콜

프로토콜은 특정 타입이 지원하는 함수의 목록을 정의해 놓은 것이다. 위에서 만든 예제에서 `add-age` 함수는  
`User`라는 레코드 타입만 지원하도록 `add-age` 안에 타입 체크하는 코드를 넣어 뒀다. 하지만 이렇게 모든 함수를
작성하는 것은 지저분해보인다. 프로토콜을 사용하면 어떤 타입에 대해서 제한적으로 수행할 수 있는 함수들을 만들 수 있다.

프로토콜은 `defprotocol` 매크로로 만들 수 있다.

```clojure
(defprotocol AgeCalculable
  (add-age [user age]))
```

프로토콜은 레코드 정의와 비슷하게 `defprotocol` 다음에 프로토콜 이름이 오고 다음에는 이 프로토콜이 지원하는 함수의
시그네쳐들을 여러개 정의한다. 주의 할 점은 첫번째 인자로는 항상 인스턴스가 넘어와야 한다는 점이다. 그래서 인자가 없는 함수를
프로토콜로 정의 할 수 없다.

그럼 이 프토콜의 구현을 만들어야 하는데 프로토콜 구현은 레코드 안에 할 수 있다. 다음은 `User` 레코드에 `add-age`를
구현한 예다.

```clojure
(defrecord User [id name age]
  AgeCalculable
  (add-age [this age]
    (+ (:age this) age)))
```

레코드의 정의 다음에 프로토콜 이름을 적어주고 그 아래 그 프로토콜이 지원하는 함수들을 구현해 주면 된다.
함수 구현의 모양은 일반 함수와 다른 것이 없다. `add-age`를 사용할 때는 함수 처럼 사용하면 된다.

```clojure
(def todd (->User 1 "todd" 10))
;; => #'user/todd
(add-age todd 10)
;; => 20
```

단 `add-age` 프로토콜을 정의하면 루트 바인딩 처럼 심볼을 사용할 수 있기 때문에 사용하려면 프로토콜이 정의된
네임스페이스에서 `add-age`를 불러(`require`) 쓰면 된다.


## core.typed

https://github.com/clojure/core.typed

## schema

https://github.com/plumatic/schema
