# 레슨 07. 필수 - 함수 정의(Essential Function Definition)

- [원문](https://dept-info.labri.fr/~strandh/Teaching/MTP/Common/David-Lamkins/chapter03-07.html)


## DEFUN - 이름을 가진 함수 정의

 `defun` 폼을 이용하여 이름을 가진 함수를 정의할 수 있습니다:

``` lisp
(defun secret-number (the-number)
  (let ((the-secret 37))
    (cond ((= the-number the-secret) 'that-is-the-secret-number)
          ((< the-number the-secret) 'too-low)
          ((> the-number the-secret) 'too-high))))
;;=> SECRET-NUMBER
```

 레슨 3에서 `let`, `cond`, `'`(quote의 별칭)를 다루었습니다. 숫자 비교 함수들은 그 의미가 명확합니다.

 `defun` 폼은 3개의 인자를 가집니다:

1.  함수의 이름: `secret-number`
2.  인자 이름 리스트: 이것이 호출되엇을시 함수의 인자가 될 `(the-number)`
3.  함수의 본체: `(let ...)`

 3개 모두 각각 그 그대로 나타내어야 하므로, `defun`은 인자를 평가하지 않습니다 (만일 평가를 수행한다면, 각 인자에 `quote`를 붙여야만 하는 불편함이 생길 것입니다).

 `defun`은 정의된 함수 이름을 반환하고, 여러분이 제공한 이름, 인자 리스트, 본체를 이용하여 전역으로 정의를 선언합니다. `defun`을 이용하여 함수를 만들면, 바로 이를 사용할 수 있습니다:

``` lisp
(secret-number 11)
;;=> TOO-LOW

(secret-number 99)
;;=> TOO-HIGH

(secret-number 37)
;;=> THAT-IS-THE-SECRET-NUMBER
```

 함수를 호출할때, 이것의 인자는(예. 두번쩨 예제에서 `99`) 정의에서 사용한 인자 이름(즉. `the-umber`)에 바인딩이 됩니다. 그런 다음, 함수의 본체 (즉. (LET ...))은 해당 인자의 바인딩 컨텍스트로 평가됩니다. 다른 말로 하자면, `(SECRET-NUMBER 99)`를 평가하는 것은 변수 `THE-NUMBER`로 `99`가 바인딩 된체 `SECRET-NUMBER` 함수 본체를 실행하도록 만들어 줍니다.

 물론, 여러분은 하나 이상의 인자를 지닌 함수를 정의 할 수 있습니다.

``` lisp
(defun my-calculation (a b c x)
  (+ (* a (* x x))
     (* b x)
     c))
;;=> MY-CALCULATION

(my-calculation 3 2 7 5)
;;=> 92
```

:TODO(pyoung)
 함수를 호출할때 인수들은, 순서에 맞게 인자 이름에 바인딩 됩니다. 리스프는 인자 이름의 list에 대한 몇몇 선택적인 변형variations을 지닙니다. 공식적으로 이러한 리스트를 lambda 리스트라 부릅니다 - 21장[p 198]에서 이것의 다른 기능들에 대해 살펴 볼 것입니다.


## LAMBDA - 이름 없는 함수 정의

 때로는 프로그램에서 단 한곳에서만 쓰이는 함수가 필요할 것입니다. `defun`으로 함수를 만들어 이를 한번 호출할 수 있습니다. 때때로, 함수에 나중에 프로그램을 읽을때 도움을 주는 descriptive 이름을 줄 수 있으므로, 이는 할 수 있는 최선이 됩니다. 그러나 종종 여러분이 필요한 함수는 아주 사소하거나 아주 명백하여, 이름을 짓고싶지 않거나 이전에 이름이 쓰였는제 대해 걱정하지 않고자 할 것입니다. 이와 같은 상황에서, 리스프는 `lambda` 폼을 이용하여 이름이 붙여지지 않는 함수를 만들 수 있게 합니다. `lambda` 폼은 마치 이름이 없는 `defun` 폼처럼 보입니다:

``` lisp
(lambda (a b c x)
   (+ (* a (* x x))
      (* b x)
      c))
```

 `lambda` 폼을 평가할 수 없습니다; 이는 리스프가 함수를 찾을 수 있다고 예상하는 곳에서 보여야 합니다 - 보통 form의 첫번째 오소로;

``` lisp
(lambda (a b c x)
     (+ (* a (* x x))
        (* b x)
        c))
;;>| Error

((lambda (a b c x)
      (+ (* a (* x x))
         (* b x)
         c))
   3 2 7 5)
;;=> 92
```


## 짚고 넘어가기

- `defun`
- `lambda`