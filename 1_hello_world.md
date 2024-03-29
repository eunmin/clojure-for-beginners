# 시작하기

화면에 `안녕하십니까`를 출력하는 Clojure 코드는 아래 처럼 생겼다.

```clojure
(println "안녕하십니까")
```

## 실행하기

위 코드를 실행하려면 준비물이 필요하다.

### 준비물

1.  Java 8, 11, 21

    ```
    brew tap homebrew/cask-versions
    brew install --cask temurin21
    ```

2.  Clojure Command Line Tools

    OSX는 homebrew([https://brew.sh](https://brew.sh))로 설치할 수 있다. Linux는 공식 안내 페이지([https://clojure.org/guides/getting\_started)에](https://clojure.org/guides/getting\_started\)%EC%97%90) 가서 안내대로 설치하면 되고 윈도우는 아직 커맨드 라인툴이 없다. 대신 Leiningen이나 Boot를 사용한다.

    ```
    brew install clojure
    ```

### 코드 실행 방법

여러가지 실행 방법이 있는데 하나씩 해보자.

1. REPL(Read Eval Print Loop)에서 실행하기
2. 파일에 쓴 코드를 Command Line Tools로 실행하기
3. Leiningen 프로젝트 관리툴로 실행하기
   1. 설치하기
   2. 프로젝트 만들기
   3. 바로 실행하기
   4. 컴파일 해서 java로 실행하기
   5. jar로 패키징 해서 java로 실행하기

### REPL(Read Eval Print Loop)에서 실행하기

Clojure Command Line Tools을 설치하면 REPL을 쓸 수 있다.

터미널에서 아래와 같은 명령어로 실행하면 REPL이 실행된다.

```bash
$ clj
```

REPL은 클로저 코드를 입력하면 실행 결과가 바로 나온다. (\* 출력 결과에 nil이 나오는데 println 함수의 리턴 값이다.)

```bash
Clojure 1.9.0
user=> (println "안녕하십니까")
Hello World
nil
user=>
```

클로저는 REPL에서 바로 실행할 수도 있지만 자바 클래스 파일로 컴파일해서 실행할 수 있다. 컴파일 해서 실행하는 방법을 알아보자.

### 파일에 쓴 코드를 실행하기

`hello.clj` 파일을 만들고 아래 코드를 입력하자.

```
(println "안녕하십니까")
```

커맨드 라인에서 아래와 같이 실행해보자.

```
$ clj hello.clj
Hello World
$
```

### Leiningen 프로젝트 관리툴로 실행하기

Clojure 세상에도 다른 언어들처럼 프로젝트 관리툴이 있다. 가장 많이 사용하고 있는 툴은 Leiningen([http://leiningen.org](http://leiningen.org))이다. 다음으로 많이 사용하는 툴은 Boot([http://boot-clj.com](http://boot-clj.com))지만 아직 대부분의 사람들이 Leiningen을 사용하고 있다. 그래서 문서에서는 Leiningen을 사용해서 설명한다.

#### 설치하기

Homebrew로 설치할 수 있다.

```
$ brew install leiningen
```

#### 프로젝트 만들기

leiningen을 설치하면 lein이라는 커맨드 라인 툴을 쓸 수 있다. 프로젝트는 lein의 new 커맨드로 만들 수 있다.

```
$ lein new hello
```

\*만약 프로젝트 이름에 자바에서 패키지 이름으로 쓸 수 없는 문자(JavaLetter가 아닌 문자 [http://docs.oracle.com/javase/specs/jls/se8/html/jls-3.html#jls-JavaLetter](http://docs.oracle.com/javase/specs/jls/se8/html/jls-3.html#jls-JavaLetter))가 있다면 규칙([https://github.com/clojure/clojure/blob/master/src/jvm/clojure/lang/Compiler.java#L2844](https://github.com/clojure/clojure/blob/master/src/jvm/clojure/lang/Compiler.java#L2844))에 따라서 디렉토리나 파일명이 바뀐다. 예를 들면 프로젝트명에 `-`가 있다면 `_`로 바뀐다.

`hello`라는 디렉토리가 만들어 졌고 아래 처럼 생겼다.

```
hello
├── CHANGELOG.md
├── LICENSE
├── README.md
├── doc
│   └── intro.md
├── project.clj
├── resources
├── src
│   └── hello
│       └── core.clj
└── test
    └── hello
        └── core_test.clj
```

중요한 몇 개 파일만 살펴보면 `project.clj` 파일이 프로젝트 버전, 사용하는 라이브러리, 소스 파일 위치, 각종 옵션을 나타내는 파일이다.

`src/hello/core.clj`는 leiningen이 만든 샘플 소스 파일이고 `test/hello/core_test.clj`는 역시 샘플 테스트 파일이다.

샘플 소스 고쳐보기

아무 편집기를 열어서 `src/hello/core.clj` 파일을 열어 다 지우고 아래 처럼 고쳐보자. (\*괄호에 주의)

```clojure
(ns hello.core)

(defn -main [] (println "안녕하십니까"))
```

코드를 간단히 살펴보면 ns 라는 구문은 네임스페이스를 선언하는 구문이다. defn은 함수를 정의 하는 것이고 프로그램이 실행되는 지점인 `-main` 함수를 정의 했다. (\* main 앞에 - 기호가 있는 것에 주의)

#### 바로 실행하기

`lein run` 명령어로 `src/hello/core.clj` 파일을 바로 실행할 수 있다. -m 옵션의 인자로 네임스페이스를 지정해 주면 된다. (\* `project.clj` 파일이 있는 곳에서 실행해야한다.)

```
$ lein run -m hello.core
안녕하십니까
```

#### 컴파일 해서 java로 실행하기

Clojure는 jvm에서 돌아가기 때문에 Clojure 코드를 class 파일로 컴파일해서 java로 실행할 수 있다.

**컴파일하기**

`lein compile` 명령어로 `src/hello/core.clj` 파일을 컴파일 할 수 있다. 인자로 네임스페이스를 지정해 주면 된다. (\* `project.clj` 파일이 있는 곳에서 실행해야한다.)

```
$ lein compile hello.core
Compiling hello.core
```

컴파일이 되면 `target/classes` 디렉토리 아래 클래스 파일이 생성된다.

```
target/classes
├── META-INF
│   └── maven
│       └── hello
│           └── hello
│               └── pom.properties
└── hello
    ├── core$_main.class
    ├── core$fn__38.class
    ├── core$loading__5569__auto____36.class
    └── core__init.class
```

네임스페이스는 자바의 패키지 구조와 같이 디렉토리, 파일명 제약을 가진다. `target/classes/hello` 디렉토리 아래 클래스가 여러개 생긴 것을 볼 수 있다. 하지만 어떤 클래스가 실행 가능한? 클래스인지 알 수 없다.

**실행하기**

Clojure는 기본적으로 네임스페이스 단위로 class를 만들지 않고 function 단위로 class를 만든다. java로 class를 실행하기 위해서는 자바 main 함수가 있는 클래스를 만들어야한다. 네임스페이스를 클래스로 만드려면 ns 구문에 `:gen-class` 라는 지시문을 넣어주면 된다. (\* 그냥 실행할 때 :gen-class 지시어가 필요없는 이유는 클래스를 생성하지 않고 동적으로 evaluation 하기 때문이다.) `src/hello/core.clj` 파일을 아래 처럼 고쳐서 다시 컴파일 해보자.

```clojure
(ns hello.core (:gen-class))

(defn -main [] (println "안녕하십니까"))
```

```
$ lein compile hello.core
Compiling hello.core
```

컴파일이 되면 이제 `target/classes/hello` 디렉토리 아래 `core.class`가 생긴것을 볼 수 있다.

이제 java로 실행해보자. `core.class`는 `clojure.jar` 의존성을 가지고 있기 때문에 classpath에 `clojure.jar`를 주면 된다. 다행이 leiningen이 처음 실행하면서 `clojure.jar`를 로컬 `~/.m2`에 다운로드 해놨기 때문에 (\* 아직 clojure 1.8.0을 쓰고 있지만...) `~/.m2` 아래 있는 `clojure.jar`를 classpath에 주면 된다.

```
$ java -cp ~/.m2/repository/org/clojure/clojure/1.8.0/clojure-1.8.0.jar:./target/classes hello.core
안녕하십니까
```

#### jar로 패키징 해서 java로 실행하기

java 애플리케이션은 jar로 의존성 파일들과 함께 패키징해서 사용하면 편리하다. leiningen으로도 jar 패키징을 할 수 있다. leiningen은 기본적으로 jar 패키징 할 때 컴파일을 하지 않고 `.clj` 파일 그대로 jar로 묶는다. 보통 clojure 라이브러리들은 컴파일 되지 않은 소스 파일 형태로 패키징 된다. 그래서 leiningen으로 패키징 할때 .`clj` 파일을 컴파일 하려면 `project.clj`에 다음과 같이 `:profiles {:uberjar {:aot :all}}` 옵션을 추가해줘야한다.

```clojure
(defproject hello "0.1.0-SNAPSHOT"
  :description "FIXME: write description"
  :url "http://example.com/FIXME"
  :license {:name "Eclipse Public License"
            :url "http://www.eclipse.org/legal/epl-v10.html"}
  :dependencies [[org.clojure/clojure "1.8.0"]]
  :profiles {:uberjar {:aot :all}})
```

`lein uberjar` 명령어로 jar로 패키징 한다.

```
$ lein uberjar
Compiling hello.core
Created .../hello/target/hello-0.1.0-SNAPSHOT.jar
Created .../hello/target/hello-0.1.0-SNAPSHOT-standalone.jar
```

`target` 디렉토리 아래 jar 파일이 두개 생기는데 `-standalone.jar` 으로 끝나는 파일이 의존성과 함께 묶인 jar다. 이제 java로 실행해보자.

```
$ java -cp ./target/hello-0.1.0-SNAPSHOT-standalone.jar hello.core
안녕하십니까
```

jar 파일을 묶을 때 main 함수가 있는 네임스페이스를 지정해 주면 java jar 옵션으로 실행할 수 있다. main 함수가 있는 네임스페이스는 `project.clj`에 다음과 같이 지정할 수 있다.

```
(defproject hello "0.1.0-SNAPSHOT"
  :description "FIXME: write description"
  :url "http://example.com/FIXME"
  :license {:name "Eclipse Public License"
            :url "http://www.eclipse.org/legal/epl-v10.html"}
  :dependencies [[org.clojure/clojure "1.8.0"]]
  :profiles {:uberjar {:aot :all}}
  :main hello.core)
```

다시 jar를 묶고 java jar 옵션으로 실행해보자.

```
$ lein uberjar
Compiling hello.core
Created .../hello/target/hello-0.1.0-SNAPSHOT.jar
Created .../hello/target/hello-0.1.0-SNAPSHOT-standalone.jar
$ java -jar ./target/hello-0.1.0-SNAPSHOT-standalone.jar 
안녕하십니까
```

`project.clj`에 `:main`을 지정해주면 `lein run`으로 실행할때 `main`함수가 있는 네임스페이스를 지정해주지 않아도 된다.

```
$lein run
안녕하십니까
```

## 정리

### Clojure 코드의 모양

```clojure
(println "안녕하십니까")
```

* 클로저 코드는 괄호`()`안에 들어 있다.
* 괄호안에 들어 있는 구문들은 띄어쓰기로 구분된다. `(구문 구문 구문 구문)`
* 따옴표 `""`안에 있는 것은 문자열이다.
* 첫번째 구문은 함수로 동작하고 두번째 구문 부터는 함수의 파라미터로 동작한다.
* 결국 Hello World 코드는 `println` 함수를 `"Hello World"`라는 하나의 파라미터를 가지고 실행하는 코드다.
* 한글을 쓸 수 있다.

### 파일로 만든 Clojure 코드의 모양

```clojure
(ns hello.core (:gen-class))

(defn -main [] (println "Hello World"))
```

* `()`는 중첩될 수 있다. (\* 안쪽 괄호부터 계산 된다)
* `ns`는 파일의 네임스페이스라고 부르는 것을 지정하는 구문으로 자바의 패키지와 유사하다.
* `example.hello` 네임스페이스는 자바의 패키지 처럼 디렉토리 구조와 파일명을 `hello/core.clj`으로 제한된다. 다르면 자바처럼 컴파일 에러가 난다.
* `defn`는 함수를 정의하는 구문으로 여러개의 파라미터를 가진다. 위에서는 첫번째 파라미터로 `-main`, 두번째 파라미터로 `[]`, 세번째 파라미터로 `(println "안녕하십니까")`를 가진다.
* `-main`이라는 이름의 함수는 클로저에서 특별하게 자바의 main 함수를 만들어 준다.
* 두번째 파라미터인 `[]`는 함수의 인자를 지정하는 것으로 위에서는 인자가 없다는 뜻이다. 인자가 있다면 `[x y]`등으로 인자 이름을 나열해준다. 타입은 없다.
* 함수에 마지막 실행 결과가 리턴 값이다.
* 컴파일 결과 클래스를 생성하기 위해서 네임스페이스 두번째 인자로 `(:gen-class)`구문을 넣었다.
* Clojure는 함수 단위로 컴파일된 클래스를 생성한다.

## 편집기

클로저 코드를 편집할때는 중첩된 괄호가 많이 사용되기 때문에 구조적 에디팅(structual editing)이 필수다.\
또 작업 중에 REPL을 많이 사용하기 때문에 편집기 안에서 실행할 수 있어야한다.

### Emacs

Clojure 개발자가 가장 많이(2016년 기준 [http://blog.cognitect.com/blog/2017/1/31/state-of-clojure-2016-results](http://blog.cognitect.com/blog/2017/1/31/state-of-clojure-2016-results)) 사용하는 에디터다. cider([https://github.com/clojure-emacs/cider](https://github.com/clojure-emacs/cider))와 clojure-mode를 조합해서 사용한다. clojure 관련 패키지들도 많이 있다.([https://github.com/clojure-emacs](https://github.com/clojure-emacs))

### Cursive

Cursive([https://cursiveclojure.com](https://cursiveclojure.com))는 IntelliJ 기반의 플러그인으로 많이 사용된다.

### Atom

처음 사용하기 좋은 에디터로 아래와 같은 플러그인을 설치해서 사용하면 된다.

* Parinfer
* linter-clojure
* language-clojure

### 기타

nightcode(\[https://sekao.net/nightcode/]\(https://sekao.net/nightcode/%29), lighttable(\[http://lighttable.com/]\(http://lighttable.com/%29), vim, sublimetext, eclipse 대부분의 편집기는 클로저 플러그인을 제공한다.

## 커뮤니티

Facebook Clojure Korea ([https://www.facebook.com/groups/defnclojure](https://www.facebook.com/groups/defnclojure))

Facebook Clojure Bridge Seoul ([https://www.facebook.com/groups/clojurebridge.seoul](https://www.facebook.com/groups/clojurebridge.seoul))

Clojure Korea Slack ([https://clojure-korea.slack.com](https://clojure-korea.slack.com))
