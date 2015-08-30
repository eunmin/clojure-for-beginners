# 네임스페이스 - 정리중

큰 프로그램에서는 많은 이름들이 사용되고 그러다 보면 이름이 겹치지 않게 하려고 이름을 길게 쓰게 된다. `user-name`, `group-name`과 같이 이름에 분류가 들어가기도 한다.

많은 프로그래밍 언어에서는 이름을 카테고리로 분류 할 수 있는 네임스페이스 기능을 제공한다.

네임스페이스는 이름을 정의 할 수 있는 구역과 그 구역에서 다른 이름을 가져다 쓸 수 있는 방법이 있어야 하는데 클로저에서 어떻게 하는지 알아본다.

## 네임스페이스 정하기

클로저는 `ns` 구문을 사용해서 네임스페이스 구역을 정한다.

REPL의 기본 네임스페이스는 `user`라서 지금까지는 `ns` 없이 이름을 만들었지만 모두 `user` 네임스페이스에 속한 이름이였다.

```clojure
user=> (ns user)
nil
user=> (defn create [name]
  #_=>   {:id (str (gensym "user")) :name name})
#'user/create
user=> (create "eunmin")
{:id "user15003", :name "eunmin"}
user=> (ns group)
nil
group=> (defn create [name user]
   #_=>   {:id (str (gensym "group")) :name name :leader user})
#'group/create
group=> (create "clojure" nil)
{:id "group15019", :name "clojure", :leader nil}
group=> create
#<group$create group$create@113114e5>
group=> (ns user)
nil
user=> create
#<user$create user$create@2c26618f>
```

위 예에서 `(ns 심볼)`로 네임스페이스를 정해줬다.

`(ns 심볼)` 이후에 생성한 Var는 모두 `user` 네임스페이스에 속한다. 

REPL에서는 편의상 앞에 현재 네임스페이스를 표시해준다. 

`user=>`는 현재 네임스페이스가 `user`라는 뜻이다.

예에서는 `user` 네임스페이스에 `create` 함수를 만들고 다시 `(ns group)`으로 네임스페이스를 바꾸고 `create` 함수를 만들었다.

같은 네임스페이스에서 `create` 이름의 함수를 다시 만들면 기존에 연결된 함수는 `create` 이름으로 접근할 수 없다.

하지만 네임스페이스가 다른 두 공간에서 `create` 함수를 만들었기 때문에 `user` 네임스페이스 `create`와 `group` 네임스페이스에 `create`를 각각 접근할 수 있다.

참고고 위의 예에서 `str`은 문자열을 만드는 함수고 `gensym`은 심볼을 클로저가 자동으로 생성해주는 구문이다.

## 다른 네임스페이스에 있는 이름 사용하기

현재 네임스페이스에서 다른 네임스페이스에 있는 이름을 사용하려면 다른 네임스페이스를 불러와야한다.

`require` 구문으로 다른 네임스페이스를 현재 네임스페이스에서 쓸 수 있다.

```clojure
group=> (require 'user)
nil
```

`(require 심볼)` 해주면 된다. 심볼은 평가되지 않도록 `'` 표시를 해준다. 표시하지 않으면 현재 네임스페이스 있는 이름을 평가하려고 해서 `user`로 바인딩된 심볼이 없다는 에러가 난다.

이제 `user` 네임스페이스에 있는 이름을 사용할 수 있다.

사용하는 법은 `네임스페이스/이름` 형식으로 사용하면 된다.

```clojure
group=> (user/create "eunmin")
{:id "user15050", :name "eunmin"}
group=> (create "clojure" (user/create "eunmin"))
{:id "group15054", :name "clojure", :master {:id "user15053", :name "eunmin"}}
```

`group` 네임스페이스에서 그룹을 생성하기 위해 `create`를 부르고 두번째 파라미터로 그룹 리더를 생성하기 위해 `user/create`를 불렀다.

## 네임스페이스를 변경하면서 다른 네임스페이스를 사용하기

하나의 네임스페이스에서 다른 네임스페이스를 사용하는 일이 많기 때문에 네임스페이스를 변경함과 동시에 다른 네임스페이스를 `require` 할 수 있는 편의 기능이 제공된다.

```clojure
user=> (ns group (:require user))
nil
group=> 
```

