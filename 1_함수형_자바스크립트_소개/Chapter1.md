# 0. 나는 왜 이 스터디를 하는가.

잘 짜고싶다.

유기적으로 슉샥슉 읽기 좋은 코드, 퍼포먼스가 좋은 코드를 짜고 싶다. 

새롭고 반짝이는 기술 말고, 기본기를 다지고 싶다. 튼튼한 개발자가 되어야지

# 1. 함수형 자바스크립트 소개

함수를 리턴하거나 괄호가 만흔 코드들을 처음 보는데, 참 난해하다. 3장정도까지 읽다가 메모하지 않으며 읽는 것은 의미가 없을 것 같아서 정리를 시작한다!

# 1.1 먹는건가..

싶다. `for` 와 `if` 의 향연만으로 코드를 짜던 나는 어렵다. 

### 1.1.1 클로저가 등장하는 예제 등장 

```javascript
function addMaker(a) {
    return function(b) {  //1 
        return a+b
    }
}
addMaker(10)(5)
/*
addMaker는 1을 반환하는 함수이다. 
addMaker(10) 의 결과가 함수이므로, 
( addMaker(10))(5) 와 같은 표현이다. 
*/
```

`addMaker(10)` 는 **함수** 이므로 `func` 처럼 써도 무방하다. 

따라서, `addMaker(10)(5)` 는 `func(5)` 이다. 

> 처음보는 ()() 꼴이 약간 혼란스러웠다. 



### 1.1.3 값으로써의 함수와 클로저 

> 클로저에 대한 내용을 따로 정리해보자 (+a)



## 1.2 집중해야 하는 함수형 js의 실용성 챕터

여기서부터 3장까지 읽다가 다시 1장으로 돌아왔다. 무한루프같은 함정에 빠질것 같다.. 따흐흙.... 

### 1.2.1 회원 목록 중 여러 명 찾기 .

```javascript
const users = [
    {id : 1, name : "ID" , age : 32} //이하생략
]
```

원래대로라면, 그냥 `for` 써서 일일히 찾았을 case. 



### 1.2.2 for To filter, if To predicate

```javascript
function filter(list, predicate) {
    let newList = []
    for(let i=0; i<list.length; i++) {
        if(predicate(list[i])) newList.push(list[i])
    }
    return newList
}
```

`predicate` 의 인자로 `list[i]` 가 넘어가고 loop을 돌며 뭘 처리한다. 

그럼 이제, **무언가 검사하는 것은 predicate 에 위임할 수 있다.** 

```javascript
const userName = filter(users, user => user.age < 30)
/*
predicate : user => user.age <30
predicate(list[i]) 에서 list[i] 가 user이다. 
*/
```

Awesome! 좋아졌다. 

### 1.2.3 다른 관점으로 filter 보기. 

함수형 프로그래밍에서는, **항상 동일하게 동작하는 함수** 를 만들고 보조 함수를 조합하는 형태로 로직을 완성한다.  

> 많은 이야기가 있었지만 객체지향과 비교하며 쓴 부분은 와닿지 않아서 패스.

### 1.2.4 map 함수 

이번에 리팩토링할 함수는, user의 나이가 30 이상과 미만인 경우에 각각 나이와 이름을 출력하려고 한다. 

막 짜는건 쉽다. for 돌려서 `.age ` `.name` 하면 된다. 근데 정말 약간만 다르고 로직은 똑같을 것이 뻔하다. 

**일을 위임하는 식** 으로 짜자. 

```javascript
function map(list, iteratee) {
    let newList = []
    for(let i=0; i<list.length; i++) {
        newList.push(iteratee(list[i]))
    }
    return newList
}
```

newList에 **무엇을 넣을 것인가** 는 `iteratee`에게 위임한다.  

```javascript
const re1 = filter(users, user => user.age < 30) //배열 return 
const ages = map(re1,user => user.age) //30미만인 배열을 넘기고, 거기서 age만 다 리턴하기. 
```

