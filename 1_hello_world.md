# Hello World

클로저로 Hello World를 만들어 보자.

```clojure
(println "Hello World")
```

클로저 Hello World 코드는 위와 같이 생겼다.

## 실행하기

위에서 작성한 코드를 실행하려면 준비물이 필요하다.

### 준비물

1. JVM 1.6 이상

   알아서 설치한다.

2. Clojure Command Line Tools 

   OSX는 homebrew\(https://brew.sh/\)로 설치할 수 있다. Linux는 공식 안내 페이지\(https://clojure.org/guides/getting\_started\)에 가서 안내대로 설치하면 되고 윈도우는 아직 커맨드 라인툴이 없다. 대신 Leiningen이나 Boot를 사용한다.

   ```
   brew install clojure
   ```

### 코드 실행 방법

여러가지 실행 방법이 있는데 하나씩 해보자.

1. REPL\(Read Eval Print Loop\)에서 실행하기
2. 파일에 쓴 코드를 Command Line Tools로 실행하기
3. 컴파일된 클래스를 java로 실행하기
4. 클로저 프로젝트를 만들어서 실행하기

### REPL\(Read Eval Print Loop\)에서 실행하기

Clojure Command Line Tools을 설치하면 REPL을 쓸 수 있다.

터미널에서 아래와 같은 명령어로 실행하면 REPL이 실행된다.

```bash
$ clj
```

REPL은 클로저 코드를 입력하면 실행 결과가 바로 나온다.

```bash
Clojure 1.9.0
user=> (println "Hello World")
Hello World
nil
user=>
```

클로저는 REPL에서 바로 실행할 수도 있지만 자바 클래스 파일로 컴파일해서 실행할 수 있다.  컴파일 해서 실행하는 방법을 알아보자.

### 파일에 쓴 코드를 실행하기

`hello.clj` 파일을 만들고 아래 코드를 입력하자.

```
(println "Hello World")
```

커맨드 라인에서 아래와 같이 실행해보자.

```
$ clj hello.clj
Hello World
$
```

### 컴파일된 클래스를 java로 실행하기

`example`라는 디렉토리를 만들고 그 안에 `hello.clj` 파일을 만들어 아래와 같이 코드를 입력하고 저장한다.

```clojure
(ns example.hello (:gen-class))

(defn -main [] 
  (println "Hello World"))
```

뭐가 뭔지 모르겠지만 중간에 `(println "Hello World")`가 보인다.

주의할점이 하나 있는데 만약 `example`이라는 이름 대신 `-`와 같이 자바 클래스명 또는 패키지명에서 지원하지 않는 문자가 있다면 다른 문자로 대치해서 작성해야한다.  
\([http://docs.oracle.com/javase/specs/jls/se8/html/jls-3.html\#jls-JavaLetter](http://docs.oracle.com/javase/specs/jls/se8/html/jls-3.html#jls-JavaLetter)\)

예를 들어 `example` 대신 `clojure-example`이라고 만드려면 `clojure_example`이라고 디렉토리를 만들어야한다.

다른 문자들은 아래 코드를 참조하면 된다.

[https://github.com/clojure/clojure/blob/bfe14aec1c223abc3253358bac34b503284467d9/src/jvm/clojure/lang/Compiler.java\#L2819](https://github.com/clojure/clojure/blob/bfe14aec1c223abc3253358bac34b503284467d9/src/jvm/clojure/lang/Compiler.java#L2819)

#### 컴파일 하기

먼저 받은 Clojure JAR에는 컴파일 기능이 들어있다. 하지만 컴파일러를 별도의 바이너리로 제공하지는 않고 함수 형태로 제공한다. 따러서 REPL이나 다른 프로그램에서 컴파일 함수를 호출해줘야한다.

컴파일을 위해 다시 REPL을 실행한다. REPL을 실행할 때 처음과 다른 부분이 있는데 소스파일을 찾기위해 현재 디렉토리를 클래스 패스로 추가해주는 부분이 있다.

```bash
mkdir classes
java -cp .:clojure-1.8.0.jar clojure.main
Clojure 1.8.0
user=> (compile 'example.hello)
example.hello
```

`(compile 'example.hello)`를 입력해서 컴파일을 한다.

에러 없이 컴파일 되었다면 먼저 만들어 둔 `classes` 디렉토리 아래 `class`파일들이 여러개 생긴다.

#### 컴파일된 class 실행하기

자바 클래스로 컴파일 되기 때문에 `java`로 실행할 수 있다.

클래스패스에 컴파일된 클래스가 있는 `classes` 디렉토리와 Clojure Runtime이 있는 Clojure JAR파일을 주고 실행하자.

```bash
java -cp classes:clojure-1.8.0.jar example.hello
Hello World
```

### 클로저 프로젝트를 만들어서 실행하기

자바의 메이븐 프로젝트와 같이 클로저도 프로젝트를 관리해주는 도구가 있다. 가장 유명한 프로젝트 관리 도구는 Leiningen\([http://leiningen.org](http://leiningen.org)\)이라는 것이 있고 좀 덜 유명하지만 뜨고 있는 Boot\([http://boot-clj.com](http://boot-clj.com)\)라는 프로젝트 관리 도구도 있다. 여기서는 Leiningen으로 실행하는 것만 알아보자.

#### Leiningen 설치하기

Leiningen은 스크립트 파일 형태로 되어 있어서 파일을 다운로드하고 실행권한을 주고 실행하면 된다.

어디서나 실행가능하게 PATH에 추가해주면 편리하다.

```bash
wget https://raw.githubusercontent.com/technomancy/leiningen/stable/bin/lein
chmod +x ./lein
```

#### Homebrew로 Leiningen 설치하기

Homebrew\([https://brew.sh/index\_ko.html\)로](https://brew.sh/index_ko.html%29로) Leiningen을 설치할 수  있다.

```
brew update
brew install leiningen
```

#### Leiningen 프로젝트 생성

```bash
lein new hello
```

위의 명령어를 실행하면 `hello` 디렉토리가 생기고 그 안에 다음과 같은 파일들이 생긴다.

```bash
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

`project.clj`파일은 메이븐 `pom.xml`와 비슷한 기능을한다.

`src/hello/core.clj`는 샘플 클로저 파일이다.

`test/hello/core_test.clj`는 샘플 클로저 테스트 파일이다.

#### `src/hello/core.clj` 수정하기

이 파일에 Hello World를 작성한다.

기본으로 만들어 준 내용은 지우고 아래와 같이 코드를 작성한다.

```clojure
(ns hello.core)

(defn -main [] (println "Hello World"))
```

#### lein으로 실행하기

프로젝트 최상위 디렉토리\(`hello` 디렉토리\)에서 아래 명령어를 실행한다.

```bash
lein run -m hello.core
Hello World
```

#### lein으로 컴파일하기

Leiningen은 컴파일 및 패키징 기능도 제공한다.

`lein`은 서버 커맨드로 `compile`, `jar`, `uberjar`를 제공하고 있다.

`compile`은 소스 디렉토리로 지정된 곳에 있는 클로저 파일들을 컴파일해준다.

`jar`는 파일들을 jar로 묶어준다.

`uberjar`는 의존하고 있는 jar들과 함께 jar로 묶어준다.

기본적으로 `jar`와 `uberjar` 패키징 커맨드는 컴파일을 하지 않고 `clj`파일을 jar로 묶는다.  
클로저의 라이브러리들은 `jar`로 Clojars 같은 곳으로 배포가 되는데 보통 소스 파일\(`clj`\)형태로  
묶어 배포를 한다. 그리고 라이브러리를 사용하는 어플리케이션에서 소스파일을 컴파일해서 클래스 형태로  
만든다. 클래스로 배포하지 않는 이유는 자바의 버전 문제등 여러가지 문제에 더 자유롭기 때문일 것이다.

패키징시 클로저 소스파일을 컴파일 하기 위해서는 `project.clj`에 설정을 추가해줘야한다.

```clojure
:profiles {:uberjar {:aot :all}}
```

을 추가한다. Leiningen은 lein 명령어를 실행하는 환경별로 다른 설정을 줄 수 있도록 하는 `profile`이라는 기능을 제공한다.

위 설정은 `uberjar` 프로필로 실행되는 `lein`명령어에 `:aot :all` 옵션을 추가한 것이다.  
모든 소스파일을 컴파일 해서 클래스로 만드라는 옵션이다.

```clojure
(ns hello.core
  (:gen-class))

(defn -main [] (println "Hello World"))
```

기존 코드에 `:gen-class` 옵션을 추가해 자바 엔트리 포인트를 만들어 주고 아래와 같이 패키징을 해보자.

```bash
$lein uberjar
Compiling hello.core
Created ..../hello/target/hello-0.1.0-SNAPSHOT.jar
Created ..../hello/target/hello-0.1.0-SNAPSHOT-standalone.jar
```

`jar`와 `standalone` `jar`가 생겼다. 그냥 `lein jar`는 위에 있는 `jar`만 생긴다.  
위에 있는 `jar`는 실행할 때 의존하고 있는 다른 클래스들을 클래스패스에 추가하고 실행해야한다.  
`standanlone` `jar`는 의존하고 있는 클래스를 모두 포함하고 있어 크기는 크지만 독립적으로 실행 가능하다.

```bash
$ java -cp target/hello-0.1.0-SNAPSHOT-standalone.jar hello.core
Hello World
```

실행 결과가 잘 나오는 것을 볼 수 있다.

jar에 main 함수가 있는 엔트리 포인트를 지정해주면 `java -jar` 로 실행 할 수 있다.

`project.clj`에 아래와 같이 추가한다.

```
:main hello.core
```

다시 jar로 패키징해서 실행해보자.

```
$lein uberjar
Compiling hello.core
Created ..../hello/target/hello-0.1.0-SNAPSHOT.jar
Created ..../hello/target/hello-0.1.0-SNAPSHOT-standalone.jar

$java -jar target/hello-0.1.0-SNAPSHOT-standalone.jar
Hello World
```

`project.clj`에 `:main`을 지정해주면 `lein run`으로 실행할때 `main`함수가 있는 위치를 지정해주지 않아도 된다.

```
$lein run
Hello World
```

## 지금 한 일 설명

### Hello World 코드의 모양

```clojure
(println "Hello World")
```

* 클로저 코드는 괄호`()`안에 들어 있다.
* 괄호안에 들어 있는 구문들은 띄어쓰기로 구분된다. `(구문 구문 구문 구문)`
* 따옴표 `""`안에 있는 것은 문자열이다.
* 첫번째 구문은 함수로 동작하고 두번째 구문 부터는 함수의 파라미터로 동작한다. 
* 결국 Hello World 코드는 `println` 함수를 `"Hello World"`라는 하나의 파라미터를 가지고 실행하는 코드다.

### 처음 만든 Hello World 파일 코드

```clojure
(ns example.hello (:gen-class))

(defn -main [] (println "Hello World"))
```

* `()`는 중첩될 수 있다.
* `ns`는 파일의 네임스페이스라고 부르는 것을 지정하는 구문으로 자바의 패키지와 유사하다.
* `example.hello` 네임스페이스는 자바의 패키지 처럼 디렉토리 구조와 파일명을 `example/hello.clj`으로 제한된다. 다르면 자바처럼  컴파일 에러가 난다.
* `defn`는 함수를 정의하는 구문으로 여러개의 파라미터를 가진다. 위에서는 첫번째 파라미터로 `-main`, 두번째 파라미터로 `[]`, 세번째 파라미터로 `(println "Hello World")`를 가진다.
* `-main`이라는 이름의 함수는 클로저에서 특별하게 자바의 main 함수를 만들어 준다.
* 두번째 파라미터인 `[]`는 함수의 인자를 지정하는 것으로 위에서는 인자가 없다는 뜻이다. 인자가 있다면 `[x y]`등으로 인자 이름을 나열해준다. 타입은 없다.
* 함수에 마지막 실행 결과가 리턴 값이다.
* 컴파일 결과 클래스를 생성하기 위해서 네임스페이스 두번째 인자로 \(:gen-class\)구문을 넣었다.
* 클로저는 함수 단위로 컴파일된 클래스를 생성한다.

## 생길 수 있는 궁금증

* repl에서 보였던 `nil`은? 널. 왜보였냐? repl은 함수의 리턴값을 표시해주고 `println`에 리턴 값이 `nil`이 였다.
* Leiningen으로 실행할 때는 `(:gen-class)`가 없었다? `lein run`는 내부에서 REPL을 실행하고 함수를 바로 부르기 때무에 클래스 파일을 만들지 않는다.

## 편집기

클로저 코드를 편집할때는 중첩된 괄호가 많이 사용되기 때문에 구조적 에디팅\(structual editing\)이 필수다.  
또 작업 중에 REPL을 많이 사용하기 때문에 편집기 안에서 실행할 수 있어야한다.

### Emacs

cider\([https://github.com/clojure-emacs/cider](https://github.com/clojure-emacs/cider)\)와 clojure-mode를 조합해서 사용한다. clojure 관련 패키지들도 많이 있다.\([https://github.com/clojure-emacs](https://github.com/clojure-emacs)\)

### Cursive

Cursive\([https://cursiveclojure.com](https://cursiveclojure.com)\)는 IntelliJ 기반의 플러그인으로 많이 사용된다.

### Atom

Atom\([https://atom.io\)은](https://atom.io/%29은) 아래와 같은 플러그인을 설치하면 도움이 된다.

* Parinfer
* linter-clojure
* language-clojure

### 기타

nightcode\([https://sekao.net/nightcode/\](https://sekao.net/nightcode/%29\), lighttable\([http://lighttable.com/\](http://lighttable.com/%29\), vim, sublimetext, eclipse 대부분의 편집기는 클로저 플러그인을 제공한다.