`ns` 구분 뒤에 `(:require 네임스페이스심볼)`을 적어주면 된다.

`ns`는 안에 있는 심볼들을 평가하지 않도록 하는 특수한 구문이기 때문에 안에 있는 네임스페이스에 `'` 표시해주지 않아도 된다.

## 네임스페이스 나누기

프로그램이 커지면 네임스페이스도 하나로 부족하고 네임스페이스도 여러 계층으로 나눠 쓸수 있어야한다.

클로저는 자바의 패키지와 같이 네임스페이스를 `.`으로 나눠 쓸 수 있다.

```clojure
user=> (ns namespace-sample.handler.user)
nil
namespace-sample.handler.user=>
```

첫 계층은 대부분 프로젝트 이름을 사용한다.
자바 처럼 도메인을 뒤집어 사용하는 경우도 있지만 클로저에서는 대부분 그냥 프로젝트 이름을 사용한다.

## 네임스페이스와 파일명

처음 Hello World 예제에서 아래와 같은 코드를 작성했다.

```clojure
(ns example.hello)

(defn -main [] (println "Hello World"))
```

이때 `example` 디렉토리 아래 `hello.clj` 파일을 만들고 컴파일 해서 실행했다.

자바 패키지 처럼 클로저도 네임스페이스에 디렉토리 구조와 파일명 제약을 가진다.

따라서 `(ns namespace-sample.handler.user)`는 `namespace_sample/handler/user.clj`에 있어야 한다.

주의 깊게 봐야할 것은 대쉬 `-` 기호는 실제 파일이나 디렉토리명에서 밑줄 `_` 기호로 바꿔서 사용해야한다는 점이다.

## 네임스페이스를 다른 이름으로 사용하기

네임스페이스를 나눠쓰면 길어지기 마련이다.

```clojure
namespace-sample.handler.user=> (def header-name "user")
#'namespace-sample.handler.user/header-name
namespace-sample.handler.user=> (ns group)
nil
group=> (require 'namespace-sample.handler.user)
nil
group=> namespace-sample.handler.user/header-name
"user"
```

이럴 때는 네임스페이스 alias를 사용해서 짧게 줄여 쓸 수 있다.

```clojure
group=> (require '[namespace-sample.handler.user :as user-handler])
nil
group=> user-handler/header-name
"user"
```

사용법은 `require`에 네임스페이스 부분을 벡터로 바꾸고 벡터의 첫번째 항목에 사용할 네임스페이스 뒤에 `:as` 키워드와 바꿔쓸 심볼을 적어주면된다.

물론 `ns`를 사용할 때도 alias를 사용할 수 있다.

```clojure
group=> (ns group (:require [namespace-sample.handler.user :as user-handler]))
nil
group=> user-handler/header-name
"user"
```

## 현재 네임스페이스에 있는 이름 처럼 쓰기

DSL 같은 라이브러리와 같이 외부에 있는 이름을 현재 네임스페이스에 있는 이름 처럼 쓰면 더 깔끔해 보일 때가 있다.

이때는 `require`의 `refer` 옵션을 사용하면 된다.

```clojure
group=> (require '[namespace-sample.handler.user :refer [header-name]])
nil
group=> header-name
"user"
```

사용법은 `:refer` 키워드 뒤에 사용할 네임스페이스에 있는 이름 심볼을 벡터에 넣어주면 된다. 여러개가 있다면 여러개 넣어주면 된다.

```clojure
group=> (require '[clojure.test :refer [deftest is]])
nil
group=> (deftest addition
   #_=>   (is (= 4 (+ 2 2)))
   #_=>   (is (= 7 (+ 3 4))))
#'group/addition
```

만약 다른 네임스페이스에 있는 이름 전부를 현재 이름에 있는 것 처럼 쓰려면 `:refer` 뒤에 `:all`을 해준다.

```clojure
group=> (require '[clojure.test :refer :all])
nil
group=> (deftest addition
   #_=>   (is (= 4 (+ 2 2)))
   #_=>   (is (= 7 (+ 3 4))))
#'group/addition
```

## 다른 네임스페이스에서 쓰지 못하게 하기

네임스페이스에 정의 한 이름을 다른 네임스페이스에서 못쓰게하면 좋은 경우가 있다. 