### 1.2.5 조금 더 멋지게 써보기.

잊지말자. `filter`함수의 return은 어차피, 배열이다.

`map(re1,user => user.age)` 이 부분의 첫 번째 인자인 re1 도 배열이다. 

있어보이게 한줄로 써보자. 

```javascript
       //30미만인 배열을 return한다.         //age반환함수 넘기기 
map(   filter(users,user=>user.age<30) , user=>user.age)
```

Awesome!

> 로그함수는 복잡하니까 이정도로 이해해도 괜찮을 것 같다. 

### 1.2.6 함수를 값으로 다룬 예제의 실용성

줄이면, 덜읽고, 협업에 좋다.

> 개발자가 두명인 프로젝트에서도 내가 너무 길고 못짜서 처음에 힘들었다. 

```javascript
function bvalue(key) {
    return function(obj){
        return obj[key]
    }
}

bvalue('a',({a:'hello',b'world'}))
```

[addMaker](1.1.1 클로저가 등장하는 예제 등장 ) 와 상당히 똑같은 함수이다.  

map 과 같이 사용해보자. 

>  map은 iteratee의 결과에 따라 새로운 배열을 리턴하는 함수이다. 

```javascript
map( filter(users, user=>user.age<30 ), bvalue('name'))
```

map 이 사용하는 함수를 `bvalue`가 리턴하는 함수로 대체한다 .

>  iteratee === `function(obj) return obj[key]`

이 코드의 동작을 정리해보자면, `filter` 에서 user.age가 30미만인 배열을 만들고, 그 배열의 `name` 만 뽑는다.  



## 1.3 함수형 자바스크립트의 실용성 2

응용인가보다. 

### 1.3.1 회원 목록 중 한 명 찾기 

회원 목록 중, 특정 조건을 가진 회원만 찾고 싶다. 예를 들면 id값으로 .

`filter` 함수를 활용하면 되지만, filter 함수는 무조건 list.length 만큼 실행되기 때문에 효율적이지 못하다. 

차라리 `for`에 `break` 를 거는 것이 낫다.

```javascript
let user
for(let i=0; i<users.length; i++) {
    if(users[i].id ===3) {
        user = users[i]
        break
    }
}
```

원하는 user를 찾은 후 break 로 빠져나오는 일반적인 코드이다. 

하지만 **재사용이 불가능** 하다. 재사용이 가능하도록 해보자.

```javascript
function findById(list, id){
    for(let i=0; i<list.length;i++) {
        if(list[i].id === id) return list[i]
    }
}
```

근데 이것도, **id** 찾기만 가능하다. 별로다.

general한 함수로 바꿔보자.

```javascript
function findBy(key, list, val) {
    for(let i=0; i<list.length; i++) {
        if(list[i].key === val) return list[i]
    }
}
```

하지만 이것도, 아래와 같은 상황에서는 안된다. 

```javascript
function User(id, name, age) {
    this.getId = function() {
        return id
    }
    this.getName = function() {
        return name
    }
}
const user = [
    new User(1,"id",32)
]
```

이 코드를 보면 user의 id를 `getId`로 얻어와야 하기 때문에 대응할 수 없다. 

더 나은 함수를 만들어 보자.



### 1.3.2 값에서 함수로

`filter`나 `map` 처럼 인자로 키와 값 대신 함수를 사용해 보자. 

```javascript
function find(list, predicate) {
    for(let i=0; i<list.length; i++) {
        if(predicate(list[i])) return list[i]
    }
}
console.log(
	find(user, u=> return u.getID()===25).getName()
)
```

`find` 는이제 배열에 어떤 값이 들어 있든 사용할 수 있게 되었다. 

마지막으로 awesome 하게 써보자.

