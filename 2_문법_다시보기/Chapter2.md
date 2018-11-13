# 함수형 자바스크립트를 위한 문법 다시보기

자바스크립트 ! 다시! 보기! 

> 기초를 다지자.

## 1. 객체와 대괄호 다시보기

### 1. 난해해 보이는 문법들을 확인하는 목적 

문법에 따른 세밀한 기능 차이에 대해 알고 있다면 난해하고 변칙적인 문법이 사용 된 오픈 소스를 읽기가 훨씬 좋다. 

```
아무 곳에서나 함수 열기, 함수 실행을 원하는 시점으로 미뤄서 실행하기
```

**이것을 가능하게 한다.**

### 2. 객체와 key

> 내가 정리하는 목적으로 한번 타이핑 해보는 것으로 괜찮을 것 같다.

```javascript
var obj = {a:1, "b":2}
obj.c = 3
obj['d'] = 4
var e = 'e'
obj[e] = 5
function f() {return 'f'}
obj[f()] = 6
console.log(obj)
```

실행결과는?

```
{a :1, b :2, c:3, d:4, e:5, f:6}
```

띄어 쓰기나 특수문자, 숫자를 가리지 않고 가능하다.



**{} 안쪽에서 key를 선언하는 것과 [] 안에서 선언하는 것은 어떤 차이가 있을까?**

> `{}` 의 문자열 부분에서는 코드를 실행할 수 없고, `[]` 안에서는 코드를 실행할 수 있다.

```javascript
var obj5 = { (true ? "A" : "b") :1 } //불가능.
var obj6 ={}
obj6[ture ? "a" : "b"] = 1 // 가능
```

> 호이스팅과 배열에 key 사용하기 넘어감.

이건 결과가 궁금해서 쳐봄.

```javascript
var obj = []
obj[0] = 1
obj[2] = 3
```

결과는 ?? 

```
> obj11
[1, empty, 3]
```

> 책에 꿀팁같은 것이 있다.
>
> arr.push(1) 보다 arr[i] =1 이 ie에서는 5배의 성능 차이가 난다.

#### +Aplha : 한번에 크기 늘리면서 undef로 채워진 배열 만들기 

```javascript
Array.apply(null, {length : 3})
```

> TODO : apply docs 읽어볼 것. 



#### 책에서 오류가 있는 것 같은 부분

```javascript
var obj13 = [1,2]
obj13[5] = 5
//크롬에서 콘솔키고 실행하면 뭐가 나오게요 ?
```