현재 네임스페이스에서만 사용하는 이름이라면 다른 네임스페이스에서 못쓰게 해두면 변경해야할 일이 생겼을 때 조금 안심할 수 있다.

그렇지 않으면 다른 네임스페이스에서 어떤 문제가 생길지 알 수 없다.

물론 테스트 코드를 잘 작성하면 되겠지만 그래도 불안하다.

```clojure
user=> (defn ^:private valid-name? [name]
  #_=>   (string? name))
user=> (ns group)
nil
group=> (require 'user)
nil
group=> (user/valid-name? "eunmin")

CompilerException java.lang.IllegalStateException: var: #'user/valid-name? is not public, compiling:(NO_SOURCE_PATH:1:1)
```

사용법은 이름 앞에 `^:private`을 붙여주면 된다.

함수를 만들어 이름을 연결하는 `defn`은 `^:private` 대신 `defn-`을 사용하면 간편한다.

```clojure
user=> (defn- valid-name? [name]
  #_=>   (string? name))
#'user/valid-name?
```

일반 값을 만들어 이름을 연결하는 `def`에는 `def-` 같은것을 제공하지 않는다.

## 상호 참조되는 네임스페이스

클로저 파일들이 컴파일 될때 네임스페이스는 상호간 참조가 불가능하다.

다음 파일을 보자.

```clojure
(ns circular-dependency-sample.user
  (:require [circular-dependency-sample.group :as group]))

(defn restricted? [user]
  false)

(defn create [name]
  (when-not (group/find-member name)
    {:id (str (gensym "user")) :name name}))
```

`circular-dependency-sample.user` 네임스페이스에서  `circular-dependency-sample.group`을 사용했다.

```clojure
(ns circular-dependency-sample.group
  (:require [circular-dependency-sample.user :as user]))

(defn find-member [name]
  nil)

(defn create [name user]
  (when-not (user/restricted? user)
    {:id (str (gensym "group")) :name name :leader user}))
```

그리고 `circular-dependency-sample.group` 네임스페이스에서 `circular-dependency-sample.user`를 사용했다.

이런 경우 컴파일 될때 어떤 파일이 앞서 있어야 하는지 알지 못하기 때문에 컴파일 에러가 난다.


```bash
$ lein check
Compiling namespace circular-dependency-sample.core
Compiling namespace circular-dependency-sample.group
Exception in thread "main" java.lang.Exception: Cyclic load dependency: [ /circular_dependency_sample/group ]->/circular_dependency_sample/user->[ /circular_dependency_sample/group ], compiling:(circular_dependency_sample/user.clj:1:1)
	at clojure.core$throw_if.doInvoke(core.clj:5405)
	at clojure.lang.RestFn.invoke(RestFn.java:442)
	at clojure.core$check_cyclic_dependency.invoke(core.clj:5541)
	at clojure.core$load.doInvoke(core.clj:5638)
	at clojure.lang.RestFn.invoke(RestFn.java:408)
	at clojure.core$load_one.invoke(core.clj:5446)
	at clojure.core$load_lib$fn__5015.invoke(core.clj:5486)
	at clojure.core$load_lib.doInvoke(core.clj:5485)
	at clojure.lang.RestFn.applyTo(RestFn.java:142)
	at clojure.core$apply.invoke(core.clj:626)
	at clojure.core$load_libs.doInvoke(core.clj:5524)
	at clojure.lang.RestFn.applyTo(RestFn.java:137)
	at clojure.core$apply.invoke(core.clj:626)
	at clojure.core$require.doInvoke(core.clj:5607)
	at clojure.lang.RestFn.invoke(RestFn.java:408)
	at circular_dependency_sample.user$eval45$loading__4958__auto____46.invoke(user.clj:1)
	at circular_dependency_sample.user$eval45.invoke(user.clj:1)
	at clojure.lang.Compiler.eval(Compiler.java:6703)
	...
```	

이런 경우 다른 해결 방법은 없다. 

결과적으로 이런 구조는 좋지 않기 때문에 사용하려고 하는 네임스페이스를 새로운 네임스페이스로 분리하는 등과 같은 방법으로 구조를 변경하는 것이 좋다. 


## 그 밖에

다른 네임스페이스를 사용하는 `use`라는 구문이 있지만 잘 사용하지 않는다.

