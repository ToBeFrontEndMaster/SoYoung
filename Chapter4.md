# Chapter4

함수 조립하기.

유인동님이 생각하시는 함수형 자바스크립트의 10가지 기법.

1. 함수를 작게

2. 다형성 높은 함수

   > 다형성 높은 함수란 ? 

3. 상태를 변경하지 않거나 정확히 다루어 부수 효과 최소화

4. 동일한 인자 받으면 항상 동일한 결과 리턴하는 **순수 함수** 만들기

5. 복잡한 객체 하나를 인자로 ㄴㄴ 일반적인 값 여러개를 인자로

6. 큰 로직을 고차함수로, 세부 로직을 보조 함수로

7. 일반 함수이자 순수 함수

8. 기본 객체 이용

9. 로직의 흐름을 단방향으로

10. 작은 함수 모아 큰 함수

## 1 고차함수와 보조함수

`map` ,`filter` ,`reduce` 는 모두 배열이나 객체, 혹은 배열에 들어 있는 객체를 다루기 위한 것. 

함수와 협업하는 함수들을 만나보자.

### 1-1 한 번만 실행

```js
var hi = _.once(()=>{
    console.log('hi')
})
hi() //hi
hi() //놀랍게도 아무일도..
```

> 그래서 정확히 언제 필요하죠 ?

### 1-2 다시 물어 보지 않는 함수

```js
function skip(body) {
    var yes;
    return function() {
        return yes || (yes = body.apply(null,arguments))
    }
}
```

`skip` 함수는 **다시 물어 보지 않는 함수** 이다. 삭제할 때 확인창을 한 번만 띄우기 위해 `skip` 을 사용한다. 

이렇게 하면, 처음 삭제를 할때만 확인을 하고 다음 번 삭제부터는 확인 창이 뜨지 않고 바로 삭제가 된다. 

### 1-3 앞서 받은 인자 혹은 상황을 변경해 나가는 경우 

`skip` 은 처음에는 false 였지만 true로 변경하여 이후 동작을 다르게 만들기 위해 사용한다. 이처럼 초기에 값을 받아 둔 후 병경해 나가는 사례를 확인하자.

```js
function idMaker(start) {
    return function(){
        return ++start
    }
}
var messageId = idMaker(0)
messageId() //1
messageId() //2

var postCid = idMaker(11)
postCid() //12
postCid() //13
```

원하는 시점부터 시작해 실행할 때마다 증가한 고유 아이디 값을만드는 함수를 만드는 함수. 

> ...

### 1-4 앞서 받은 인자를 잘 유지해야 하는 경우

클로저가 기억하고 있는 외부 변수도 일반 변수처럼 언제든지 값이 변경될 수 있다. 

이번에는 **값을 잘 유지하는** 상황을 살펴 보자. 

> 살펴보려 했지만

## 2.부분적용

졸리다.

### 2-1. _.partial로 함수 만들기

기본적인 `_.partial` 함수 사용법을 다시 익혀보자. 

```js
var pc = _.partial(console.log,1)
pc(2) 
//결과 : 1 2
//2가 오른쪽으로 들어감.
pc(2,3)
//결과 : 1 2 3
//2,3이 오른쪽으로 들어감.

var pc = _.partial(console.log,_,2)
pc(1)
//결과 : 1 2
//1이 _로 들어감.
pc(1,3)
//결과 : 1 2 3
//1은 _로 들어가고, 3은 오른쪽으로 들어감. 

var pc = _.partial(console.log,_,_,3)
pc(1)
//결과 : 1 undefined 3
//1은 _로 들어가고 두 번째 _는 들어오지 않아서 undefined
pc(1,2)
//결과 : 1 2 3
pc(1,2,4)
//결과 : 1 2 3 4
```

이제 좀 더 다른 예제.

```js
var add_all = _.partial(_.reduce,_,(a,b)=> a+b)
add_all([1,2,3,4])
```

### 2-2 partial 과 compose로 함수 만들기 

`_.partial` 은 함수를 연속으로 실행해 주는 `_.compose` 등의 함수와 함께 더 재미있게 사용할 수 있다. `_.compose` 는 오른쪽의 함수를 실행한 결과를 왼쪽의 함수에게 전달하는 것을 반복하는 고차 함수이다. 

