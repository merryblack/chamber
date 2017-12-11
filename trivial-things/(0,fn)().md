## (0, function)(params...)


react-router 소스를 열어보던 중에,
리액트 관련 패키지들중에서도 유독 이녀석이 함수를 호출할 때 **(0, function)()** 와 같은 comma expression을 자주 사용해서...

구별되는 특징과 의도를 알아보기 위해서 찾아보기 시작하다가 적어보는
기본적인 자바스크립트 문법 메모장

#### 초초초 기초지식
```
var x = (0, 1, 2)
console.log(x)
```
2가 출력된다.

```
function myFunc() {
  var x = 0;
  return (x += 1, x); // the same as return ++x;
}
```
myFunc() 는 1을 리턴한다


#### 함수에 사용하면

```
c.d()
```
위 함수를 직접 실행시키면 함수의 this 는 c 를 가리킨다.
```
(0, c.d)()
```
위 코드는 결과적으로 c.d 를 리턴,실행하지만 이 함수의 this는 c 가 아니게 된다.
이 함수의 this는 global object가 된다. 즉 window

또는, strict mode 일 때는 undefined 가 된다.

결국
```
var f = function(x) { return x; };
f(c.d)();
```

또는
```
var f = c.d;
f();
```
와 같은 표현이라는 것

```
(0,eval)("var foo = 123");    // indirect call to eval, creates global variable
console.log(foo);             // 123

eval("var bar = 123");        // direct call to eval, creates local variable
console.log(bar);             // ReferenceError
```


#### 덧붙여서...

```
(1, eval)     // 는 value
eval          // 는 l-value
```
즉
```
var x;
x = 1;
(1, x) = 1; //  syntax error!
```

이렇게도 표현 가능하다
```
(1, eval)
  or
(true && eval)
  or
(0 ? 0 : eval)
```
와 같이 eval을 수행하지만 참조하지 않음.

이렇게 간접적인 방법으로 함수를 호출해서 global scope 에서 함수가 실행되도록 한다

*단 ',' 앞에 오는 값은 false류의 값들(0, '', NaN, false)은 가능하지만 null 이나 undefined이면 안된다.*
