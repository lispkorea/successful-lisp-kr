# 레슨 12. 필수 - 리더 매크로(Essential Reader Macros)

- [원문](https://dept-info.labri.fr/~strandh/Teaching/MTP/Common/David-Lamkins/chapter03-12.html)


## 리더(reader)는 문자를 데이터로 만든다

 레슨 11에서 리스프 리더가 constituent characters를 심볼과 숫자로 모으gather고 macro characters가 lists, strings, quoted forms, comments를 다루기 위해 reader를 제어하는 것을 보았습니다. 이 모든 경우에, reader는 문자(characters)를 데이터로 바꿉니다 (좀 더 분명히 하자면, comment는 "데이터가 아닙니다".)


## Standard reader macros handle built-in data types

 지금까지, 우리는 리스프의 기본 문법만을 살펴보았습니다. 이는 reader에 의해 구현되며, readtable에 의해 제어됩니다. reader는 readtable에 저장되어있는 정보를 따라 characters를 처리합니다.


## User programs can define reader macros

 리스프는 리드테이블(readtable)을 `*readtable*`변수를 통해 나타내며, 리스프는 리드테이블에 있는 항목을 다룰 수 있는 함수를 제공합니다. 여러분은 이를 이용하여 리스프 리더의 행동을 바꿀 수 있습니다. 다음 예제에서, 우리는 문법을 바꿀 것이며 [와 ]를 이용하여 quoted(즉, 평가되지 않는) 리스트를 작성할 수 있을 것입니다.

``` lisp
;;이는 틀렸습니다:
(1 2 3 4 5 6)
;;>| Error: 1 is not a function

;; 대신에 이렇게 해야 합니다:
'(1 2 3 4 5 6)
;;=> (1 2 3 4 5 6)

;;새로운 문법을 정의하여
;; '(1 2 3 4 5 6)
;;대신에
;; [1 2 3 4 5 6]
;;이처럼 작성할 수 있게 합시다.
(defun open-bracket-macro-character (stream char)
  `',(read-delimited-list #\] stream t))
;;=> OPEN-BRACKET-MACRO-CHARACTER

(set-macro-character #\[ #'open-bracket-macro-character)
;;=> T

(set-macro-character #\] (get-macro-character #\)))
;;=> T

;;이제 테스트 해봅시다:
[1 2 3 4 5 6]
;;=> (1 2 3 4 5 6)
```

 처음 저희는 `(1 2 3 4 5 6)`을 평가하려 했습니다: `1`은 함수가 아니기에, 이는 옳지않습니다.
  저희가 실제로 해야하는 것은 리스트를 `quote` 하는 것입니다. 그러나 이러한 작업을 매번 해야한다면, 더욱 편리한 문법을 원하게 될 것입니다. 특히, `'(...)`과 같은 행동을 하는 `[...]`이 있었으며 좋겠습니다.

 이를 수행하기 위해, 이의 인자를 평가하지 않는 specialized list reader macro 함수를 정의해야 합니다. 저희는 reader가 [ 문자를 만나면 함수가 호출되도록 할 것입니다; 함수는 ] 문자를 만나면 리스트를 반환할 것입니다. 모든 reader macro function은 두개의 인자에 의해 호출됩니다: input stream과 macro를 작동시키는 문자.

 리스프가 한정된delimited 리스트를 읽도록 설계된 함수를 지녔기에, 저희 reader 매크로는 매우 단순합니다. READ-DELIMITED-LIST는 하나의 인자를 기다립니다 - 읽고있는 리스트를 종료시키는 문자. 다른 두개의 인자는 optional입니다 - input stream과 (reader macro 함수에서 사용될땐 보통 T로 설정되는)flag. READ-DELIMITED-LIST는 terminating character를 만나기 전까지 input stream에서 objects를 읽은다음, 모든 objects를 list로 반환합니다. 자체적으로, 이는 평가를 막는것suppressing을 제외한 저희가 필요한 모든것을 수행합니다.

 레슨 3에서 봤던것 처럼, `QUOTE` (혹은  `'`)는 평가를 막습니다. 그러나 저희는 `'(READ-DELIMITED-LIST ...)`를 사용할 수 없습니다; 이는, 저희가 `quote` 하고자 하는 폼을 얻기 위해 저희가 평가하고자 하는, 폼의 평가를 막습니다... 대신, `quote`된 폼에 대해 선택적인 평가를 요구하는 `` ` ``(레슨 8 참조)를 이용하였습니다.

 저희 `OPEN-BRACKET-MACRO-CHARACTER`의 정의는 폼을 평가하고 `quote`된 결과를 반환하기 위해
`` `',form ``
을 이용하였습니다.

- 리스프는 프로그래머를 위해 6개의 문자를 예약하였습니다: `[ ] { } ! ?`

 여러분은 이중 일부나 전체를 리스프의 중재없이 매크로 문자를 정의할 수 있습니다. 그러나, 다른 프로그래머와 코드를 공유하게 된다면, 논쟁이 발생할 수 있다는 점을 주의해야 합니다.


## 짚고 넘어가기

- `*readtable*`