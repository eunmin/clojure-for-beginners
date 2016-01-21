# 클로저 연습문제

1. "Hello World"를 출력하는 프로그램을 파일로 작성하고 실행해 보시오.

2. "Hello World"를 출력하는 프로그램을 Leiningen 프로젝트로 만들어서 `lein run`으로 실행해 보시오.
  - 힌트 : project.clj에 `:main` 키워드로 `-main` 함수가 있는 네임스페이스를 지정해주면
    `lein run`으로 편리하게 `-main` 함수를 실행 할 수 있습니다.
    ```clojure
    (defproject main-sample "0.1.0-SNAPSHOT"
      :description "FIXME: write description"
      :url "http://example.com/FIXME"
      :license {:name "Eclipse Public License"
                :url "http://www.eclipse.org/legal/epl-v10.html"}
      :dependencies [[org.clojure/clojure "1.7.0"]]
      :main main-sample.core)
    ```

3. 2번에서 작성한 프로젝트를 `lein`으로 패키징해서 `java`로 실행해 보시오.
  - 힌트 : `-main` 함수가 있는 (엔트리 포인트) 네임스페이스는 `(:gen-class)`를 줘서 시작 클래스를
    생성해 줘야 합니다.
    ```clojure
    (ns main-sample.core
      (:gen-class))

    (defn -main [& args]
      (println args))
    ```

4. 이름을 입력 받아 "Hello 이름"을 출력하는 프로그램을 작성하시오.
  - 힌트 : `lein run`으로 실행하는 함수는 가변 인자로 커맨드 라인 인자를 받을 수 있습니다.
      ```bash
      (defn -main [& args]
        (println args))
    
      $ lein run
      nil
      $ lein run 1 2
      (1 2)
      ```

5. 두 숫자를 입력 받아 합을 출력하는 프로그램을 작성하시오. (Leiningen 프로젝트 + `lein run`으로 실행)
  - 힌트 : 문자열을 숫자로 변한하기 위해서 `Long/parseLong` 함수를 사용합니다.
    ```bash
    user=> (Long/parseLong "13")
    13
    ```

6. 사칙 연산 문자열(`+`, `-`, `*`, `/`)과 두 수를 입력 받아 해당 연산을 출력하는 프로그램을 작성하시오.

7. 여러개의 숫자를 입력 받아 해당 숫자를 모두 더하는 프로그램을 작성하시오.
  - 힌트 : 가변 인자 함수를 벡터 인자로 실행할 수 있는 `apply` 함수가 있습니다.
    ```bash
    user=> (str "hello" " " "world")
    "hello world"
    user=> (apply str ["hello" " " "world"])
    "hello world"
    ```

8. 아래와 같은 파일을 만들어 `config.edn`으로 저장하고 `config.edn`안에 있는 `:name`을 출력하는
   프로그램을 작성하시오.

   ```clojure
   {:name "clojure" :author "Rich Hickey"}
   ```
   
   - 힌트1 : 파일을 읽어 문자열로 리턴해주는 `slurp` 함수가 있습니다.
    ```clojure
    user=> (slurp "config.txt")
    test
    ```
   - 힌트2 : edn 형식의 문자열을 읽어 클로저 맵으로 리턴해주는 `read-string`이라는 함수가 있습니다.
    ```clojure
    user=> (read-string "{:name \"eunmin\"}")
    {:name "eunmin"}
    user=> (class (read-string "{:name \"eunmin\"}"))
    clojure.lang.PersistentArrayMap
    ```
    
9. `id`(숫자), `name`(문자열), `age`(숫자), `role`(키워드, 종류는 `:developer`, `:manager`, `:designer`, `:tester`)을 
   데이터로 하는 사용자 데이터라고 하는 맵으로 만들어 출력하시오.

10. 9에서 만든 사용자 데이터를 여러개 가질 수 있는 `users`라는 백터를 만들어 사용자 여러명을 넣어 데이터를 만들어 출력하시오.

11. 10에서 만든 `users` 벡터에서 개발자의 나이를 합산하는 `sum-developer-age` 함수를 만들어보시오.

12. 10에서 만든 `users` 벡터에 마지막에 사용자를 추가하는 `add-user`라는 함수를 만들어 보시오.

13. 10에서 만든 users 벡터에 특정 id 사용자를 삭제하는 `remove-user`라는 함수를 만들어 보시오.

14. `map` 함수를 작성하시오.

15. `reduce` 함수를 작성하시오.

16. `filter` 함수를 작성하시오.

17. `example.util.string` 네임스페이스에 특정 문자의 위치를 찾는 `index-of` 함수를 만들고 
    `example.core` ``-main` 함수에서 "Hello World"에서 공백 문자를 찾는 예제를 작성하라.

18. Jedis 자바 라이브러리를 이용해 Redis에서 get/set 하는 클라이언트 프로그램을 작성하시오. 
  - 힌트 : 자바 라이브러리를 사용하기 위해 `project.clj`에 `:dependencies` 키에 `[redis.clients/jedis "2.8.0"]`를 추가해준다.
  - 힌트 : Jedis 사용법은 https://github.com/xetorthio/jedis 를    참조
  - 힌트 : `lein run set 키 값` 또는 `lein run get 키`로 사용할 수 있도록 만든다.
   

 
