# 함수형 자바스크립트를 위한 문법 다시보기 2

2-1과 이어지는 내용.

## 함수 실행과 인자 그리고 점 다시보기

`call`, `apply` , `this` 등에 대해 다시 정리해보기. 

### 인자 다시보기

> 1은 그냥 생략.

```js
function test2(a,b) {
    b = 10
    console.log(arguments)
}
test2(1) // [1]
test2(1,2) // [1,10]
```

`b=10` 에 의해서 값이 바뀐다. 

> 화살표 함수도 당연 마찬가지. 

```js
var obj1 = {
    0:1,
    1:2
}
console.log(obj1) //{0:1, 1:2}
var a = obj1[0]
var b = obj1[1]
b = 10

console.log(obj1) // not change! 
console.log(obj1[1]) //2
console.log(b) //10 얘만 바뀜.
```

**이것이 인자와 변수의 차이이다.**

> 뭐야.. 왜 모호하게 설명하고 끝나는거지!
>
> TODO : 이 부분 좀 더 자세히 정리.
>
> Q. page 78 **2에서는 두 번째 인자가 넘어오지 않았다.** 라는 부분이 뭐지? 넘긴것 같았는데 

반대로 해도 같은 결과다.

```js
function test3(a,b) {
    arguments[1] = 10
    console.log(b)
}
test3(1,2) //10
```

### this 다시	보기

```js
function test(a,b,c) {
    console.log("a b c ",a,b,c)
    console.log('this' , this)
    console.log('args',arguments)
}

var obj = {name : "obj1"}
obj.test = test
obj.test(3,2,1)
// a b c 3 2 1
// this: {name : "obj1", test:f}
// args : [3,2,1]

var a1 = [1,2,3]
a1.test = test
a1.test(3,3,3)
// a b c 3 3 3 
// this : [1,2,3]
// args : [3,3,3]
```

js 에서는 객체에 함수를 붙인 다음 그 함수를 `.` 으로 접근하여 실행하면 함수 내부의 `this` 가 **. 왼쪽의 객체가 된다.**

```js
var o1_test = obj.test
o1_test(5,6,7)
// a b c 5 6 7
// this : window 당연히, 호출 객체가 window 이니까~
// args [5,6,7]
```

그래서 아래 결과는 true 이다.

```js
console.log(test == obj.test && obj.test == a1.test)
```

자바스크립트에서의 함수는 **어떻게 선언 했느냐** 와 **어떻게 실행했느냐** 가 모두 중요하다. 

1. 어떻게 선언 했느냐 : 클로저와 스코프와 관련 된 부분 
2. 어떻게 실행 했느냐 : this와 args 결정. 



### call, apply 다시 보기.

자바스크립트에서 함수를 실행하는 방법이 두 개 더있다. 

```js
test.call(undefined,1,2,3)
test.call(null,1,2,3)
test.call(void 0 ,1,2,3)
// a b c: 1 2 3
// this: window
// args: [1,2,3]
```

`null` 이나 `undefined` 를 call 의 첫 번째 인자에 넣으면 this 는 window 가 된다. 

`void 0` 의 결과도 undefined이기 때문에 같은 결과가 나온다. 

> 이 이후에 call 설명은 그냥 저번에 했던거. 

call을 사용할 경우, 그 앞에서 함수를 `.` 으로 참조 했을지라도 `call` 을 통해 넘겨 받은 첫 번째 인자에 의해 `this` 가 결정된다. 



#### apply

`call` 과 동일하게 동작 하지만, 인자 전달 방식이 다르다. 인자들을 배열이나 배열과 비슷한 객체를 통해 전달한다. 

> 일반 객체도 가능하고, 다른 함수를 통해 생성된 arguments 도 apply 로 전달 가능.

#### call의 실용적 사례

감각적이라고 한다. 

```js
var slice = Array.prototype.slice
function toArray(data) {
    return slice.call(data) //slice 를 부르는 주체가 data로 변경. 
}
function rest(data, n) {
    return slice.call(data, n || 1) //slice 를 부르는 주체가 data로 변경.
    								//n||1 을 인자로 넘김. 
}
var arr1 = toArray({0:1, 1:2, length:2})
//[1,2]
arr1.push(3)
console.log(arr1) // [1,2,3]

rest([1,2,3])     // [2,3]
rest([1,2,3],2)   // [3]
```

