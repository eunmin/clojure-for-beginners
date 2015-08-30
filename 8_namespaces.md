# 네임스페이스 - 정리중

큰 프로그램에서는 많은 이름들이 사용되고 그러다 보면 이름이 겹치지 않게 하려고 이름을 길게 쓰게 된다.

많은 프로그래밍 언어에서는 이름을 카테고리로 분류 할 수 있는 네임스페이스 기능을 제공한다.

네임스페이스는 이름을 정의 할 수 있는 구역과 그 구역에서 다른 이름을 가져다 쓸 수 있는 방법이 있어야 하는데 클로저에서 어떻게 네임스페이스를 사용하는지 알아본다.

## 네임스페이스 정하기

클로저에서 네임스페이스 구역을 정하는 방법은 `ns` 구문을 사용해서 이름을 정의하는 구역을 구분할 수 있다.

REPL의 기본 네임스페이스는 `user`이기 때문에 지금까지는 `ns` 없이 이름을 만들었지만 모두 `user` 네임스페이스에 속한 이름이였다.

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

예에서 `user` 네임스페이스에 `create` 함수를 만들고 다시 `(ns group)`으로 네임스페이스를 변경하고 `create` 함수를 만들었다.

같은 네임스페이스에서 `create` 함수를 다시 만들어 이름과 연결하면 기존에 연결된 이름은 연결이 끝어저 더 이상 접근할 수 없다. 

하지만 네임스페이스가 다른 두 공간에서 `create` 함수를 만들었기 때문에 `user` 네임스페이스 `create`와 `group` 네임스페이스에 `create`가 존재한다.

참고고 위의 예에서 `str`은 문자열 변환 함수고 `gensym`은 심볼을 클로저가 자동으로 생성해주는 구문이다.

## 다른 네임스페이스에 있는 이름 사용하기

현재 네임스페이스에서 다른 네임스페이스에 있는 이름을 사용하기 위해서는 다른 네임스페이스를 불러와야한다.

`require` 구문은 다른 네임스페이스를 현재 네임스페이스에서 쓸 수 있게 해준다.

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

`group` 네임스페이스에서 그룹을 생성하기 위해 `create`를 부르고 두번째 파라미터로 그룹장을 생성하기 위해 `user/create`를 불렀다.

## 네임스페이스를 변경하면서 다른 네임스페이스를 사용하기

하나의 네임스페이스에서 다른 네임스페이스를 사용하는 일이 많기 때문에 네임스페이스를 변경함과 동시에 다른 네임스페이스를 `require` 할 수 있는 편의 기능이 제공된다.

```clojure
user=> (ns group (:require user))
nil
group=> 
```

`ns` 구분 뒤에 `(:require 네임스페이스심볼)`을 적어주면 된다.

`ns`는 안에 있는 심볼들을 평가하지 않도록 하는 구문이기 때문에 안에 있는 네임스페이스에 `'` 표시해주지 않아도 된다.

## 네임스페이스 나누기

프로그램이 커지면 네임스페이스도 하나로 부족하고 네임스페이스도 여러 계층으로 나눠 쓸수 있어야한다.

클로저는 자바의 패키지와 같이 네임스페이스를 `.`으로 나눠 쓸 수 있다.

```clojure
user=> (ns namespace-sample.handler.user)
nil
namespace-sample.handler.user=>
```

REPL에서 보기에는 별거 없어보이지만 실제 파일로 만들