![image](https://user-images.githubusercontent.com/18658235/48407071-59e46700-e779-11e8-9447-f39d4672088d.png)

```javascript
//[1,2, 5:5] 같은 표현인가 ? 그런듯. 
```



## 2. 함수 정의 다시보기

### 1. 기본 정의

자바스크립트에서 함수를 정의하는 방법은 다양하다. 대표적인 방법들은 다음과 같다. 

```javascript
function add1(a,b) {
    return a+b
}
var add2 = function(a,b) {
    return a+b
}
var m = {
    add3: function(a,b) {
        return a+b
    }
}
```

### 2. 호이스팅

> 면접 준비하면서 정리했던 자료를 가져온다! 

자바스크립트는 `함수-수준(function-level)`의 범위를 가집니다. 함수내에 정의된 변수는 지역 범위를 가지며, 해당 함수와 내부 함수에서만 접근이 가능합니다. 

> es5 기준 

#### 변수 호이스팅

모든 변수 선언은 호이스트 된다. 

호이스트란, **변수의 정의가 그 범위에 따라 선언과 할당으로 분리** 되는 것을 말한다.

- 함수 내에서 선언 되었을 경우 : 함수의 최 상위
- 함수 바깥에서 선언 되었을 경우 : 전역 컨텍스트의 최 상위 



다만, 오해하지 말자. **선언** 이 최상위로 가는 것이다.

```javascript
function showName() {
     console.log("First Name : " + name);
     var name = "Ford";
     console.log("Last Name : " + name);
}
showName();
// First Name : undefined
// Last Name : Ford
// First Name이 undefined인 이유는 지역변수 name이 호이스트 되었기 때문입니다.

```

이 코드는 자바스크립트 엔진에 의해 다음과 같이 해석 된다.

```javascript
function showName() {
     var name; // name 변수는 호이스트 되었습니다. 할당은 이후에 발생하기 때문에, 이 시점에 name의 값은 undefined 입니다.
     console.log("First name : " + name); // First Name : undefined
     name = "Ford"; // name에 값이 할당 되었습니다.
     console.log("Last Name : " + name); // Last Name : Ford
}
```

호이스트 되었을때, 함수 선언은 변수선언을 덮어 씁니다.

```javascript
// 다음 두 변수와 함수는 myName으로 이름이 같습니다.
var myName; // string
function myName() {
     console.log("Rich");
}
// 함수 선언은 변수명을 덮어 씁니다.
console.log(typeof myName); // function
```

하지만, 변수에 값이 할당될 경우에는 반대로 변수가 함수선언을 덮어 씁니다.

```javascript
var myName = "Richard";
function myName() {
     console.log("Rich");
}
console.log(typeof myName); //string

```

##### 참고

[변수호이스팅](https://m.post.naver.com/viewer/postView.nhn?volumeNo=8431285&memberNo=34176766)



#### 함수 호이스팅

```javascript
add(2, 3); // add is not a function
var add = function (a, b) {
  return a + b;
};
add(4, 5);
```

위 코드의 실행 결과는 **add is not a function** 이다. 위 코드를 실행할 때 자바스크립트 엔진 관점에서 호이스팅을 적용하여 코드 순서를 변경해보면 아래의 결과가 된다.

```javascript
var add;
add(2, 3);
add = function (a, b) {
  return a + b;
};
add(4, 5);
```

아래 코드는 오류나 undef가 없다.

```javascript
console.log(add1) // add1 그대로 출력
console.log(add2) // undefined

function add1(a,b) {return a+b}
var add2 = function(a,b) {return a+b}
```

##### 참고

[캡틴판교블로그]

#### 괄호 없이 즉시 실행

일반적인 즉시 실행 방식은 아래와 같다. 

```javascript
(function(a){
    console.log(a)
})(100)
//처음 보는 것. 
```

아래 코드는 `Syntax Error `가 발생한다. 

```javascript
function(a) {
    console.log(a)
}(100)
```

에러가 난 이유는 익명 함수를 잘못 실행한 것이 아니라, **익명 함수 선언 자체가 실패했기 때문** 이다. 

```javascript
function() {} //fail
```

#### new Function 이나 eval ? 

`new Function `이나 `eval` 을 클라이언트 단에서만 사용한다면 보안적인 문제가 생기지 않는다.

>  TODO : 왜 ? 

우선 `eval` 과 `new Function` 의 사용법을 간단히 익히고 넘어간다. 

```javascript
var a = eval('10' + '5')
console.log(a)
//15
var add = new Function('a,b', 'return a+b')
add(10,5) //15
```

>  TODO : 따로정리. 



> 사실 중간 내용이.... 생략..



## 7. named 함수

익명이 아닌, 이름을 지은 함수를 `named function ` 이라고 한다. 

아래는 익명 함수에서 함수 자신을 참조하는 방법들이다. 

```javascript
var first = function() {
    console.log(first)
}
//위험 상황
var second = first
//second 결과는
//f() {console.log(first)}

first = 'hi!'
second()
// hi!
```

`위험상황` 부분 처럼 함수 생성 이후 변경이 일어나면 더 이상 자기 자신을 참조하지 못하게 될 수 있다. 



> TODO : ??????? 흠..

```javascript
var f1 = function f(){
    console.log(f)
}
f1()
/*
function f(){
  console.log(f)
}
*/
var f2 = f1
f1 = null
f2()
/*
function f(){
  console.log(f)
}
*/
```

아주 아주 안전하고 편한 자기참조 코드가 아래 있다. 

```javascript
var hi = 1
var hello = function hi() {
    console.log(hi)
}
hello()
// function 잘 나옴.
console.log(hello.name === 'hi') // true
console.log(hello.name === hi) //false

var z1 = function z(){
    console.log('zzz')
}
//...
//z; : reference error  
```



## named function 을 이용한 재귀 

깊이를 가지는 배열을 펴 주는 `flatten` 함수다.

```javascript
function flatten(arr) {
    return function f(arr, new_arr) {   //1
        arr.forEach(function(v){
            Array.isArray(v) ? f(v, new_arr) : new_arr.push(v) //2
        })
        return new_arr
    }(arr, []) //3
}
flatten([1,[2],[3,4]])
//[1,2,3,4]
flatten([1,[2],[[3],4]])
//[1,2,3,4]
flatten([1,[[2],[[3],[[4],5]]]])
//[1,2,3,4,5]
```

1. flatten 함수가 실행 되면, 먼저 즉시 실행할 `f` 라는 이름의 named function 으로 만든다... ? 

2. 함수 `f` 를 즉시 실행 하면서, 새로운 배열 객체를 생성하여 넘겨준다. 

   > new_arr 

3. 루프를 돌면서, **배열이 아닐때 에만** `push` 를 하고, 배열인 경우에는 **f를 다시 실행한다. **



만약 `return` 형태가 없다면..

```javascript
function flatten2(arr,new_arr) {
    arr.forEach(function(v){
        Array.isArray(v) ? flatten2(v,new_arr) : new_arr.push(v)
    })
    return new_arr
}
flatten2([1,[2],[3,4]] , [])
```



> 흠 완벽히 이해되지는 않는군? 















`