> var arr2 = toArray({0:1, 1:2}) 로 콘솔에서 돌리면,
>
> [] 나온다. 
>
> 그 외에 동작은 대강 이해됨.
>
> Q. Page83 Native Helper 라는것은 무엇이지..  



## if else || && 삼항 연산자

### if 괄호 

괄호에서 할 수 없는 일들

1. 지역 변수, 지역 함수를 선언하는 것
2. 비동기 프로그래밍.

> 괄호에서 변수 값 할당하는 예제가 나오는데 .. 이상해보이는데 오픈소스에서 쓴다곤 하는데
>
> 나는 쓰지 않을 것 같은 코드이다. ; ; ; 



### || &&

> 앞에는 그냥 기본적인 예제라서 생럌쿠.. 

```js
console.log([]&&{})
// {}
console.log([]&& {} || 0)
// {}

```

> TODO : 정리해보기. 

> 이후에 나오는 if를 || 나 &&로 대체하는 코드 역시.. 좋은지 모르겠다.. 

### 삼항 연산자

삼항 연산자는 조건이 간단하고 실행 코드도 간단할 때 많이 사용한다. 

> 나는 주로 react의 render() function 에서 쓰는 듯. 



## 함수 실행의 괄호

### 함수 실행을 통해 생기는 새로운 공간

```js
var add5 = function(a) { //새로운 공간.
    return a+5
}
```

괄호를 통해 **새로운 실행 컨텍스트**가 열린다.  

> TODO : 실행 컨텍스트에 대해 자세히 정리할 때가 되었다. 

### 기본적인 비동기 상황

기본적인 비동기 상황.

```js
console.log(1)
setTimeout(function() {
    console.log(3)
},1000)
console.log(2)
//1
//2
//3 (1초 후)
```

아 이제는 잘 안쓰는 이런 코드를 다시 써볼까. 

> 나는 요새 promise 를 쓰기는 하지만.. 뭐든 무조건 맞는 건 없겠지.. 알고 있는건 좋다

```js
var add = function(a,b,callback) {
    setTimeout(()=>{
        callback(a+b)
    },1000)
}
add(10,5, (r)=>{console.log(r)})
```

`add` 의 마지막 인자로 넘겨진 익명함수 `callback` 은 `add` 안에서 모든 상황이 끝날 때 실행된다. 

이를 통해  **add를 실행한 스코프 내부에서 다시 add의 결과를 받을 수 있게 된다. **

### 함수 실행 괄호의 마법과 동기

비동기 상황을 제어 하는 방법은 함수실행을 일렬로 나열하는 것이다. 

> 이제 나올 내용이 Promise 하고 비슷하다고 한다. 
>
> 우리가 가지고 있는 함수는 강을 한번 건너갔다 와야 해서 오래걸린다고 가정하자. 

> add, sub, div 코드는 그냥 일반적인 예제라 생략. 

아래 코드는 원래 안되는 코드이다. 

```js
console.log(div(sub(add(10,5),5),10))
```

이걸 되게 해보자. 

함수가 실행되는 사이에 무언가를 할 수 있도록 함수로 한 번 감싸서 공간을 만든다.

`wrap` 에게 함수를 전달하여 함수를 리턴 받으면 원래 기능을 유지하면서 코드 사이에 공간이 생긴다. 

```js
function wrap(func) {   // 1. 함수 받기
    return function() { // 2. 함수 리턴하기. 이것이 실행됨.
    	return func.apply(null,arguments) // 3
    }
}
var add = wrap( (a,b,callback)=>{
    setTimeout(()=>{
        callback(a+b)
    },1000)
} )

add(5,10, r=>{
    console.log(r) //15
})
```



1 에서 받은 함수를 기억하는 `클로저` 를 만들어 리턴 했고, `add` 는 **2** 가 된다. 

> 담았으니까. 

나중에 2가 실행되면 1에서 받아 둔 **3** 함수를 실행하면서 2가 받은 모든 인자를 넘겨준다. 



#### 더 어려운 코드. 

```js
function _async(func) {
    return function(){
        arguments[arguments.length++] = function(result) { // 1
            _callback(result) 								//6
        }
    	func.apply(null,arguments)							//2
    	var _callback 										//3
    	function _async_cb_receiver(callback) {				//4
        	_callback = callback							//5
    	}
    	return _async_cb_receiver
  	}
}
var add = _async( (a,b,callback)=>{
    setTimeout(()=>{
        callback(a+b)
    },1000)
} )

add(20,30)(r=>{   											//7
    console.log(r)//50
})
```