```javascript
//map 설명
//(list, iteratee)를 인자로 받음 
//newList.push(iteratee(list[i]))
     // list                     //iteratee
map( filter(users, u=>u.age>30) , u=>u.name )
```

### 1.3.3 함수를 만드는 함수와 find, filter 조합하기

```javascript
function bmatch(key, val) {
    return function(obj) {
        return obj[key] === val
    }
}
console.log( find(users, bmatch('id',1))  )
```

> 나는 아무래도 클로저에 대한 이해가 부족한것 같다. 

bmatch의 실행 결과는 **함수**다. key 와 val을 미리 받아서 나중에 들어올 obj와 비교하는 익명함수를 클로저로 만들어 리턴한다.

> 응? ?  ?  ? 클로저 정리 진짜 해야할때다. 

bmatch는 함수를 리턴하기 때문에 filter나 map 과 조합이 좋다. 



여러 개의 key에 해당하는 value들을 비교하는 함수를 만들어 보자. 

```javascript
function object(key,val) {
    let obj = {}
    obj[key] = val
    return obj
}
function match(obj, obj2) {
    for(let key in obj2) {
        if(obj[key] !== obj2[key]) return false
    }
    return true
}

function bmatch(obj, vla) {
    //arguments.length 속성은 실제로 함수에 전달된 arguments 의 수를 제공합니다.
    //obj2를 만들어줌.
    if(arguments.length === 2) obj2 = object(obj2,val) 
    return function(obj) {
        return match(obj,obj2)
    }
}

// 어려운 코드 ...
console.log( match(find(users,bmatch('id',3)) , find(users,bmatch('name','BJ'))  ) 
```

`find`를 고쳐서 `findIndex` 를 만들 수 있다. 

```javascript
function findIndex(list, predicate) {
    for(let i=0; i<list.length; i++) {
        if(predicate(list[i])) return i
    }
    return -1
}
```

잘 만들었다.

### 1.3.4 고차함수

고차 함수란, 함수를 인자로 받거나 함수를 리턴하는 함수를 말한다. 보통 고차 함수는 함수를 인자로 받아, **필요한 때 실행하거나 클로저로 만들어 리턴한다.**

> 끊임없는 클로저 이야기

아직 lodash 나 Undersocre.js 를 많이 써보지 않았지만, ~~책에 있으니까 써보자.~~ 만들어 보자. 

```javascript
_.map = function(list, iteratee) {
    let newList = []
    for(let i=0; i<list.length; i++){
        newList.push(iteratee(list[i], i, list))
    }
}
_.filter = function(list , predicate) {
    let newList = []
    for(let i=0; i<list.length; i++) {
        if(predicate(list[i],i,list)) newList.push(list[i])
    }
}
                                 //val = list[index]
console.log( _.filter([1,2,3,4], (val,index)=>index>1  ) )
```

### 1.3.5 쓸모없어 보이지만 쓸모 있다고 하는 함수

`Underscore.js` 에 있는 함수라고 한다. ~~진짜 쓸모없어 보인다.~~

```javascript
_.identity = v=>return v
const a = 10
console.log(_.identity(a)) //10
```

뭔가 싶다. 

**결론을 스포하자면, undef나 null 체크에 좋다.**

```javascript
console.log( filter([true,0,'10',false,null]), _.identity )
//[true,'10']
```

> 질문이다. 어렵다. 모르겠다; !! 왜하지 
>
> 해결 되었다. 