```js
//compose 사용법
_.compose(console.log, a=>a-2, a=>a+5)(0)
//console.log <- 5 - 2 <- 0+5 <- 0
//3 
var falsy_values = _.compose(
	_.partial(_.isEqual, -1),              //1 
    _.partial(_.findIndex,_,_.identity)    //2
)
console.log( falsy_values([1,true,{}]) )
```

1. `_.isEqual` 함수에 -1 을 부분 적용하여, 앞서 나온 결과(2에서 받아올 결과)가 -1과 같은지 검사하는 함수를 만들었다.
2. -1 과 비교하는 함수가 실행되기 전에는, `_.findIndex` 에 `_.identity` 를 부분 적용해 둔 함수가 실행 된다. 
   결국, **falsy_values** 는 배열에 들어있는 값이 모두 부정인 값인지 검사하는 함수이다. 



### 2-3 더 나은 _.partial 함수 

`_.partial` 은 인자를 왼쪽에서부터 하나씩 적용하면서 _로 구분하여 인자가 적용될 위치를 지정해 둘 수 있도록 한다. 자바스크립트의 함수는 인자 개수가 유동적일수 있고.. 합이 잘 안맞는대.

> 타입스크립트 ! 쓰자!
>
> 이 이후의 내용을 보는게 의미가 있나 싶어서 Loadash를 찾아보기로 함. 

## 3. 연속적인 함수 실행

### 3-1 체인의 아쉬운 점

체인은 method를 연속적으로 실행하면서 객체의 상태를 변경해 나가는 기법이다. 체인은 표현혈깅 좋고 실행 순서를 눈으로 따라가기에도 좋다. 

뭐야 그래서 체인이..뭐지 ;

### 3-3 파이프라인

파이프라인은 `_.compose` 의 장점을 그대로 가지고 있다. 

다만, 함수 실행 방향은 왼쪽에서부터 오른쪽이다. 체인과 달리 아무 함수나 사용할 수 있어서 자유도가 높다. 
다음은 `_.pipeline` 

```js
_.pipeline = function() {
    var funs = arguemtns                      //1
    
    return function(seed) {                   //2
        return _.reduce(funs, (l,r)=>r(l),seed)
    }
}

var divSquare = _.pipeline( a=>a/2 , a=>a*a )
```

`_.pipeline` 은 `_.reduce` 를 이용해서 만들어졌다. `_.reduce` 는 가지고 있는 데이터들로 다른 데이터를 만들고자 할때 사용한다. 

위 상황에서 가지고 있는 데이터는 함수들이고 만들고자 하는 데이터는 최초 인자로부터 시작해 모든 함수를 통과한 마지막 결과이다. 

1. `arguments` 를 지역변수 `funs` 에 담았다.
2. `funs` 를 기억하는 함수를 리턴한다.
3. 리턴된 함수가 나중에 실행되면 받은 인자인 seed를 _.reduce 의 마지막 인자로 넘겨주어 seed는 최초의 l이 된다. 
4. l은 left이고, r은 right 인듯 하다. 오른쪽 함수 r을 실행하며 왼쪽 함수의 결과 l을 넘겨주고 있다. funs의 개수만큼 반복되며 마지막 함수의 결과가 곧 _.pipeline 으로 만든 함수의 결과가 된다. 

> Q. 작동이 잘 이해가 안되는 부분. 

### 3-5 multiple results 

함수의 결과값을 여러개로 리턴하는 개념이다. 자바스크립트에는 이런 기능이 없다. 하지만 이것을 대체하는 기능을 구현할 수 있다. 여러 개의 값을 모아 multiple results 를 뜻하는 객체로 만든 후 파이프라인 안에서 mutiple results에 담긴 인자를 다시 여러개로 펼쳐서 실행하도록 구현한다. 

```js
_.mr = function() {
    arguments._mr = true
    return arguments
}

_.pipeline = function(){
    var funs = arguments
    
    return function(seed) {
    	return _.reduce(funs, (l,r)=> l && l._mr ? r.apply(null,l) : r(l) )
    },
        arguments.length <2 ? seed : _.mr.apply(null,arguments)
}
```















