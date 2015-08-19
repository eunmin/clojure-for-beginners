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

    클로저 다운로드 페이지 (http://clojure.org/downloads) 에서 최신버전을 다운로드 한다. 글을 작성하는 시점에 최신 버전은 1.7.0 버전이다.
    
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




