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

아래와 같은 코드를 `example/hello.clj` 파일 작성

  ```clojure
(ns example.hello (:gen-class))

(defn -main [] (println "Hello World"))
```





