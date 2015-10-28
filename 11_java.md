# 자바 사용하기

클로저는 JVM 안에서 동작하는 언어기 때문에 자연스럽게 자바를 사용할 수 있다.
따라서 기존 자바라이브러리를 모두 사용할 수 있다.

물론 자바스크립트로 변환이되는 클로저스크립트는 다르다.
클로저스크립트는 대신 자바스크립트 라이브러리들을 사용할 수 있지만 여기서 다루지 않는다.

클로저에서 자바를 사용한다는 말은 다음과 같다.
- 자바의 클래스를 사용
- 자바의 클래스를 생성

또 자바에서 클로저를 사용할 수도 있다.

그럼 위해서 나열한 기능들을 예제를 통해 하나씩 살펴 보자.

## 클로저에서 자바 클래스 사용하기

### 자바 인스턴스 생성하기

`new` 구문으로 클로저에서 자바 인스턴스르 생성할 수 있다.

```bash
user=> (new java.util.Random)
#object[java.util.Random 0x380a1372 "java.util.Random@380a1372"]
```

더 간편하고 자주 사용하는 특별한 문법이 있는데 자바 클래스명 뒤에 `.`을 붙여 생성하는 방법이다.

```bash
user=> (java.util.Random.)
#object[java.util.Random 0x3fa8678e "java.util.Random@3fa8678e"]
```

기본 생성자 말고 다른 생성자로 인스턴스를 생성하려면 `.` 구문 뒤에 생성자 인자를 넣으면 된다.

```bash
user=> (def seed 1000)
#'user/seed
user=> (java.util.Random. seed)
#object[java.util.Random 0x21f8c591 "java.util.Random@21f8c591"]
```

### 자바 메서드 호출 하기

생성된 인스턴스의 메서드를 호출하려면 `(.메서드명 인스턴스)` 형태로 사용한다.

```bash
user=> (def random (java.util.Random.))
#'user/random
user=> (.nextInt random)
682185547
```

메서드의 인자는 인스턴스 다음으로 순서대로 넣어준다.

```bash
user=> (.nextInt random 10)
5
```

`.` 구문과 메서드명을 띄어서 사용 할 수도 있지만 잘 사용되지 않는다.

### 인스턴스 상태

클로저의 기본 데이터들은 상태를 가지지 않는다고 설명 했다.
값을 설정하면 기존 값은 변하지 않고 변경된 값을 가지는 새로운 값이 리턴 되는 식이다.
그럼 자바 인스턴스도 그렇게 동작할까?
그렇지 않다. 자바 인스턴스는 인스턴스를 직접 접근하기 때문에 상태를 가진다.

다음 예제를 보자.

```bash
user=> (def date (java.util.Date. 0))
#'user/date
user=> date
date
#inst "1970-01-01T00:00:00.000-00:00"
```

`java.util.Date` 생성자에 0을 주고 `epoch` 시간 0를 생성했다.
`Date` 클래스의 `setTime` 메서드로 10초가 지난 시간으로 변경하고 확인해보자.

```bash
user=> (.setTime date 10)
nil
user=> date
#inst "1970-01-01T00:00:00.010-00:00"
```

확인해보니 10초가 지난 `epoch` 시간을 가지고 있는 것을 알 수 있다.
결국 기존 `date` 인스턴스의 내부 값이 변경된 것이다.

### static 메서드 호출 하기

자바의 static 메서드는 인스턴스를 생성하지 않고 호출 하기 때문에 사용 방법이 조금 다르다.

```bash
user=> (System/currentTimeMillis)
1428242247005
```

### static 필드 사용하기

static 필드도 static 메서드와 비슷하지만 함수 호출이 아닌 값 형식으로 사용한다.

```bash
user=> java.lang.Long/MAX_VALUE
9223372036854775807
```

### 자바 클래스 import 하기

자바 클래스를 사용할 때 항상 전체 패키지 명을 써야한다면 번거롭다.
그래서 자바 처럼 `import`를 사용해서 현재 네임스페이스에서 클래스명으로만 사용할 수 있다.

```clojure
(import 'java.util.Random)

(Random.)
```

`import`는 `require`처럼 `ns` 폼에서 사용할 수 있다.

```clojure
(ns user
  (:import [java.util Date GregorianCalendar]))
```

`:import` 뒤에 클래스명을 제외한 패키지명이 오고 뒤로 그 패키지에서 사용할 클래스명들이 온다.

### 클로저에 기본 데이터

클로저에서 기본으로 사용하는 많은 데이터들은 자바의 인스턴스다. (자바에서 제공하는 기본 클래스도 있고 클로저에서 제공하는 클래스도 있다.)

```bash
user=> (class random)
java.util.Random
user=> (class "eunmin")
java.lang.String
user=> (class 100)
java.lang.Long
user=> (class true)
java.lang.Boolean
```

따라서 해당 클래스의 메서드를 사용할 수 있다.

```bash
user=> (.length "eunmin")
6
user=> (.toString 10)
"10"
```

## 자바 클래스 만들기

