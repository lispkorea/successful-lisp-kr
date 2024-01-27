# 레슨 11. 필수 - 입력과 출력(Essential Input and Output)

- [원문](https://dept-info.labri.fr/~strandh/Teaching/MTP/Common/David-Lamkins/chapter03-11.html)


## READ는 리스프 데이터를 받는다

 [레슨 10](./lesson_10.md)에서 봤던것처럼, `read`는 문자를 리스프 데이터로 변환시킵니다. 이제까지, 우리들은 리스프 데이터 여러 출력 값들을 살펴 보았습니다:

- 심볼과 숫자
- 문자, 문자열, 리스트, 배열, 백터, 구조체
- 해쉬태이블

 리스프 리더(reader)는 문자 분류법(classifications)를 따라 이와 같은 일을 수행합니다. 표준 분류법은 아래에 나와있습니다. [레슨 12](./lesson_12.md)에서 보게될 것처럼, 여러분은 필요에 따라 이러한 분류법을 바꿀 수도 있습니다.

``` txt
표준 구성 문자(Standard Constituent Characters)
-------------------------------
a b c d e f g h i j k l m n o p q r s t u v w x y z
A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
0 1 2 3 4 5 6 7 8 9
! $ % & * + - . / : < = > ? @ [ ] ^ _ { } ~
<backspace> <rubout>


표준 종료 매크로 문자(Standard Terminating Macro Characters)
-------------------------------------
" ' ( ) , ; ‘


표준 비-종료 매크로 문자(Standard Non-Terminating Macro Characters)
-----------------------------------------
#


표준 단일 이스케이프 문자(Standard Single Escape Characters)
---------------------------------
\


표준 다수 이스케이프 문자(Standard Multiple Escape Characters)
-----------------------------------
|


표준 공백 문자(Standard Whitespace Characters)
------------------------------
<tab> <space> <page> <newline> <return> <linefeed>
```

 `READ`가 구성문자(constituent character)로 시작된다면, 이는 심볼이나 숫자를 축적하기 시작합니다. `READ`가 종료 매크로 문자(terminating macro character)나 공백 문자(whitespace character)를 만나면, 이는 모아진 구성문자를 우선 숫자로, 그 다음 심볼로 해석하기 시작합니다. 숫자로 해석이 가능하다면 `READ`는 숫자를 반환합니다. 그렇지 않으면, `READ`는 영문자를 표준문자(standard case (보통은 대문자))로 바꾸어, 심볼로써 이름을 인턴(intern)하여 심볼을 반환합니다.

 이스케이프 문자(Escape characters)는 특별한 역활을 수행합니다. 하나의 이스케이프 문자는 다음에 나오는 문자를 마치 구성문자(constituent character)처럼 다루도록 합니다. 보통 공백이나 종료 매크로 문자로 취급되는 문자는 심볼의 일부가 될 수 있습니다. 이스케이프된 것이 숫자일 지라도, `READ`가 이스케이프 문자를 만나면 결과를 숫자로 해석하려 하지 않을 것입니다

 `READ`시 매크로 문자(macro character)로 시작한다면, 아래의 문자들이 다음 단계를 결정하게 됩니다:

| 문자 | 설명                                            |
| ---- | ----------------------------------------------- |
| "    | 문자열을 읽는다.                                |
| '    | 폼을 읽는다.                                    |
| (    | 리스트를 읽는다.                                |
| ;    | 새로운 라인을 만나기 전까지 모든 것을 무시한다. |
| #    | 다음 나오는 문자에 기반하여 무엇인지 결정한다.  |

 마지막으로, 몇몇 리스프 데이터는 읽을 수 없습니다. 예를들어, 해쉬테이블의 출력결과는 `#<HASH-TABLE>`와 같습니다. `#<` 문자로 시작하는 어떠한 것을 읽으려할때 `READ`는 에러를 뱉을 것입니다.


## PRINT는 여러분을 그리고 READ를 위한 리스프 데이터를 작성한다.

 `print` 함수는 리스프 오브젝트를 문자 시퀀스(sequence of characters)로 바꿉니다. 이 문자 시퀀스는 `READ`에서 재구성하는 데 필요로 하는 것입니다:

``` lisp
(print 'abc)
;;>> ⏎
;;>> ABC⌴
;;=> ABC

(print (list 1 2 3))
;;>> ⏎
;;>> (1 2 3)⌴
;;=> (1 2 3)

(print "A String")
;;>> ⏎
;;>> "A string"⌴
;;=> "A string"

(print 387.9532)
;;>> ⏎
;;>> 387.9532⌴
;;=> 387.9532

(print (make-hash-table))
;;>> ⏎
;;>> #<HASH-TABLE>⌴
;;=> #<HASH-TABLE>
```

 `PRINT`는 개행(⏎)으로 시작하고 띄어쓰기(⌴)으로 끝나는 결과물을 출력합니다. 이는 `PRINT` 출력이 다른 출력들과 구별되도록 만들어 줍니다. 개행과 띄어쓰기 모두 공백(whitespace)으로 취급되며, 이는 (이스케이프되지 않는 한) 리스프 오브젝트를 출력하는데 하는데 포함될 수 없습니다.

 `PRINT`와 비슷한 다양한 쓰임세를 지닌 다른 변종들이 있습니다. `PRIN1`은 `PRINT`와 비슷하게 행동하지만, 공백으론 감싸지 않습니다. 예를들어 여러 조각들로 이름을 만들고자 할때 유용하게 쓰일 것입니다. `PRINC`는 `PRIN1`처럼 행동하지만 `READ`를 위하기 보단, 유저에게 보여지는것을 위한 결과물을 생성합니다; 예를들어, `PRINC`는 문자열을 둘러싸는 쌍따옴표를 생략하며, 이스케이프 문자 역시 출력하지 않습니다.

``` lisp
(print 'a\ bc)
;;>> ⏎
;;>> |A BC|⌴
;;=> |A BC|

(prin1 'a\ bc)
;;>> |A BC|
;;=> |A BC|

(princ '|A BC|)
;;>> ⏎
;;>> A BC⌴
;;=> |A BC|
```


## OPEN과 CLOSE는 파일을 다룰 수 있게 한다

 보통, `READ`는 키보드를 읽고 `PRINT`는 화면에 출력합니다. 이러한 함수 모두 옵셔널(optional) 인수를 취합니다; 인수로 `READ`를 위한 입력 스트림과 `PRINT`를 위한 출력 스트림을 지정합니다. 스트림(stream)은 무엇일까요? 스트림은 데이터의 소스(source)이자 밑바닥(sink)이며, 보통은 (그치만 절대적이지는 않은) 문자들(characters)입니다. 이제부터, 저희는 텍스트 파일이 어떻게 문자 스트림의 소스가 될 수 있는지를 살펴볼 것입니다. 19장 [p 183]에서 저희는 몇몇 다른 가능성들을 살펴볼 것입니다.

 파일 이름을 인수로 취하고 스트림의 방향(입력과 출력)을 결정하는 키워드 인수를 취하는, `OPEN` 함수를 이용하여 스트림을 파일로 연결 시킬 수 있습니다. 스트림에 대한 작업을 끝내고 연결된 파일을 닫기 위해선 `CLOSE` 함수를 사용합니다.

``` lisp
(setq out-stream (open "my-temp-file" :direction :output))
;;=> #<OUTPUT-STREAM "my-temp-file">

(print 'abc out-stream)
;;=> ABC

(close out-stream)
;;=> T

(setq in-stream (open "my-temp-file" :direction :input))
;;=> #<INPUT-STREAM "my-temp-file">

(read in-stream)
;;=> ABC

(close in-stream)
;;=> T
```

 이 예제에서, 저희는 `my-temp-file`에 대한 출력 스트림을 만들었으며, 심볼 `ABC`를 그 스트림에 출력하였습니다. 여느때와 같이 인자를 반환하지만, 출력은 하지않았다는  점을 주목해주시길 바랍니다 - 출력된 결과는 파일로 갔습니다.

 다음으로, 출력 스트림을 닫고 동일한 파일로 입력 스트림을 열었습니다. 그런 다음 저희가 파일에 출력한 심볼을 읽어온 다음, 입력 스트림을 닫음으로써 끝을 맺었습니다.


## PRINT의 여러가지 변종 태마

 리스프는 또한 이러한 옵션을 제어하는 키워드 인자를 사용하여, 출력을 더욱 세부적으로 제어할 수 있는 `WRITE`라는 함수를 제공합니다:

| 키워드 인자      | 기본 값                   | 행동                           |
| ---------------- | ------------------------- | ------------------------------ |
| :stream          | `t`                       | 출력 스트림 설정               |
| :escape          | `*print-escape*`          | 이스케이프 문자 포함           |
| :radix           | `*print-radix*`           | 진법(radix (base)) 프리픽스    |
| :base            | `*print-base*`            | 숫자가 몇 진법을 사용할지 설정 |
| :circle          | `*print-circle*`          | 순환(circular) 구조물 출력     |
| :pretty          | `*print-pretty*`          | 가독성을 위한 공백 추가        |
| :level           | `*print-level*`           | 중첩 단계 한계                 |
| :length          | `*print-length*`          | 중첩 단계당 아이템의 한계      |
| :case            | `*print-case*`            | :upper, :lower, 혹은 :mixed    |
| :gensym          | `*print-gensym*`          | uninterned 심볼의 접두사       |
| :array           | `*print-array*`           | 가독성 있게 배열 출력          |
| :readably        | `*print-readably*`        | 강제로 가독성있게              |
| :right-margin    | `*print-right-margin*`    | 가독성있게 출력하는 옵션       |
| :miser-width     | `*print-miser-width*`     | ''                             |
| :lines           | `*print-lines*`           | ''                             |
| :pprint-dispatch | `*print-pprint-dispatch*` | ''                             |

 우연하게도, 위에 키워드 인자의 기본값으로 나온 변수들은 또한 `PRINT`의 연산을 제어합니다. 여러분은, 키워드 인자를 쓰지 않고 `LET` 폼에서 이러한 변수에 바인딩 하고 `PRIN1`를 감싸면 `WRITE`과 동일한 효과를 얻을 수 있습니다:


``` lisp
(write foo
       :pretty t
       :right-margin 60
       :case :downcase)


(let ((*print-pretty* t)
      (*print-right-margin* 60)
      (*print-case* :downcase))
  (prin1 foo))
```

 `print`가 아닌 `prin1`를 사용했는데, `PRINT`는 앞에 개행(⏎) 있고 뒤에 빈칸(⌴)이 오기 때문입니다.

 `*PRINT-...*`류의 변수를 바꿨다가, 프로그램의 어느 지점에서 다시 기본 값(default value)을 쓰고자 한다면, `with-standard-io-syntax` 폼으로 감쌀 수 있습니다:

``` lisp
;; 프로그램의 출력 제어를 정의한다.
(setq *print-circle* t)
(setq *print-array* nil)
(setq *print-escape* nil)
...

;; 위에서 설정한 걸 출력한다.
(print ...)
...

;; 원래의 출력 제어 설정으로 되돌린다.
(with-standard-io-syntax
   ...
   ;; 기본 설정으로 출력한다음,
   ;; 위에서 설정한 것들을 덮어버린다.
   (print ...)
   ...)

;; WITH-STANDARD-IO-SYNTAX 폼 밖에선,
;; 예제 상단에 있는 SETQ 폼에 의해 설정된
;; print의 설정을 또 다시 해야한다.
```


## 짚고 넘어가기

- `print`
- `prin1`
- `princ`
- `write`
- `with-standard-io-syntax`
- `open`
- `close`
- `*print-circle*`
- `*print-array*`
- `*print-escape*`