[!! 쓰는 이유](https://hermeslog.tistory.com/279 )



정리하자면 느낌표 두개(!!) 연산자는 **확실한 논리결과**를 가지기 위해 사용합니다.
예를 들어 정의되지 않은 변수 undefined 값을 가진 내용의 논리 연산 시에도 **확실한 true / false**를 가지도록 하는게 목적입니다.

```javascript
_.some = function(list) {
    return !!_.find(list,_.identity)
}
_.every = function(list) {
    return _.filter(list, _.identity).length == list.length
}
```

`_.some` 은 배열에 들어 있는 값 중 하나라도 긍정적인 값이 있으면 `true` , 하나도 없다면 `false` 를 리턴한다.

`_.every` 는 모두 긍정적인 값이어야 `true` 를 리턴한다. 

그런데, `_.every` 는 좀 루프를 끝까지 돌아서 좀 비효율 적이다. 



### 1.3.6 연산자 대신 함수로 

아주 작은 함수. tiny 함수.

```javascript
function not(v) return !v
function beq(a) {
    return function(b) {
        return a === b
    }
}
```

굳이 이런것 까지 함수로 만들어야 할까.. 라고 싶지만 일단 따라가자. 

```javascript
_.some = function(list) {
    return !!_.find(list , _.identity)
}
_.every = function(list) {
    return beq(-1)(_.find(list,not))
}
```

not은 연산자 !가 아닌 함수이기 때문에, `_.findIndex` 와 함께 사용할 수 있다. 

list의 값 중 하나라도 부정적인 값을 만나면, `predicate` 가 `not`이므로 부정적인 값을 가진 해당 아이템의 `index` 를 리턴한다. 

그렇게 되면, -1과 비교했을 때, `false`이므로 `_.every` 의 값도 false가 된다. 

**함수가 가능하면 하나의 일만 하게 하자.**

### 1.3.7 함수 합성

함수를 쪼갤수록 함수 합성은 쉬워진다. 

Underscore.js 의 `_.compose` 는 오른쪽의 함수의 결과를 바로 왼쪽 함수에게 전달한다. ... 어쩌구 저쩌구.... 

> 질문이다... 나중에 뭔지 다시보자..

```javascript
//underscore.js 중 일부 발췌
_.compose = function() {
    const args = arguments
    let start = args.length -1
    return function() {
        let i = start
        let result = args[start].apply(this, argumetns)
        while(i--) result = args[i].call(this, result)
        return result
    }
}

const great = function(name) {return "hi"+name}
const exclaim = function(statement){return statement.toUpperCase()+"!"}
const welcome = _.compose(great, exclaim)
//'hi: MOE!'
```

compose는 이해하지 못했다.



코드에 인자와 변수가 등장하지 않고 함수의 내부가 보이지 않는다는 것은 개발자가  **예측하지 못할상황이 없다**는 말이다. 

에러 없는 함수들이 인자와 결과에 맞게 잘 조합되어 있다면 전체의 결과 역시 에러가 날 수 없다. 

작게 쪼게두자. 내가 짠 코드도 30분만 지나면 ~~2주라고 하자.~~ 못알아 보는 경우도 많은데... 언제 다 유지보수하냐. 



## 1.4 다시 기초

일급 함수, 클로저, 고차 함수, 콜백 패턴, 부분 적용, this, arg 객체, 함수 객체의 메서드(bind, call, apply) 등에 대해 알고 가야 한다. 

> 진짜 진짜 맞는 이야기.

### 1.4.1 일급 함수

자바스크립트에서 함수는 **일급 객체이자 일급 함수** 이다. 자바스크립트에서 객체는 **일급 객체** 이다.  

여기서 `일급`은 `값으로 다룰 수 있다` 는 의미로, 아래와 같은 조건을 만족해야 한다. 

- 변수에 담을 수 있다.
- 함수나 메서드의 인자로 넘길 수 있다.
- 함수나 메서드에서 리턴할 수 있다. 



일급 함수는 아래와 같은 조건을 추가적으로 만족한다.

- 아무 때나 (런타임에서도) 선언 가능 하다.
- 익명으로 선언할 수 있다.
- 익명으로 선언한 함수도 함수나 메서드의 인자로 넘길 수 있다.



자바스크립트의 함수는 위 조건을 모두 만족하는 일급 함수이다.



### 1.4.2 클로저

> 오예 클로저 정리 나온다!

**정확히 이해하자.** ~~새벽 한시 반에 이해해보는 클로저.~~

함수는, 번수 참조 범위를 결정하는 중요한 기준이다. 함수가 중첩되어 있다면 스코프들 역시 중첩되어 생겨난다.

```
클로저는 자신이 생성될 때의 환경을 기억하는 함수다.
```

''클로저는 자신의 상위 스코프의 변수를 참조할 수 있다.' 라고도 말할 수 있다. 

조금더 정확히 말하자면,

```
클로저는 자신이 생성될때의 스코프에서 알 수 있었던 변수를 기억하는 함수이다. 
```

자바스크립트의 모든 함수는 글로벌 스코프에 선언되거나 함수 안에서 선언된다. 

```
클로저로 만들 함수가 myFunc 라면, myFunc내부에서 사용하고 있는 변수중에 myFunc내부에서 선언되지 않은 변수가 있어야 한다. 그 변수를 a라고 한다면, a라는 이름의 변수가 myFunc를 생성하는스코프에서 선언되었거나 알 수 있어야 한다.
```

> 난해한 문장이다. 질문...
>
> scope 와 관련하여 (this를 정리한 글이긴 하지만 ) 
>
> [Javascript_this](https://github.com/SoYoung210/Study_note/blob/master/Javascript/2_this.md)

```javascript
function parent() {
    const a = 5
    function myFunc() {
        console.log(a)
    }
}
//or
function parent2(){
    const a=5
    function parent1(){
        function myFunc() {
            console.log(a)
        }
    }
}
```

`parent` 와 `parent2` 의 `myFunc`  에서는 `a` 라는 변수를 선언하지 않았지만 사용하고 있다. 

> this.a 는 안되겠네. 

`parent` 의 변수 `a` 는 `myFunc` 을 생성하는 스코프에서 정의되었고, `parent2`의 변수 `a`는 `myFunc` 를 생성하는 스코프의 상위 스코프에 정의되어 있다. 

**참조할 수 없지만, 참조되도록 유지시켜주는 함수** 라고 정의할 수 있지 않을까.

```javascript
const a = 10
const b = 20
function f1() {
    return a+b
}
f1() //30
```

f1 은 a와 b를 찾기위해 우선 자신의 scope를 검사하고, 없으면 global scope를 참조한다. 

> 지인에게 설명받은 내용 

진짜 클로저 함수를 적어보자.

```javascript
function f6() {
    const a = 10
    function f7(b) {
        return a+b
    }
    return f7
}
const f8 = f6()
f8(20) // 30
f8(5) // 15
```

*진짜 클로저다.* 이제 `a`는 사라지지 않는다. `f7` 이 a를 사용하기에 a를 **기억해야 하고** f7이 f8에 담겼으므로 클로저가 되었다. 

> scope에 대한 정의는 this 정리문서에서 했으므로 넘어가자. 



### 1.4.3 클로저 실용 사례

클로저의 강력함은 은닉에 있지 않다. 

* 이전 상황을 나중에 일어날 상황과 이어 나갈 때
* 함수로 함수를 만들거나 부분 적용을 할 때

`이전 상황을 나중에 일어날 상황과 이어 나갈 때` 란, 이벤트 리스너로 함수를 넘기기 전에 알 수 있던 상황들을 변수에 담아 클로저로 만들어 기억해 두면, 이벤트가 발생되어 클로저가 실행되었을 때 기억해 두었던 변수들로 이전 상황들을 이어나갈 수 있다.

> 아, 코드보면 이해되겠지
>
> 라고 생각했지만 썩?  page 36 질문

### 1.4.4 클로저를 많이 사용하라.

책에는 어려운 듯 하지만 어렵지 않으며 간단하다고 적혀있다. ~~아닌데~~

클로저 패턴을 많이 작성해봐야 좋을 것 같다. 

### 1.4.5 고차 함수 

1. 함수를 인자로 받아 대신 실행하는 함수
2. 함수를 리턴하는 함수
3. 함수를 인자로 받아서 또 다른 함수를 리턴하는 함수

`_.map `, `_.filter` , `_.reduce` 등이 유명한 고차함수 이다. 

함수를 인자로 받아 대신 실행하는 함수를 만들어 보자. 

```javascript
function callwith10(val, func) {
    return func(10, val)
}
function add(a,b) {
    return a+b
}
function sub(a,b) {
    return a-b
}
callwith10(10,add) //25
callwith10(10,sub) //0
```

**callwith10** 은 고차함수다. 



아래에 나올 함수는 ~~뭐 이런걸 다....;;;~~ 많이 사용되는 함수라고 한다. 

```javascript
function constant(val) {
    return function() {
        return val
    }
}
var alwa = constant(10)
```

> ? ? ? ? ? ? ? ? 그냥 const 썼으면 좋겠다. 질문



아래에 나올 것은 이름부터 복잡해서 이름을 붙이자. 

* f1 : 함수를 대신 실행하는 함수
* f2 : f1을 리턴하는 함수

```javascript
function f2(val1) {
    return function(val2,func) { //f1
        return func(val1,val2)
    }
}
let re1 = f2(10)
re1(20,add) //30
```

`f1` 은 `val1`을 기억하는 함수를 리턴했다. 

`f2(10)(20,add) ` 로도 쓸 수 있다. 



그래서, 숫자가 아닌 값도 활용 가능하다고 한다. 

```javascript
f2( [1,2,3] )( v=>return v*10, _.map )
//func == _.map
//val1 = func의 첫 번째 인자[1,2,3]
//val2 = func의 두 번째 인자 
// _.map([1,2,3],v=>v*10) 형태
```

다시 다시 조금 더 실용적인거!

```javascript
_.get = function(list, idx) {
    return list[idx]
}
let callWithUsers = f2([
    {id :2, name: "soyoung", age:23},
    {id :3, name: "yapp", age:0 },
]) //배열을 기억하는 f1리턴.
callWithUsers(2, _.get) //_.get(배열,2)
```

Awesome!



### 1.4.6 콜백함수라 잘못 불리는 보조 함수

콜백 함수를 받아 자신이 해야할 일을 모두 끝낸 후 결과를 되돌려 주는 함수도 고차 함수다. 

보통은 비동기가 일어나는 상황에서 사용되며 콜백 함수를 통해 다시 원래 위치로 돌아오기 위해 사용되는 패턴이다. 

context를 **다시 돌려주는 역할** 을 수행 하는 것이 **callback** 이다. 

**인자로 사용된 모든 함수를, 익명 함수가 넘겨지고 있는 모든 형태를 콜백이라고 칭하면 안된다.**



`button.click(function(){})` 과 같은 코드 표현은 `이벤트 리스너` 라고 칭하는 것이 맞다. 

`callback`은 종료가 되었을 때 단 한 번 실행되지만 앞선 예제의 고차 함수들은 종료 될때 실행되지 않으며 상황에 따라 여러 번 실행되기도 하고 각각 다른 역할을 한다. 

> 자 그럼 콜백도 정리해보자......!

보조 함수의 이름을 잘 짓고, 유용한 고차 함수를 많이 만들어야 한다. 



### 1.4.7 함수를 리턴하는 함수와 부분 적용

앞선 예제에서 미리 필요한 인자를 넘겨 두고 그 인자를기억하는 클로저를 리턴하는 함수들을 확인했다.

`addMaker` , `bvalue` , `bmatch` 와 같은 함수들은 약속된 개수의 인자를 받은 후 클로저로 만들어진 함수가 추가적으로 인자를 받아 로직을 완성하는 패턴을 갖는다. 

이런 기법들은 다음과 같은 공통점을 가진다.



**기억하는 인자 혹은 변수가 있는 클로저** 를 리턴한다. 



bind 를 보자.

```javascript
function add(a,b) {
    return a+b
}
let add10 = add.bind(null,10)
add10(20) //30
```

> 코드만 쳤을 때는 역시나 어렵다.

`bind` 는 첫 번째 인자로 `bind` 가 리턴할 함수에서 사용될 `this` 를 받는다. 

> 여기선 null

두 번째 인자부터 함수에 미리 적용될 인자들이다. 

`bind` 의 this 는 `null` 이고 한번 정해지면 바꿀 수 없다. 그래서, 다들 Lodash의 `_.curry` 가 등장했다.(?)

그러나, 커링은 인자의 수나 형이 명확하게 정해지지 않은 함수와는 잘 맞지 않다. 



`bind` 는 원하는 지점을 비워두고(인자를 비워두고) 사용할 수는 없다. 

이러한 점을 개선한 방식이 **partial** 이다. 



```javascript
function.prototype.partial = function() {
    var fn = this,                       							//1
        args = Array.prototype.slice.call(argumetns)				//1
    return function(){												//2
        var arg = 0
        for(var i=0; i<args.length && arg < arguments.length;i++) 	//5
            if(args[i]=== undefined) args[i] = arguments[arg++]		//6
        return fn.apply(this,args)
    }
}
function abc(a,b,c) {
    console.log(a,b,c)
}

var ac = abc.partial(undefined, 'b', undefined)						//3
ac('a','c')//a b c													//4
```

> HardCode

1. 우선 partial이 실행되면 fn에 자기 자신인 `this` 를 담는다. args에는 `partial` 이 실행될 때 넘어온 인자들을 배열로 변경하여 args에 담는다. 

2.  `fn`과 `args`는 리턴된 익명 함수가 기억하게 되므로 지워지지 않는다.
3. `abc.partial` 을 실행할 때 첫 번재 인자와 세 번째 인자로 넘긴 `undefined` 자리는 나중에 `ac`가 실행될 때 채워질 것이다. 
4. `ac` 를 실행하면서 넘긴 `a` 와 `c` 는
5. 리턴된 익명함수의 `arguments` 에 담겨 있다. 
6. `for` 를 돌면서 미리 받아 두었던 `args`에 `undefined` 가 들어 있던 자리를 `arguments` 에서 앞에서 부터 꺼내면서 채운다. 



다 채우고 나면 미리 받아 두었던 `fn` 을 `apply` 로 실행하면서 인자들을 배열로 넘긴다. 



좋아보이는 코드이지만, **인자의 개수가 자유롭지 못하다** 는 점과, 클로저로 생성 된 `args` 의 상태를 직접 조작하는 형태이기 때문에 **재사용이 불가능** 하다. 

재사용이 불가능한 점은 아래와 같이 고치면 된다. 

```javascript
function.prototype.partial = function(){
    var fn = this, _args = arguments 								//1
    return function() {
        var args = Array.prototype.slice.call(_args)				//2
        var arg = 0
        for(var i=0; i<args.length && arg < arguments.length;i++) 	//3
            if(args[i]=== undefined) args[i] = arguments[arg++]	
        return fn.apply(this,args)
    }
}
```



1. 클로저가 기억할 변수에는 원본을 남겼다.
2. 리턴된 함수가 실행될 때 마다 복사하여 원본을 지켰다.
3. 실행 될때마다 새로 들어온 인자를 채웠다. 

 

이제 좋은 오픈소스인 `Underscore.js` 나 `Lodash` 의 `partial` 동작을 살펴보자.

> 스타 많이 받은데는 이유가 있다. 

```javascript
var ac = _.partial(abc,_,'b')
ac('a','c') //a b c
var add2 = _.partial(add,_,2)
add2(1,3,4,5) //15
```

적용해 둘 인자와 비워둘 이자를 구분하는 구분자로 `undefined` 대신 `_` 를 사용한다. 