만약 클로저에서 특정 클래스를 상속 받거나 구현된 인스턴스가 필요하다면 `proxy` 구문을 사용하면 된다.

아래는 `java.lang.Thread`를 상속 받고 `run` 메서드를 오버라이딩하고 `start` 메서드로 쓰레드를 실행하는 예제다.

```clojure
user=> (def thread
         (proxy [java.lang.Thread] []
           (run []
             (println "Thread:" (.getName (Thread/currentThread))))))
#'user/thread
user=> (.getName (Thread/currentThread))
"nREPL-worker-1"
user=> (.start thread)
Thread: Thread-15
nil
```

사실 클로저의 함수는 자바의 `Runnable` 인터페이스를 구현하고 있기 때문에 함수를 별도의 쓰레드로 실행하기 쉽다.

```bash
user=> (instance? java.lang.Runnable (fn []))
true
user=> (.getName (Thread/currentThread))
"nREPL-worker-2"
user=> (.start (Thread. (fn [] (println "Thread:" (.getName (Thread/currentThread))))))
Thread: Thread-18
nil
```

## 자바에서 클로저를 사용하기

자바에서 클로저를 사용할 수 있는 방법은 두가지가 있는데 먼저 클래스를 생성하는 방법이 있다.
맨 처음 Hello World 예제에서 클로저를 컴파일해서 클래스 파일을 만드는 법에 대해서 알아봤다.
하지만 그 클래스 파일은 클로저에서 실행하기 위한 목적이 였고 자바에서 사용하기 위해서는 정말 자바 클래스 모양이어야 한다.
다음은 자바의 `com.eunmin.Calc` 클래스를 만들고 두 수를 더하는 `add`라는 메서드를 추가하는 예제다.

```clojure
(ns com.eunmin.calc
  (:gen-class
   :name com.eunmin.Calc
   :methods [[add [int int] int]]))

(defn -add [this x y]
  (+ x y))
```

위의 예제를 보면 `:gen-class`에 `:name`과 `:methods`가 있는 것을 볼 수 있다.
`:name`은 실제 생성될 클래스 이름이다. 여기서는 Calc 클래스를 대문자로 사용하기 위해 써줬다.
`:methods`는 클래스에 포함될 메서드 시그네처를 추가하는 것이다.
대략 알아보면 `add`라는 메서드명과 숫자 두개를 받아 숫자를 리턴해 줄것 이라고 짐작할 수 있다.
static 메서드에 대한 정의 방법도 있는데 자세한 내용은 메뉴얼을 참고 하자.
실제 메서드의 구현은 메서드 이름 앞에 `-`가 붙고 항상 첫번째 파라미터로 인스턴스 자신이 넘어오도록 만들어야한다.

이 클래스를 사용 할 `Main.java`는 다음과 같다.

```java
import com.eunmin.Calc;

public class Main {
  public static void main(String[] args) {
    Calc calc = new Calc();
    System.out.println("1 + 2 = " + calc.add(1, 2));
  }
}
```

일반 자바 클래스를 사용하는 것과 다른 점은 없다.

이제 이 클로저 파일을 컴파일 하고 자바에서 실행해보자.
컴파일 과정은 첫번째 Hello World 예제에서 했던 것처럼 `leiningen`을 사용하지 않고 수동으로 해보자.

준비할 디렉토리 구성은 아래와 같다.

```bash
$ tree
.
├── Main.java
├── classes
├── clojure-1.7.0.jar
└── com
    └── eunmin
        └── calc.clj

3 directories, 3 files
```

먼저 `calc.clj` 파일을 컴파일 하자. 컴파일을 repl을 띄워서 한다.

```bash
$ java -cp .:clojure-1.7.0.jar clojure.main
Clojure 1.7.0
user=> (compile 'com.eunmin.calc)
com.eunmin.calc
```

컴파일을 하면 `classes` 디렉토리 아래 패키지 명의 구조 대로 파일이 생성된다.
다음은 `Main.java`를 컴파일 한다. `Main.java`는 방금 생성한 클래스에 의존적이기 때문에 클래스패스에 `classes` 경로를 준다.

```bash
$ javac -cp ./classes Main.java
```

`Main.java`는 따로 패키지를 주지 않았기 때문에 현재 디렉토리에 클래스가 생성된다.

이제 `Main.class`를 실행해보자. `Main.class`는 먼저 만든 `Calc`클래스에 의존적이기 때문에 클래스패스에 `classes` 경로를 준다.
또 `Calc` 클래스는 `clojure.jar`에 의존적이기 때문에 클래스패스에 `clojure.jar`도 준다.

```bash
java -cp .:./classes:clojure-1.7.0.jar Main
1 + 2 = 3
```

잘 실행되는 것을 볼 수 있다.

## 자바에서 클로저를 사용하는 다른 방법

자바에서 클로저를 사용하기 위해 클래스를 생성했는데 클로저 자바 API를 이용해서 사용하면 간단하다.

```java
IFn plus = Clojure.var("clojure.core", "+");
plus.invoke(1, 2);
```

자세한 내용은 클로저 메뉴얼을 참고하자.
