# 레슨 08. 필수 - 매크로 정의(Essential Macro Definition)

- [원문](https://dept-info.labri.fr/~strandh/Teaching/MTP/Common/David-Lamkins/chapter03-08.html)


## DEFMACRO - 이름있는 매크로 정의

 `defmacro` 폼은 `defun` 폼과 많이 유사합니다 - 이름(name), 인자 리스트(argument list), 그리고 본체(body)를 지닙니다:

``` lisp
(defmacro name (argument ...)
   body)
```


## 매크로는 값이 아닌 폼을 반환한다

 매크로 본체는 평가될 폼을 반환합니다. 다른 말로 하자면, 매크로의 본체로 값이 아닌 폼을 반환하도록 작성해야 합니다. 리스프가 매크로의 호출을 평가할때, 이는 우선 매크로 본체 정의를 평가하고, 첫번째 평가의 결과를 평가합니다 (이에 비해, 함수의 본체는 값을 반환하기 위해 평가됩니다).
 
다음으로 저희가 알아야 할 대부분의 내용을 설명하는 몇 가지 간단한 매크로들이 있습니다:

``` lisp
(defmacro setq-literal (place literal)
  `(setq ,place ',literal))
;;=> SETQ-LITERAL

(setq-literal a b)
;;=> B

a
;;=> B

(defmacro reverse-cons (rest first)
  `(cons ,first ,rest))
;;=> REVERSE-CONS

(reverse-cons nil A)
;;=> (B)
```

 `setq-literal`는 인수가 평가되지 않는다는 점을 제외하면 마치 `setq`처럼 동작합니다(SETQ는 이의 두번째 인수를 평가한다는 것을 명심하시기 바랍니다). `SETQ-LITERAL`의 본체는 ("역따옴표"라 불리는) `` ` ``로 시작하는 폼을 지녔습니다. 따옴표처럼 역따옴표로 묶인 모든 폼의 평가를 억제하지만, 역따옴표 안에서 쉼표가 나타나는 경우는 예외입니다. 쉼표 뒤에 오는 심볼은 평가됩니다.

 따라서 위의 `(SETQ-LITERAL A B)`를 호출함에 있어, 무슨 일이 벌어지는지 여기 나와 있습니다:

1. 심볼 `A`를 `PLACE`에 바인드한다.
2. 심볼 `B`를 `LITERAL`에 바인드한다.
3. 다음 단계를 거처 `` `(SETQ ,PLACE ',LITERAL) `` 본체를 평가한다:
    1. `PLACE`를 평가하여 심볼 `A`를 얻는다.
    2. `LITERAL`를 평가하여 심볼 `B`를 얻는다.
    3. 폼 `(SETQ A 'B)`를 반환한다.
4. 폼 `(SETQ A 'B)`를 평가한다.

 반환된 폼에서 역따옴표나 쉼표는 보이지 않습니다. `SETQ-LITERAL` 호출에서 `A`나 `B`가 평가되지 않지만 서로 다른 이유 때문입니다. `A`는 `SETQ`의 첫번째 인수이기에 평가되지 않습니다. `B`는 매크로에 의해 반환된 폼앞에 따옴표(`'`)가 보이기에 평가되지 않습니다

`(REVERSE-CONS NIL A)`의 동작은 유사합니다:

1. 심볼 `NIL`을 `REST`에 바인드한다.
2. 심볼 `A`를 `FIRST`에 바인드한다.
3. 다음 단계를 거처, `` `(CONS ,FIRST ,REST) `` 본체를 평가한다:
    1. `FIRST`를 평가하여 심볼 `A`를 얻는다.
    2. `REST`를 평가하여 심볼 `NIL`를 얻는다.
    3. 폼 `(CONS A NIL)`를 반환한다.
4. 폼 `(CONS A NIL)`을 평가한다.

 `CONS`는 이의 인수들을 평가하며, 매크로 본체에서 두 인자에 따옴표를 붙이지 않았기에, `reverse-cons`의 두 인수는 평가됩니다. `A`는 심볼 `B`로 평가되며, `NIL`은 자기자신으로 평가됩니다.

 여러분이 평가되기전에 매크로 본체가 어떤지 확인하고자 한다면, `macroexpand` 함수를 이용할 수 있습니다:

``` lisp
(macroexpand '(setq-literal a b))
;;=> (SETQ A 'B)

(macroexpand '(reverse-cons nil a))
;;=> (CONS A NIL)
```

 `macroexpand`는 함수이기에 인자를 평가합니다. 때문에 확장시키고자 하는 폼 앞에 따옴표가 있어야 합니다.

 일부러 이번 레슨의 예제를 매우 간단한 걸로 놓았으므로, 여러분은 매우 간단 기본 매커니즘을 이해할 수 있을 것입니다. 일반적으로, 매크로는 함수보다 작성하기 힘듭니다 - 20장[p 188]에서 그 이유와 그러한 복잡한 상황을 다룰 수 있는 적절한 기법에 대해 살펴보도록 하겠습니다.


## 짚고 넘어가기

- `defmacro`
- `macroexpand`
- `macroexpand-1`