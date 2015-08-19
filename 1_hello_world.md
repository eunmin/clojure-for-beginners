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

2. Clojure JAR 파일

    클로저 다운로드 페이지(<http://clojure.org/downloads>)에서 최신버전을 다운로드 한다. 글을 작성하는 시점에 최신 버전은 1.7.0 버전이다.
    
### 실행 방법

여러가지 실행 방법이 있는데 

1. REPL(Read Eval Print Loop)에서 실행하기
2. 파일에 작성된 코드를 실행하기
3. 클로저 프로젝트를 만들어서 실행하기
    
### REPL(Read Eval Print Loop)에서 실행하기

다운로드 받은 Clojure JAR에는 Clojure Runtime과 컴파일러 REPL등이 포함되어 있다. 

터미널에서 아래와 같은 명령어로 실행하면 REPL이 실행된다.

```bash
java -cp clojure-1.7.0.jar clojure.main
```

REPL은 클로저 코드를 입력하면 실행 결과가 바로 나온다.

```bash
user=> (println "Hello World")
Hello World
nil
user=>
```

### 파일에 작성된 코드를 실행하기

`example`라는 디렉토리를 만들고 그 안에 `hello.clj` 파일을 만들어 아래와 같이 코드를 입력하고 저장한다.

  ```clojure
(ns example.hello (:gen-class))

(defn -main [] (println "Hello World"))
```

뭐가 뭔지 모르겠지만 중간에 `(println "Hello World")`가 보인다.

클로저는 자바와 같이 컴파일해서 실행하는 언어기 때문에 컴파일을 해준다.

#### 컴파일 하기

먼저 받은 Clojure JAR에는 컴파일 기능이 들어있다. 하지만 컴파일러를 별도의 바이너리로 제공하지는 않고 함수 형태로 제공한다. 따러서 REPL이나 다른 프로그램에서 컴파일 함수를 호출해줘야한다. 

컴파일을 위해 다시 REPL을 실행한다. REPL을 실행할 때 처음과 다른 부분이 있는데 소스파일을 찾기위해 현재 디렉토리를 클래스 패스로 추가해주는 부분이 있다.

```bash
java -cp .:clojure-1.7.0.jar clojure.main
Clojure 1.7.0
user=> (compile 'example.hello)
example.hello
```

`(compile 'example.hello)`를 입력해서 컴파일을 한다.

에러 없이 컴파일 되었다면 먼저 만들어 둔 `classes` 디렉토리 아래 `class`파일들이 여러개 생긴다.

#### 컴파일된 class 실행하기

자바 클래스로 컴파일 되기 때문에 `java`로 실행할 수 있다.

클래스패스에 컴파일된 클래스가 있는 `classes` 디렉토리와 Clojure Runtime이 있는 Clojure JAR파일을 주고 실행하자.

```bash
java -cp classes:clojure-1.7.0.jar example.hello
Hello World
```

### 클로저 프로젝트를 만들어서 실행하기

자바의 메이븐 프로젝트와 같이 클로저도 프로젝트를 관리해주는 도구가 있다. 가장 유명한 프로젝트 관리 도구는 Leiningen(<http://leiningen.org>)이라는 것이 있고 좀 덜 유명하지만 뜨고 있는 Boot(<http://boot-clj.com>)라는 프로젝트 관리 도구도 있다. 여기서는 Leiningen으로 실행하는 것만 알아보자.

#### Leiningen 설치하기

Leiningen은 스크립트 파일 형태로 되어 있어서 파일을 다운로드하고 실행권한을 주고 실행하면 된다. 

어디서나 실행가능하게 PATH에 추가해주면 편리하다.


```bash
wget https://raw.githubusercontent.com/technomancy/leiningen/stable/bin/lein
chmod +x ./lein
```

#### Leiningen 프로젝트 생성

```bash
lein new hello
```

위의 명령어를 실행하면 `hello` 디렉토리가 생기고 그 안에 다음과 같은 파일들이 생긴다.

```bash
hello
├── LICENSE
├── README.md
├── doc
│   └── intro.md
├── project.clj
├── resources
├── src
│   └── hello
│       └── core.clj
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

프로젝트 최상위 디렉토리(`hello` 디렉토리)에서 아래 명령어를 실행한다.

```bash
lein run -m hello.core
Hello World
```

## Hello World 코드 설명


