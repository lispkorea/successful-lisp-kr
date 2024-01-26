# 레슨 08. 필수 - 매크로 정의(Essential Macro Definition)

- [원문](https://dept-info.labri.fr/~strandh/Teaching/MTP/Common/David-Lamkins/chapter03-08.html)


## DEFMACRO - 이름있는 매크로 정의

 `defmacro` 폼은 `defun` 폼과 많이 유사합니다 - 이름(name), 인자 리스트(argument list), 그리고 본체(body)를 지닙니다:

``` lisp
(defmacro name (argument ...)
   body)
```


## 매크로는 값이 아닌 폼을 반환한다

:TODO(pyoung)
 매크로 본체는 평가될 폼을 반환합니다. 다른말로 하자면, 매크로의 본체로 값이 아닌 폼을 반환하도록 작성해야 합니다. 리스프가 매크로의 호출을 평가할때, 이는 우선 매크로 본체 정의를 평가하고, 첫번째 평가의 결과를 평가합니다 (비교하여, 함수의 본체는 값을 반환하도록 평가됩니다) 여기 여러분이 가장 알고자 하는 것을 설명해주는 간단한 매크로 쌍이 있습니다:

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

 `setq-literal`는 인수가 평가되지 않는다는 점을 제외하면 마치 `setq`처럼 동작합니다(SETQ는 이의 두번째 인수를 평가한다는 것을 명심하시기 바랍니다). `SETQ-LITERAL`의 본체는 ("역따옴표"라 말하는) `로 시작하는 폼을 지녔습니다. 역따옴표는 역따옴표된 폼에서 쉼표가 보인다는 점을 제외하면 (폼으로 둘러싸인 모든 것의 평가를 숨기는)따옴표처럼 행동합니다.

 따라서 위의 `(SETQ-LITERAL A B)`를 호출함에 있어, 무슨 일이 벌어지는지가 여기 나와 있습니다:

1. PLACE를 심볼 A에 bind한다.
2. LITERAL를 심볼 B에 bind한다.
3. 다음 단계를 거처 `(SETQ ,PLACE ',LITERAL) 본체를 평가한다:
    1. 심볼 A얻기 위해 PLACE를 평가한다.
    2. 심볼 B얻기 위해 LITERAL를 평가한다.
    3. 폼 (SETQ A 'B)를 반환한다.
4. 폼 (SETQ A 'B)를 평가한다.

 반환된 폼에서 역따옴표나 쉼표는 보이지 않습니다. `SETQ-LITERAL` 호출에서 `A`나 `B`가 평가되지 않지만 서로 다른 이유 때문입니다. `A`는 `SETQ`의 첫번째 인자처럼 보이기에 평가되지 않습니다. `B`는 매크로에 의해 반환된 폼앞에 따옴표가 보이기에 평가되지 않습니다

`(REVERSE-CONS NIL A)`의 동작은 유사합니다:

1. bind REST를 심볼 NIL에 bind한다.
2. bind FIRST를 심볼 A에 bind한다.
3. 다음 단계를 거처, `(CONS ,FIRST ,REST) 본체를 평가한다:
    1. 심볼 A를 얻기 위해 FIRST를 평가한다.
    2. 심볼 NIL를 얻기 위해 REST를 평가한다.
    3. 폼 (CONS A NIL)를 반환한다.
4. 폼 (CONS A NIL)을 평가한다.

 CONS가 이의 인자를 평가하며, 매크로 본체는 두 인자에 따옴표를 붙이지 않았기에, `reverse-cons`의 두 인자는 평가됩니다. `A`는 심볼 `B`로 평가되며, `NIL`은 자기자신으로 평가됩니다.

 여러분이 평가되기전에 매크로 본체가 어떻게 보여지는지 확인하고자 한다면, `macroexpand` 함수를 이용할 수 있습니다:

``` lisp
(macroexpand '(setq-literal a b))
;;=> (SETQ A 'B)

(macroexpand '(reverse-cons nil a))
;;=> (CONS A NIL)
```

 `macroexpand`는 함수이기에 인자를 평가합니다. 때문에 확장시키고자 하는 폼에 따옴표가 있어야 합니다.

 일부러 이번 레슨의 예제를 매우 간단한 걸로 놓았으므로, 여러분은 매우 간단 기본 매커니즘을 이해할 수 있을 것입니다. 일반적으로, 매크로는 함수보다 작성하기 힘듭니다 - 20장[p 188]에서 이유와 복잡한 상황을 다룰수 있는 적절한 기법을 살펴볼 것입니다.


## 짚고 넘어가기

- `defmacro`
- `macroexpand`
- `macroexpand-1`