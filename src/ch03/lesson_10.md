# 레슨 10. 프리뷰 - 다른 데이터 타입(A Preview of Other Data Type)

- [원문](https://dept-info.labri.fr/~strandh/Teaching/MTP/Common/David-Lamkins/chapter03-10.html)


## 리스프는 거의 항상 숫자를 제대로 처리한다.

 이는 말하기에 이상한 것처럼 들립니다. 컴퓨터는 항상 숫자로 일을 수행하지 않나요?  음, 아니요... 보통은 그렇지 않습니다.

 수치 계산은 수많은 방식으로 나뉠 수 있습니다. 가장 큰 문제가 되는 점 중 하나는 소수의 계산이 있습니다 (여러분의 언어가 이를 실수라 부른다면, 그것은 거짓말 입니다). 비쥬얼 어쩌고 혹은 객체 지향 블라블라와 같은 많은 책들 중 대다수가 소수의 계산에 대해 써져있습니다.

 소수의 문제는 수학적으로 실수가 아니지만, 종종 실수인양 (잘못) 사용된다는 점입니다.
중요한 문제는 소수를 그렇게 사용한다면, 정확도의 한계를 지니게 된다는 것 입니다 - 소수점 우측 몇개의 자릿수 만큼. 이제, 계산에 사용되는 모든 숫자가 거의 같은 규모라면, 계산의 정확도가 떨어지지 않을 수 도 있을 것입니다. 그러나, 매우 다양한 규모의 숫자들이라면, 소수점 계산은 정확도를 희생할 것입니다.

 여러분의 컴퓨터의 소수가 정확하게 소숫점 7자리를 표현할수 있다고 가정해 봅시다. 그러면, 여러분은 `1897482.0`에 `2973225.0`를 더하면 완벽히 정확한 답을 얻을 수 있을 것입니다. 그러나, `1897482.0`에 `0.2973225`를 더하고자 한다면, 정확한 답은 `14`자리의 숫자이지만, 여러분의 컴퓨터는 `1897482.0`을 답으로 할 것입니다.

 소수에 관한 또 다른 문제는 더욱 미묘합니다. 프로그램을 작성할때 보통 10진수에 기반하여 수를 사용할 것입니다. 그러나 컴퓨터는 모든 연산을 2진수에 기반하여 수행합니다. 10진수에서 2진수로의 변환은 어떤 "명백히 정확한" 특정 숫자에 대해 재미난 일을 수행합니다. 예를들어, 소수 `0.1`은 2진수로 변환하면 순환 분수입니다. 컴퓨터는 순환분수에 의해 요구되는 무한한 숫자를 저장하지 못하기에, 정확하게 0.1을 저장할 수 없습니다.

 대부분의 컴퓨터 언어에서 정수(자연수) 연산은 또다른 문제를 지닙니다

- 하나의 정수를 담을 수 있는 양수나 음수의 최대치에 제한을 가하는 경향이 있습니다. 따라서, 숫자 하나를 여러분의 언어에 대해 컴퓨터를 다룰 수 있는 가장 큰 정수에 더하고자 한다면, 둘 중 하나가 일어납니다:

1.  에러로 프로그램이 종료된다, 혹은
2.  굉장히 부정확한 답을 얻는다.(가장 큰 양수에 1을 더한 것은 가장 작은 음수를 만들어 냅니다.)

 그러면 숫자에 관해 올바른 일을 수행하기 위해 리스프는 어떻게 처리할까요? 무엇보다도, 이 문제가 컴퓨터의 연산에서 나온 것처럼 보입니다. 답은 리스프는 내장 컴퓨터 산술 연산을 바로 사용하지 않는다는 것입니다 - 수학적으로 정확한 특정한 수치 데이터 형식을 추가하였습니다:

- `bignum`은 무한한 정수이다 (컴퓨터의 메모리의 한계에 달려있다)
- 유리수는 나눗셈 알고리즘에서 나온 대략적인 소수가 아니라 두 정수의 정확한 몫이다.

 물론, 리스프 또한 머신 기반 정수와 소수를 지닙니다. 머신에서의 정수를 리스프에서 `fixnum`라 부릅니다. `fixnum`의 범위에서 자연수가 떨어지면, 리스프는 이를 머신의 정수로 저장할 것입니다. 그러나, 이게 매우 커지면, 리스프는 자동으로 이를 `bignum`으로 승격시킬 것입니다.

 앞서 리스프는 거의 항상 수에 관해 올바른 일을 수행한다고 말했는데, `거의 항상 수학적으로 옳은 수의 표현을 고른다` 라는 의미로 말했었습니다:

``` lisp
(/ 1 3)
;;=> 1/3

(+ (/ 7 11) (/ 13 31))
;;=> 360/341

(defun factorial (n)
  (cond ((= n 0) 1)
        (t (* n (factorial (- n 1))))))
;;=> FACTORIAL

(factorial 100)
;;=> 933262154439441526816992388562667004907159682643816214685
;;   929638952175999932299156089414639761565182862536979208272
;;   23758251185210916864000000000000000000000000
```

:TODO(pyoung)
 소수를 사용하여 계산을 할 수 도 있지만, 리스프는 정확한 수치 결과를 부정확한 소수로 전환할 수 없기에(여러분은 이에게 소수는 전염된다라고 말해야 합니다) 일단 이를 계산에 도입하면, 전체 계산 결과는 소수를 유지할 것입니다:

``` lisp

(float (/ 1 3))
;;=> 0.3333333333333333

(* (float (/ 1 10)) 
   (float (/ 1 10)))
;;=> 0.010000000000000002

(+ 1/100 (* (float (/ 1 10))
            (float (/ 1 10))))
;;=> 0.020000000000000004

(+ 1/100 1/100) ; 이전 계산과 비교하세요
;;=> 1/50

(* 3 7 10.0)
;;=> 210.0

(- 1.0 1)
;;=> 0.0

(+ 1/3 2/3 0.0)
;;=> 1.0

(+ 1/3 2/3)
;;=> 1 ; 이전 계산과 비교하세요
```

 리스프는 소수를 소수점과 같이, 정수에는 소수점 없이 출력합니다.


## 문자는 리스프가 읽고 쓸 수 있는 무언가를 제공합니다.

 기본적으로 리스프 I/O는 문자를 이용합니다. `READ`와 `WRITE` 함수는 문자를 리스프 오브젝트로 그리고 역으로 변환시킵니다. `READ-CHAR`와 `WRITE-CHAR`는 단일 문자를 읽고 씁니다.

``` lisp
(read)
;;<< a \newline
;;=> A

(read)
;;<< #\a \newline
;;=> a

(read-char)
;;<< a
;;=> #\a

(write 'a)
;;>> A
;;=> A

(write #\a)
;;>> #\a
;;=> #\a

(write-char #\a)
;;>> a
;;=> #\a

(write-char 'a)
;;>| Error: Not a character
```

 위 예에서 새로운 표기를 추가했습니다. `;;<<` 표시는 `READ`와 같은 입력함수에 대한 입력을 기다린다는 것을 의미합니다. `\newline`은 엔터(enter)키에 의해 생성되는 개행문자를 나타냅니다.

 값을 반환하는 것(`;;=>`)과 달리 `;;>>`는 출력을 나타냅니다.

 개행시 `read`의 입력받는 동작이 종료된다는 것을 알아채셨을 것입니다. `READ`는 완벽한 리스프 표현식을 구성하고자하는 문자들을 모으기 때문입니다. 레슨 11[p 77]에서 이것에 대해 좀 더 살펴볼 것입니다. 이번 예제에서, `READ`는 개행으로 종료되는 심볼을 모읍니다. 심볼은 또한 공백, 괄호, 심볼의 일부가 아닌 문자로 종료됩니다.

 대조적으로, `READ-CHAR`는 입력에서 정확히 하나의 문자만 읽습니다. 문자를 받자마자 `READ-CHAR`는 실행을 완료하고 문자를 반환합니다.

 몇몇 리스프 시스템은 어떠한 입력을 받아들이기 전에 리턴(return) 키를 누르도록 요구할 것입니다. 이는 일상적인게 아니며, 환경 설정 변수로 변경 할 수 있습니다 - 여러분이 사용하는 리스프의 제조사와 상담을 하시기 바랍니다.

 `WRITE`와 `WRITE-CHAR`는 둘 다 받은 값을 반환합니다. 값을 출력하는 방식은 다릅니다. `WRITE`는 값을 출력하므로, 동일한 값을 생성하는 `READ`로 표현할 수 있습니다. `WRITE-CHAR`는, 이것이 `READ`에서의 문자라는 것을 나타내는 추가 리스프 문법 (`#\`)가 없는, 읽을 수 있는 문자를 출력합니다.

 리스프는 단일 문자를 `#\문자` 표기법을 이용하여 나타내며, 그러한 문자로는 literal 문자나 출력 가능한 상형문자가 아닌 문자의 이름이 있습니다.

| Character       | Hex Value | Lisp        | Standard? |
| --------------- | --------- | ----------- | --------- |
| space           | 20        | #\Space     | yes       |
| newline         | --        | #\Newline   | yes       |
| backspace       | 08        | #\Backspace | semi      |
| tab             | 09        | #\Tab       | semi      |
| linefeed        | 0A        | #\Linefeed  | semi      |
| formfeed        | 0C        | #\Page      | semi      |
| carriage return | 0D        | #\Return    | semi      |
| rubout or DEL   | 7F        | #\Rubout    | semi      |

 `#\Space`와 `#\Newline`만이 모든 리스프 시스템에서의 요구사항입니다. ASCII 문자셋를 이용하는 시스템은 아마 위에 나온 나머지 문자코드를 구현할 것입니다. `\#Newline` 문자는 호스트 시스템에 맞는 출력 라인의 끝을 표시하는 컨벤션(convention)을 따릅니다. 예로:

| System    | Newline | Hex Value |
| --------- | ------- | --------- |
| Macintosh | CR      | 0D        |
| MS-DOS    | CR LF   | 0D 0A     |
| Unix      | LF      | 0A        |

 94개의 출력가능한 표준 문자들이 `\#문자`로 표현됩니다:

``` txt
  ! " # $ % & ' ( ) * + , - . /
0 1 2 3 4 5 6 7 8 9 : ; < = > ?
@ A B C D E F G H I J K L M N O
P Q R S T U V W X Y Z [ \ ] ^ _
‘ a b c d e f g h i j k l m n o
p q r s t u v w x y z { | } ~
```

## Arrays organize data into tables

 여러분이 데이터를 2차원, 3차원 혹은 더 많은 차원의 테이블로 역으려한다면, 배열(array)을 만들 수 있습니다:

``` lisp
(setq a1 (make-array '(3 4)))
;;=> #2A((NIL NIL NIL NIL)
;;       (NIL NIL NIL NIL)
;;       (NIL NIL NIL NIL))

(setf (aref a1 0 0) (list 'element 0 0))
;;=> (ELEMENT 0 0)

(setf (aref a1 1 0) (list 'element 1 0))
;;=> (ELEMENT 1 0)

(setf (aref a1 2 0) (list 'element 2 0))
;;=> (ELEMENT 2 0)

a1
;;=> #2A(((ELEMENT 0 0) NIL NIL NIL)
;;       ((ELEMENT 1 0) NIL NIL NIL)
;;       ((ELEMENT 2 0) NIL NIL NIL))

(aref a1 0 0)
;;=> (ELEMENT 0 0)

(setf (aref a1 0 1) pi)
;;=> 3.141592653589793

(setf (aref a1 0 2) "hello")
;;=> "hello"

(aref a1 0 2)
;;=> "hello"
```

 dimensions의 리스트를 취하여 배열을 반환하는 MAKE-ARRAY를 이용하여 배열을 만듭니다. 기본적으로,  배열은 어떠한 종류의 데이터라도 담을 수 있습니다; 효율성을 위해 추가 인자로 데이터형식의 요소를 제한할 수 있습니다.

 배열의 rank는 dimension의 수와 동일합니다. 위 예제에서 저희는 2-차원rank-2 배열을 만들었습니다. 리스프는 `#rankA(...)`표기법을 이용하여 배열을 출력합니다. 배열의 내용물은, 첫번째 dimension이 최상단 그룹의 요소로 보여지고, 마지막 dimension이 최하단 그룹의 요소로 보여지는, 중첩된 list로 보여집니다.

 여러분의 리스프 시스템은 아마도 여기서 보여졌던것 처럼 라인을 넘기면서 배열을 출력하지 않을 것입니다. 저는 배열의 구조를 강조하기 위해 라인을 넘겼습니다.

 배열의 요소를 받기 위해선, AREF를 이용합니다. AREF의 첫번째 요소는 배열입니다; 나머지 요소는 각 dimension의 index를 지정합니다. index의 수는 배열의 rank와 맞아야만 합니다.

 배열의 요소를 설정하기 위해선, 이 예제에서 보인것 처럼 SETF form내부에서 AREF를 사용합니다. SETF는 SETQ와 유사하지만, SETQ가 값을 심볼에게 할당하고, SETF는 값을 place에 할당합니다. 예제에서 AREF 폼은 배열의 요소의 place를 지정합니다.


## 백터는 일차원 배열

 Vector는 일-차원one-dimensional 배열입니다. MAKE-ARRAY를 이용하여 벡터를 만들 수 있으며, AREF를 이용하여 이의 요소에 접근 할 수 있습니다.

``` lisp
(setq v1 (make-array '(3)))
;;=> #(NIL NIL NIL)

(make-array 3)
;;=> #(NIL NIL NIL)

(setf (aref v1 0) :zero)
;;=> :ZERO

(setf (aref v1 1) :one)
;;=> :ONE

(aref v1 0)
;;=> :ZERO

v1
;;=> #(:ZERO :ONE NIL)


;; 리스프는 #1A(...)이 아닌 조금 간소화된 form #(...)을 이용하여 벡터를 출력합니다.
;; MAKE-ARRAY에서 단일-원소 리스트나 벡터의 차원dimenion을 지정할 수 있습니다.
;; VECTOR 폼을 이용하여 값을 나열하여 벡터를 만들 수 있습니다:
(vector 34 22 30)
;;=> #(34 22 30)
```

 결과값이 list가 아닌 vector라는 점을 제외하면, 이는 LIST form과 유사합니다. list와 vector간에는 또 다른 닮음이 있습니다: 모두 sequence입니다. sequence는 13장[p 150]에서 보게될 함수에 의해 다뤄집니다.

 벡터의 요소에 접근하기 위해 AREF를 이용하거나, sequence-특화 함수 ELT를 이용할 수 있습니다:

``` lisp
(setf v2 (vector 34 22 30 99 66 77))
;;=> #(34 22 30 99 66 77)

(setf (elt v2 3) :radio)
;;=> :RADIO

v2
;;=> #(34 22 30 :RADIO 66 77)
```

## 문자열은 문자를 담고있는 백터

 여러분은 이미 "..."를 이용한 문자열(string) 작성하는 법을 알고 있습니다. 문자열은 벡터이기에, 문자열의 요소에 접근하기 위해 배열과 백터 함수를 적용할 수 있습니다. 또한 `make-string` 함수를 이용하여 문자열을 만들거나, `string` 함수를 이용하여 문자나 심볼을 문자열로 바꿀 수 있습니다.

``` lisp
(setq s1 "hello, there.")
;;=> "hello, there."

(setf (elt s1 0) #\H))
;;=> #\H

(setf (elt s1 12) #\!)
;;=> #\!

s1
;;=> "Hello, there!"

(string 'a-symbol)
;;=> "A-SYMBOL"

(string #\G)
;;=> "G"
```


## Symbols are unique, but they have many values

 레슨 5에서 심볼은 고유한 독자성(identity)을 지닌다고 보았지만, 이는 반복성을 지닙니다: 심볼은 같은 철자의 다른 심볼과 동일합니다(이번 레슨 끝부분에서 좀더 배우게될 패키지 designation를 포함하여). 이는 리스프로 하여금 프로그램이나 데이터를 읽어올 수 있게 만들 수 있다는 것을 의미하며, 같은 철자로 나타낸 모든 심볼은 동일한 심볼입니다. 리스프가 이를 수행하는 매커니즘을 제공하기에, 심볼릭 정보를 다루는 프로그램을 작성하는 것에 대한 걱정을 하나 덜게되었습니다.

 저희는 또한 레슨 5에서 심볼은 변수와 함수, 그리고 문서, 출력 이름, 프로퍼티(properties)를 위한 값을 가질 수 있다고 배웠습니다. 심볼의 프로퍼티 리스트(property list)는 다수의 키/값 쌍이 심볼과 연결된 아주 작은 데이터베이스와 같습니다. 예를들어, 여러분의 프로그램이 오브젝트를 표현하고 다룬다면, 오브젝트에 관한 정보를 프로퍼티 리스트에 저장할 수 있습니다:

``` lisp
(setf (get 'object-1 'color) 'red)
;;=> RED

(setf (get 'object-1 'size) 'large)
;;=> LARGE

(setf (get 'object-1 'shape) 'round)
;;=> ROUND

(setf (get 'object-1 'position) '(on table))
;;=> (ON TABLE)

(setf (get 'object-1 'weight) 15)
;;=> 15

(symbol-plist 'object-1)
;;=> (WEIGHT 15 POSITION (ON TABLE) SHAPE ROUND SIZE LARGE COLOR RED)

(get 'object-1 'color)
;;=> RED

object-1
;;>| Error: no value
```

 `OBJECT-1`은 값을 갖지 않는다는 점을 주목하시기 바랍니다 - 모든 유용한 정보는 두 곳에 있습니다: 심볼의 identify와 심볼의 프로퍼티.

 이러한 프로퍼티의 이용은 현대의 오브젝트 프로그래밍보다 몇 십년이나 앞서 나왔습니다. 이는 오브젝트의 필수적인 3가지 매커니즘중 2가지를 제공합니다: 식별자identify와 캡슐화encapsulation (property 값 역시 함수가 될 수 있다는 것을 명심하시기 바랍니다). 3번째 매커니즘, 상속(inheritance)은 다른 "object"를 link하여 simulate합니다.

 프로퍼티는 오늘날의 리스프 프로그램에서 거의 사용되지 않습니다. 해쉬테이블(Hashtables) (아래 참조) [p 73] , (다음 section에서 기술된) 구조체(structures), CLOS 오브젝트(7장 [p 117]과 14장 [p 157] 참조)는 사용하기 더 편리하며 더욱 효율적인 방식으로 프로퍼티 리스트의 모든 기능을 제공합니다. 현대 리스프 개발 시스템은 프로퍼티 파일과 심볼을 정의하는 폼의 파일 위치, 함수의 인자 리스트의 정의와 같은 (프로그래밍 환경에서 유용한 도구로 이용하기 위한) 특정 정보를 기록하여 프로그램에 주석을 다는 목적으로 주로 사용합니다.


## Structures let you store related data

 리스프 구조체는, 명명된 슬롯(slot)과 관계된 데이터를 저장하는, 오브젝트를 생성하는 방법을 여러분에게 제시합니다.

``` lisp
(defstruct struct-1
  color
  size
  shape
  position
  weight)
;;=> STRUCT-1

(setq object-2 (make-struct-1
                              :size 'small
                              :color 'green
                              :weight 10
                              :shape 'square))
;;=> #S(STRUCT-1 :COLOR    GREEN
;;               :SIZE     SMALL 
;;               :SHAPE    SQUARE
;;               :POSITION NIL
;;               :WEIGHT   10)

(struct-1-shape object-2)
;;=> SQUARE

(struct-1-position object-2)
;;=> NIL

(setf (struct-1-position object-2) '(under table))
;;=> (UNDER TABLE)

(struct-1-position object-2)
;;=> (UNDER-TABLE)
```

 이 예에서, 저희는 `COLOR`, `SHAPE`, `SIZE`, `WEIGHT` 슬롯을 지닌 `STRUCT-1` 구조체를 정의하였습니다. 그런 다음 `STRUCT-1`의 인스턴스(instance)를 만들고, 해당 인스턴스를 변수 `OBJECT-2`에 할당하였습니다. 예제의 나머지 부분은, struct-slot 이름으로된 접근자(assessor) 함수를 이용하여, 어떻게 구조체 인스턴스의 슬롯에 접근하는지를 보여줍니다. 리스프는 `DEFSTRUCT`를 이용하여 구조체를 정의할때 `make-structname`과 structname-slotname 함수를 생성합니다.

 6장[p 112]에서 `DEFSTRUCT`의 optional 기능에 대해 살펴볼 것입니다.


## Type information is apparent at runtime

 심볼은 런타임시 어떠한 변수 타입과도 연관지을 수 있습니다. 이것이 문제되는 경우에 대해, 리스프는 저희에게 값의 타입을 묻는것을 허용합니다

``` lisp
(type-of 123)
;;=> FIXNUM

(type-of 123456789000)
;;=> BIGNUM

(type-of "hello, world")
;;=> (SIMPLE-BASE-STRING 12)

(type-of 'fubar)
;;=> SYMBOL

(type-of '(a b c))
;;=> CONS
```

 `TYPE-OF` 는 인자의 타입을 나타내는 심볼이나 리스트를 반환합니다. 게다가 이 정보는 인자의 종류의 기반하는 프로그램의 행동을 설명하는데 이용할 수 있습니다. `TYPECASE` 함수는 타입에 대한 질의(inquiry)와 COND-와같은 분기(dispatch)를 결합한 것입니다.

 CLOS(14장 [p 157] 참조)S의 제네릭 함수의 도입으로, `TYPE-OF`는 예전만큼 중요하지는 않습니다.


## Hash Tables provide quick data access from a lookup key

 해쉬 테이블은 값과 유니크 키(유일 키, unique key)와 관련있습니다. 프로퍼티 리스트완 다르게, 해쉬 테이블은 엄청나게 많은 키/값쌍에 매우 적합하지만, 작은 연관 set에 대해 엄청난 오버헤드(overhead)를 감수해야합니다.

``` lisp
(setq ht1 (make-hash-table))
;;=> #<HASH-TABLE>

(gethash 'quux ht1)
;;=> NIL
;;=> NIL

(setf (gethash 'baz ht1) 'baz-value)
;;=> BAZ-VALUE

(gethash 'baz ht1)
;;=> BAZ-VALUE
;;=> T

(setf (gethash 'gronk ht1) nil)
;;=> NIL

(gethash 'gronk ht1)
;;=> NIL
;;=> T
```

 `MAKE-HASH-TABLE`을 이용하여 해쉬 테이블을 생성하며, `GETHASH`를 이용하여 값에 접근합니다. `GETHASH`는 두개의 값을 반환합니다. 첫번째는 키와 관련있는 값입니다. 두번째는 키를 찾으면 `T`, 아니면 `NIL`입니다. 위 예제에서 처음과 마지막 `GETHASH`폼의 차이를 주목하시기 바랍니다.

 기본적으로, 해쉬테이블은 `EQ`(이는 숫자나 리스트가 아닌 심볼에 대해서만 동작합니다)를 이용하여 이의 키를 비교하기 위해 만들어졌습니다. 17장[p 174]에서 동일성 판단(equality predicates)에 대해 더 자세히 배울 것입니다. 지금은, 다수의 키를 이용하고자 한다면, 이 폼을 이용하여 해쉬테이블을 만들어야한다는 것을 명심하시기 바랍니다:

``` lisp
(make-hash-table :test #'eql)
```

 리스트를 키로 사용하고자 한다면, 이렇게 해쉬테이블을 만듭니다:

``` lisp
(make-hash-table :test #'equal)
```

 키를 없애고자한다면, `(REMHASH key hash-table)`폼을 이용합니다. 그리고 키에 해당하는 값을 바꾸고자한다면, 키/값쌍을 추가했던것처럼 `GETHASH`와 `SETF`를 이용합니다.


## Packages keep names from colliding

 프로그램을 작성하는것에 관한 어려운 일중 하나는 프로그램의 이름을 짓는 부분입니다. 한편으론, 기억하기 쉽고 명명된 object의 규칙과 목적을 환기evocative시켜주는 이름을 이용하기 원할 것입니다. 다른 한편으론, 언젠가 여러분의 프로그램과 작업하게될, 다른 프로그램의 어딘가에 이미 사용한 (혹은 사용될 것 같은)이름을 이용하지 않길 원할 것입니다.

 이름 충돌(naming conflict)을 피하는 법 중 하나는 프로그램에 있는 모든 이름에 누구도 사용하지 않는 고유 접두사(prefix)를 붙이는 것입니다. 여러분은 이것이 라이브러리에 매번 필요하다고 생각할 것입니다 - 보통 하나에서 세개정도 문자의 접두사가 있다. 불행히도, 여전히 두 소프트웨어 개발자가 동일한 prefix를 선택하기 위한 많은 관문이 남아있습니다; 유달히 몇몇 prefix들이 다른 것보다 더욱 evocative하기 때문입니다. 여러분이 생산한 모든 소프트웨어를 제어하고자 한다면, 모든 prefix를 선택하고 문제를 피할 수 있습니다. prefix naming scheme을 이용하는 third-parfy 소프트웨어를 사고자 한다면, 제조사에 의해 선택된 이름에 관련된 일을 해야할 것이며, 두 제조사가 우연히 동일한 prefix를 사용하지 않기를 희망할 것입니다.

 ![prefixed-names.gif](../res/prefixed-names..gif)

 이름 충돌을 피하는 또 하나의 방법은 qualified name을 이용하는 것입니다. 이를 하기 위해선, 언어가 프로그래머에 의해 정의되고 제어되는 분리된 이름공간의 지원을 제공해야 합니다. 어떻게 이것이 동작하는지 이해하기 위해, 프로그램을 위해 여러분이 만든 모든 이름이 여러분의 이름이 타이틀 상단에 쓰여진 한장에 종이에 작성되었다고 상상해보시기 바랍니다. 이름이 이용하기에 안전한지 확인하기 위해선, 여러분이 이 페이지에 작성한 이름 목록을 확인하기만 하면 됩니다. 누군가의 소프트웨어가 여러분의 프로그램의 서비스를 필요로 할때, 그것은 여러분의 qualifier와 name을 이용하여 여러분의 이름을 참조합니다. 다른이의 소프트웨어는 다른 qualifier를 지니고 그들의 qualifier는 그들만의 name을 암시하기에(이것을 작성할 필요는 없습니다), 이름 충돌을 피할 방도가 없습니다.

 여러분은 qualifier는 이름에 prefix를 붙이기 위한 복잡한 방법에 지나지 않는다고 생각할 것입니다. 그러나, 거기엔 미묘하며 중대한 차이점이 있습니다. prefix는 이름의 일부입니다; 이는 한번 작성되면 바뀔 수 없습니다. qualifier는 이가 qualify한 이름에서 분리되었으며, 정확히 한 장소에서 "작성"되었습니다. 더욱이, 여러분은 이름들이 쓰여진 "종이"를 가리켜 이를 "그러한 이름들"이라 말할 수 있습니다. 다른 프로그래머와 동일한 qualifier를 선택하게 된다면, 여러분은 여전히 여러분이 선택한 qualifier에 의해 "그 이름"을 참조할 수 있습니다 - 다른 말로 하자면, 여러분이 사용할 소프트웨어가 출고된 후에도, 여러분은 qualifier를 바꿀 수 있습니다.

![rename-package.gif](../res/rename-package.gif)

 위 예제에는, 파일 LIB1과 LIB2에서 가져온 두 라이브러리가 있습니다. 두 라이브러리 설계자는, 리스프에서 패키지 이름으로 알려진, 이름 공간namespace을 위해 UTIL이란 이름을 사용하였습니다. 각 라이브러리는 client에게 보여지는 이름들을 나열했습니다. 두 라이브러리를 이용하는 프로그래머는 `MY-PACKAGE`란 패키지 이름에서 코드를 작성합니다. 각 라이브러리를 로드한후, 프로그래머는 이의 package의 이름을 다른 이름으로 바꿉니다. 그러면, `UTIL-1:INITIALIZE`와 `UTIL-2:INITIALIZE`의 호출에서 봤던 것처럼, 라이브러리에 있는 이름들은 이름이 바뀐 qualifier를 이용하여 참조됩니다. 프로그래머는 여전히 qualify 폼이 아닌 INITIALIZE 이름은 이용할 수 있다는 것을 주목하시기 바랍니다 - 이는 `MY-PACKAGE:INITIALIZE`와 동일합니다.

 리스프는 package facility라 알려진 함수와 매크로를 통해 이러한 기능을 제공합니다. `DEFPACKAGE` 매크로는 간편하게 대부분의 패키지 연산을 제공하는데 반해, `IN-PACKAGE` 매크로는 현재 패키지를 설정합니다:

``` lisp
;;;; file: util1.lisp
(defpackage util1
  (:export init func1 func2)
  (:use common-lisp))
(in-package util1)

(defun init () 'util1-init)
(defun func1 () 'util1-func1)
(defun func2 () 'util1-func2)
``` 

``` lisp
;;;; file: util2.lisp
(defpackage util2
  (:export init func1 func2)
  (:use common-lisp))
(in-package util2)

(defun init () 'util2-init)
(defun func1 () 'util2-func1)
(defun func2 () 'util2-func2)
``` 

``` lisp
;;;; file: client.lisp
(defpackage client
  (:use common-lisp)
  (:import-from util1 func1)
  (:import-from util2 func2))
(in-package client)

(defun init () 'client-init)

(util1:init)
(util2:init)
(init)
(func1)
(func2)
```

 예제는 세개의 파일의 내용물을 나열했습니다. File 1과 File 2는 모두 동일한 이름으로 3개의 함수를 정의하였습니다. File 1은 이름을 UTIL1 package에 넣었으며, File 2는 UTIL2 package를 이용합니다. `defpackage` 폼은 package의 이름을 지어줍니다. `:use` 옵션은 다른 pakcage에서 qualification없이 사용될 이름을 지정하는 반면, :EXPORT 옵션은 package에서 client에게 노출될 이름을 지정합니다.

 `defpackage` 폼은 단지 pakcage를 생성합니다. `use-package` 폼은 package를 current로 만듭니다 -  qualify가 되지 않은 모든 이름들은 current입니다. COMMON-LISP:*PACKAGE* 변수는 항상 current package를 포함합니다.

 File 3은 CLIENT package를 만듭니다. :INFORT-FROM 옵션은 UTIL1과 UTIL2 package로부터 특정 이름을 가져옵니다 - 이러한 이름들은 CLIENT package에서 qualification 없이 사용되어집니다. UTIL1나 UTIL2에서 export되었지만 CLIENT에서 import된 이름들은, form pakcage:name 의 명시적explicit qualifier를 이용하여 CLIENT에 의해 참조될 수 있습니다.

 이번 section에선 아주 기초적인 package 연산만을 다루었습니다. 31장[p 247]에서, 큰 규모의 소프트웨어 시스템을 구성에서의 package를 살펴볼때, 좀더 세부적인걸 다룰 것입니다.


## 짚고 넘어가기

- `read`
- `write`
- `read-char`
- `write-char`
- `make-array`
- `aref`
- `vector`
- `elt`
- `string`
- `type-of`
- `make-hash-table`
- `gethash`
- `remhash`
- `defstruct`
- `defpackage`
- `in-package`