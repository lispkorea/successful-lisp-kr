# 레슨 11. 필수 - 입력과 출력(Essential Input and Output)

- [원문](https://dept-info.labri.fr/~strandh/Teaching/MTP/Common/David-Lamkins/chapter03-11.html)


## READ는 리스프 데이터를 받는다.

 레슨 10에서 봤던것처럼, `read`는 문자를 리스프 데이터로 변환시킵니다. 이제까지, 우리들은 리스프 데이터 여러 출력 값들을 보았습니다:

- 심볼과 숫자
- 문자, 문자열, 리스트, 배열, 백터, 구조체
- 해쉬태이블

 리스프 리더는 문자의 분류법(classifications)를 따라 일을 수행합니다. 표준 분류법은 아래에 나와있습니다. 레슨 12에서 보게될 것처럼, 여러분은 필요에 따라 이러한 분류법을 바꿀 수도 있습니다.

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
" ’ ( ) , ; ‘


표준 비-종료 매크로 문자(Standard Non-Terminating Macro Characters)
-----------------------------------------
#


표준 단일 예외 문자(Standard Single Escape Characters)
---------------------------------
\


표준 다수 예외 문자(Standard Multiple Escape Characters)
-----------------------------------
|


표준 공백 문자(Standard Whitespace Characters)
------------------------------
<tab> <space> <page> <newline> <return> <linefeed>
```

 READ가 구성문자(constituent character)로 시작한다면, 이는 심볼이나 number를 축적합니다. READ가 종료매크로문자(terminating macro character)나 공백문자(whitespace character)를 만나면, 이는 모아진 구성문자를 우선 숫자로, 그 다음 symbol로 이해interpret하려 시도합니다. 수치화 해석이 가능하다면, READ는 숫자를 반환합니다. 그렇지 않으면, READ는 영문자를 standard case(보통 대문자)로 바꾸어, symbol로써 이름을 intern하여, 심볼을 반환합니다.

 예외문자(Escape characters)는 특별한 역활을 수행합니다. 하나의 예외문자는 다음에 나오는 문자를 마치 constituent character처럼 다루도록 합니다. 보통 공백이나 종료매크로문자로 취급되는 문자는 심볼의 일부가 될 수 있습니다. READ가 escape character를 만나면, 숫자들로만 된것이 escape될지라도, constituents 결과를 숫자로 interpret 하려 하지 않을 것입니다

 READ가 매크로 문자(macro character)로 시작한다면, 문자는 다음 경우를 판별할 것입니다.

| 문자 | 설명                                            |
| ---- | ----------------------------------------------- |
| "    | 문자열을 읽는다.                                |
| ’    | 폼을 읽는다.                                    |
| (    | 리스트를 읽는다.                                |
| ;    | 새로운 라인을 만나기 전까지 모든 것을 무시한다. |
| #    | 다음 나오는 문자에 기반하여 무엇인지 결정한다.  |

 마지막으로, 몇몇 리스프 데이터는 읽을 수 없습니다. 예를들어, 해쉬테이블이 출력되어 보여지는 것은 `#<HASH-TABLE>`와 같이 보여집니다. `#<` 문자로 시작하는 어떠한 것을 읽으려할때 READ는 에러를 낼 것입니다.


## PRINT writes Lisp data for you and for READ

 `print` 함수는 리스프 오브젝트를, READ가 이를 재구성해야만 하는, 문자열(sequence of characters)로 바꿉니다:

``` lisp
(print ’abc)
=  \newline ABC \space
;;=> ABC

(print (list 1 2 3))
=  \newline (1 2 3) \space
;;=> (1 2 3)

(print "A String")
=  \newline "A string" \space
;;=> "A string"

(print 387.9532)
=  \newline 387.9532 \space
;;=> 387.9532

(print (make-hash-table))
=  \newline #<HASH-TABLE> \space
;;=> #<HASH-TABLE>
```

 PRINT는 개행문자(`\newline`)로 시작하고 띄어쓰기(`\space`)으로 끝나는 결과물을 출력합니다. 개행과 띄어쓰기 모두 공백(whitespace)으로 취급되므로, (escape가 아닌)리스프 오브젝트가 출력된 표현의 일부가 될수 없기에, 이는 PRINT 결과물이 다른 결과물과는 다르다는 것을 보증합니다.

 PRINT의 다른 변종들은 다양한 쓰임세를 지녔습니다. PRIN1은 PRINT처럼 행동하지만, whitespace로 감싸지 않습니다. 예를들어 연이은 조각들로부터 이름을 만들고자 할때, 이는 유용할 것입니다. PRINC는 PRIN1처럼 행동하지만, 보여는것 보단 READ를 위한 결과물을 생성합니다; 예를들어, PRINC는 문자열을 둘러싸는 쌍따옴표(quotes)를 생략하며, escape character를 출력하지 않습니다.

``` lisp
(print ’a\ bc)
;;>>  \newline  |A BC| \space
;;=> |A BC|

(prin1 ’a\ bc)
;;>> |A BC|
;;=> |A BC|

(princ ’|A BC|)
;;>> \newline  A BC \space
;;=> |A BC|
```

## OPEN and CLOSE let you work with files

 보통, READ는 키보드를 읽고 PRINT는 화면에 출력합니다. 이러한 함수 모두 optional 인자를 취합니다; 인자는 READ를 위한 input stream과 PRINT를 위한 output stream을 지정합니다. stream은 무엇일까요? stream은 데이터의 근원(source)나 밑바닥(sink)이며, 보통 (그치만 절대적이지는 않은) 문자(characters)입니다. 이제부터, 저희는 tex file이 어떻게 문자 stream의 source나 sink가 될 수 있는지를 살펴볼 것입니다. 19장 [p 183]에서 저희는 몇몇 다른 가능성들을 살펴볼 것입니다.

 여러분은, 파일 이름을 인자로 취하고 stream의 방향(input이나 output)을 결정하는 keyword 인자를 취하는, OPEN 함수를 이용하여 stream을 파일로 붙일 수 있습니다. stream에 관한 작업을 종료하고 관련된 file을 닫기 위해선, CLOSE 함수를 사용합니다.

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

 이 예제에서, 저희는 my-temp-file에 대한 output stream을 만들고, symbol ABC를 그 stream에 출력하였습니다. 인자는 평상시처럼 반환하지만 출력을 하지 않는 다는 것을 주목하시기 바랍니다 - 대신, 출력된 결과는 파일로 갑니다.

 다음으로, output stream을 닫고 동일한 파일에 input stream을 열었습니다. 그런 다음 저희가 file에 출력한 심볼을 읽어온 다음, input stream을 닫음으로써 끝을 맺습니다.


## Variations on a PRINT theme

 리스프는 또한, 이러한 옵션을 제어하는 keyword 인자를 이용하여, 여러분에게 출력에 대해 더욱 세부적인 것에 관한 제어권을 주는 WRITE 함수를 제공합니다:

| Keyword Argument | Default Value             | Action                         |
| ---------------- | ------------------------- | ------------------------------ |
| :stream          | `t`                       | set output stream              |
| :escape          | `*print-escape*`          | include escape characters      |
| :radix           | `*print-radix*`           | include radix (base) prefix    |
| :base            | `*print-base*`            | set number base (rationals)    |
| :circle          | `*print-circle*`          | print circular structures      |
| :pretty          | `*print-pretty*`          | add whitespace for readability |
| :level           | `*print-level*`           | limit nesting depth            |
| :length          | `*print-length*`          | limit items per nesting level  |
| :case            | `*print-case*`            | :upper, :lower, or :mixed      |
| :gensym          | `*print-gensym*`          | prefix uninterned symbols      |
| :array           | `*print-array*`           | print arrays readably          |
| :readably        | `*print-readably*`        | force printing to be readable  |
| :right-margin    | `*print-right-margin*`    | controls pretty-printing       |
| :miser-width     | `*print-miser-width*`     | "                              |
| :lines           | `*print-lines*`           | "                              |
| :pprint-dispatch | `*print-pprint-dispatch*` | "                              |

 우연하게도, 위에 키워드 인자의 기본값으로 나온 변수들은 또한 PRINT의 연산을 제어합니다. 여러분은, PRIN1을 감싸는 LET 폼에서 이러한 변수들을 binding 함으로써, non-default keyword 인자로 WRITE의 효과를 얻을 수 있습니다:


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

 PRINT가 추가하는 앞에 newline이 있고 뒤에 빈칸(blank)이 오는걸 원치 않기에, `print`가 아닌 `prin1`를 사용하였습니다.

 여러분의 프로그램이 `*PRINT-...*` 변수를 바꾸지만, 프로그램의 어느 지점에서 기본 값(default value)을 쓰고자 한다면, `with-standard-io-syntax` 폼으로 프로그램의 일부를 감쌀 수 있습니다:

``` lisp
;;프로그램의 printer control을 정의한다.
(setq *print-circle* t)
(setq *print-array* nil)
(setq *print-escape* nil)
...

;;위에서 설정한 걸 출력한다.
(print ...)
...

;;default printer control 설정으로 되돌린다.
(with-standard-io-syntax
   ...
   ; standard setting으로 출력한다음,
   ; 위에서 설정한 이러한 것들을 덮어쓴다.
   (print ...)
   ...)
;; WITH-STANDARD-IO-SYNTAX form 밖에선,
;; 예제 상단에 있는 SETQ 폼에 의해
;; 구축establish된 print 설정을 또 다시 해야합니다.
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