> 읅 어려엌
>
> Q. _async 함수가 _async_cb_receiver을 리턴한다는 것은 이해가 되었는데, ....
>
> 나머지는 이해가 조금 어렵다 .. 흠 동작이 어떻게 되는거지. 

1. `add` 가 실행되면 인자로 20, 30 이 넘어온다. 원래는 callback 함수를 받아야 하므로, arguments에 마지막 값으로 함수를 추가 했다. 그리고 그 함수는 나중에 개발자가 넘겨준 `callback` 함수를 실행할 수 있게 준비해 두었다.

   > Q. 억지스럽다고 느낀 부분... 

2. add 를 정의할 때 받아 둔 func 함수를 **실행** 하면서 인자 3개를 넘긴다. 

   > Q. apply 에 굳이 null을 넘기면, 함수 실행의 주체가 window 에서 null로 명확히 바뀌는건가. 
   > 맞다고 해도 굳이 왜 그랬을까. ? 

3. `_callback`  이라는 지역 변수를 만들어서 1, 4가 기억해 두도록 했다. 클로저를 활용하여 서로 다른 컨텍스트가 협업할 수 있도록 이어주었다.

   > Q. yeah...? ...? ......쩜쩜...
   > **서로 다른 컨텍스트가 협업** 을 어떻게 할 수 있도록 이어주었다는걸까. 

4. _async_cb_receiver 라는 이름을 가진 name function 이자 클로저를 만들어 리턴한다. 

5. _async_cb_receiver가 실행될 때 받은 함수 `callback`  을  _callback 에 할당한다. 

6. 1초가 지나면, 1이 실행될 것이고 add 가 callback을 통해 넘긴 결과인 result 를 받아 두었던 _callback을 실행하면서 다시 넘겨주고 있다. 

7. 이 name function이 _callback 이므로, 6에서 넘겨진 r을 받게 되고 로그를ㄴ ㅏㅁ겼다......



### 비동기와 재귀

연산에 필요한 실행과 결과를 받기 위한 실행이 분리 되었다. 

> Q.... 

위의 코드에서 `func.apply(null,arguments)	` 부분만 수정해 보자. 

```js
//변경 전
func.apply(null,arguments)
//변경 후
(function wait(args){
    for(var i=0; i< args.length; i++) {
        if(args[i]&&args[i].name === '_async_cb_receiver')
            return args[i](function(args){ 
                args[i] = args; 
                wait(args) //recursive function 
            })
    }
    func.apply(null,args)
})(arguments)
```

`wait` 이라는 name function 을 만들었고, 내부에서 재귀를 돌다 `func` 를 실행하도록 변경하였다. 

재귀는 인자중에 `_async_cb_receiver` 가 있다면 모두 결과값으로 치환될 때까지 돌게 된다. 



## 화살표 함수

기본적인 부분은 생략한다.

### identity, constant

받아 둔 값을 항상 리턴하는 constatnt 같은 함수를 화살표로 만들어보자. 

```js
var identity = function(v) {
    return v
}
var identity = v=>v

var constant = function(v) {
    return function() {
        return v
    }
}
var constant = v => () => v
```

### 변화된 this

ECMAScript를 개정하면서 `arrow function` 안의 `this` 는 무조건 **lexical context** 라고 지정해버렸다.

```js
function Car() {
  this.v = 0;
  
  this.go = () => {
    engine();
  }

  this.show = () => {
    console.log(this.v);
  }
  
  var engine = () => {
   this.v++;
  }
}

var car = new Car();

car.go();
car.show();
```

결과는 **1이다.**

```javascript
//ES5
function Car() {
  this.v = 0;
  
  this.go = function () {
    engine();
  }

  this.show = function () {
    console.log(this.v);
  }
  
  function engine() {
   this.v++;
  }
}

var car = new Car();

car.go();
car.show();
```

이것의 결과는 **0이다. **

1. show를 호출한 주체는 누구? : car
2. go를 호출한 주체는 누구? : car
3. engine을 호출한 주체는 누구? : window. 

혼돈의 this 때문에, 

`() => {this.v++}` this는 함수가 선언된 위치의 바깥에 있는 this를 참고한다.



> 중간은 좀 생략하고..



결론적으로, 이 장에서 필자가 얻었으면 하는점은

**아무 곳에서나 함수 열기. 함수 실행을 원하는 시점으로 미뤄서 실행하기**

인것 같은데... 