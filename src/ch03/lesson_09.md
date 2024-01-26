# 레슨 09. 필수 - 다중 값(Essential Multiple Values)

- [원문](https://dept-info.labri.fr/~strandh/Teaching/MTP/Common/David-Lamkins/chapter03-09.html)


## 대다수의 폼은 하나의 값만 만든다

 폼은 보통 하나의 값을 반환합니다. 리스프는 여러개의 값을 생성하거나 받을 수 있는 폼을 갖고 있습니다.


## VALUES는 여러개의 값을 생성하거나 아무것도 생성하지 않는다.

 `values` 폼은 0개 이상의 값을 반환합니다:

``` lisp
(values)
;;=>

(values :this)
;;=> :THIS

(values :this :that)
;;=> :THIS
;;=> :THAT
```

 폼의 평가에 의해 생성된 여러 라인이으로, 얼마나 많은 값이 반환되었는지 보았습니다. 위의 예제에서 3개의 `values` 폼에서 각각 0개, 1개, 2개의 값을 생성하였습니다.

 `values`는 함수이며, 따라서 인자를 평가합니다.


## 몇몇 특수한 폼은 여러개의 값을 받을 수 있다.

:TODO(pyoung)
하나의 프로그램에서 다수의 값으로 무얼 하길 원하십니까? 가장 기본적인 동작은 :

1.  각 값들을 개별 심볼로 바인드한다, 혹은
2.  값들을 리스트로 모은다.

 각 값들을 개별 심볼로 바인드하기 위해 `multiple-value-bind`를 이용합니다:

``` lisp
(multiple-value-bind (a b c) (values 2 3 5)
  (+ a b c))
;;=> 10
```

 심볼의 갯수보다 더 많은 값을 넘기면, 초과된 값은 무시됩니다:

``` lisp
(multiple-value-bind (a b c) (values 2 3 5 'x 'y)
     (+ a b c))
;;=> 10
```

 심볼의 갯수보다 더 적은 값을 넘기면, 나머지 심볼들은 `NIL`로 바인드됩니다:

``` lisp
(multiple-value-bind (w x y z) (values :left :right)
     (list w x y z))
;;=> (:LEFT :RIGHT NIL NIL)
```


## 몇몇 폼은 여러값(multiple values)그대로 넘긴다.

 몇몇 폼은 새로운 값을 생성하는 것 대신에, 마지막 값을 본체로 넘김니다. 예제는 `let`, `cond`, `defun`, `lambda`의 본체를 포함합니다.

``` lisp
(let ((a 1)
      (b 2))
  (values a b))
;;=> 1
;;=> 2

(cond (nil 97)
      (t (values 3 4)))
;;=> 3
;;=> 4

(defun foo (p q)
  (values (list :p p) (list :q q)))
;;=> FOO

(foo 5 6)
;;=> (:P 5)
;;=> (:Q 6)

((lambda (r s) (values r s)) 7 8)
;;=> 7
;;=> 8
```

 함수와 `lambda` 본체의 경우엔, "암묵적인 PROGN"라는 것으로 부터 다수의 값이 반환되었습니다. 이는, 본체가 다수의 폼을 포함할 수 있으며, 오직 마지막 form의 값이 반환될거라고, 멋지게 말하는 방법입니다.

 여러분은 이러한 행동을 원할때 `progn` 스페실 폼을 이용할 수 있습니다. `(PROGN form1 form2 ... formN)` 은 순서대로 `form1`부터 `formN`까지 평가하고, 최종적으로 `formN`의 값을 반환합니다.


## 짚고 넘어가기

- `values`
- `multiple-value-bind`
- `progn`