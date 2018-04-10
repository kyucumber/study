## 자바스크립트의 함수형 프로그래밍과 Lodash

함수형 프로그래밍은 부수효과(side-effect)와 어플리케이션의 상태 변화를 줄이기 위해서 함수를 이용해 제어 흐름과 데이터에 대한 조작을 추상화하는 프로그래밍, **순수함수**를 조합하고 참조 투명성을 가지며 부수효과를 피하려고 하는 프로그래밍 방법.

#### 순수함수

주어진 입력에 의해서만 계산되어야 하고, 변경이 가능할 수 있는 상태를 사용하면 안됩니다.

순수함수는 자신 함수 스코프 외 어떠한 영향을 미쳐서도 안되고 전역 객체나 매개변수의 레퍼런스를 변경해도 안 되는 side effect가 없는 순수한 함수를 말합니다.

```javascript
function add(a, b) {
    return a + b;
}

function squrare(num) {
    return Math.pow(num, 2);
}
```

#### 참조 투명성(Referential transparency)

함수가 같은 입력에 대해서 일관된 같은 결과를 반환하면 참조 투명성을 보장하는 것.

```javascript
var counter = 0;

function increment(x) {
    counter++;
    return x + counter;
}
increment(1);
//전역 값이 바뀜에 따라 2가 아니라 다른 값이 나올수도 있어서 참조 투명성을 보장하지 않는다.

function increment(x) {
    return x + 1;
}
increment(1);
//항상 1을 파라미터로 주면 2가 나올 것을 보장하므로 참조 투명성이 보장된다.
```

#### 부수효과

결과값을 반환하는 것 이외의 행위..예를 들어 전역 객체를 수정하거나 전달 받은 인자를 수정하거나 예외를 던지거나 출력을 하거나..등등

```javascript
var counter = 0;

function increment() {
    return ++counter;
}

increment();
```

위에서는 increment 함수 내에서 전역의 counter 값을 증가시키는 부수효과를 발생시키고 있습니다.

- - -

#### 선언형 프로그래밍

선언형 프로그래밍은 함수형, 논리형, 제한형 프로그래밍을 모두 포함한 개념이며 선언형 프로그래밍은 순서나 문제해결 과정(제어 흐름, 상태 변화) 보다는 어떠한 모습이어야 할지에 대한 표현에 중점을 둡니다.(선언적 프로그래밍은 명령형 프로그래밍과 반대로 프로그램이 어떤 일을 하는지에 대한 설명과 실제 구현을 분리합니다). 뭔소리야..

```javascript
// 명령형 프로그래밍
var array = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];
for (let i = 0; i < array.length; i++) {
  array[i] = Math.pow(array[i], 2);
}
//결과 [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

// 선언적 프로그래밍
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9].map(num => Math.pow(num, 2));
//결과 [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

```

### 자바스크립트의 함수형 프로그래밍

자바스크립트의 함수형 프로그래밍은 제공되는 고차함수들을 이용해 할 수 있습니다.

#### 고차함수

함수를 매개변수로 전달 받거나 함수를 반환하는 함수.

```javascript
function forEach(array, action) {
    for(var i =0; i<array.length; i++) action(array[i]);
}

forEach(["hello", "bye", "hi"], console.log);
```

고차함수는 아래와 같은 경우에 자주 쓰입니다.

- Callback => `XMLHttpRequest.onreadystatechange = callback;`
- Iteratee => `.map(collection, iteratee)`
- Predicate => `.filter(collection, predicate)`
- Event Listener => `target.addEventListener(type, listener)`

#### 자바스크립트의 고차함수

가장 자주 쓰이는 고차함수 몇개를 뽑아봤습니다.

##### map

```javascript
[0, 1, 2, 3, 4, 5].map(num => Math.pow(num, 2));
//[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```

위의 map 고차함수는 배열의 각 요소를 제곱한 결과를 담은 배열을 반환합니다,

##### filter

``` Javascript
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9].filter(num => num % 2);
//[1, 3, 5, 7, 9]
```

위의 filter는 각 배열의 요소 중 `num % 2`를 만족하는 요소를 제거하고 반환합니다.

##### reduce

```javascript
[1, 2, 3].reduce((acc, cur) => acc + cur);
// 1, 2, 3을 모두 더한 값 반환. 6

const personInfo = [{name: 'Joe'}, {age: 31}, {birthday: '1/1/2018'}];
const fullPerson = (personInfo).reduce((acc, curr) => {
	return Object.assign({}, acc, curr)
});
//{name: "Joe", age: 31, birthday: "1/1/2018"}
```

reduce는 간단히 말하면, 요소를 합쳐서 반환합니다. 위의 배열의 reduce에 acc + cur은 앞 뒤 요소를 다 더하고, 배열의 루프를 돌아서 모든 배열의 합인 6을 반환하고 아래는 3개의 객체를 가진 personInfo를 합쳐서 하나의 객체로 만들어 줍니다.

#### Lodash

자바스크립트 유틸리티 라이브러리

유틸리티 라이브러리로 array, collection, date, number, object 등이 있으며, 데이터를 쉽게 다룰 수 있도록 도와줍니다.
(예를들면, 배열 안에 중복 값을 제거하기 / object 배열 안에 특정 값만 추출하기 등..)

설치해도 되지만, 간단하게 cdn을 넣고 Lodash를 사용해보자.

```javascript
<script src="https://cdn.jsdelivr.net/npm/lodash@4.17.5/lodash.min.js"></script>
```

Lodash에서 제공하는 고차함수들중 일부.

##### _.fill()

특정한 값으로 배열을 채우는 경우에 사용합니다.

```javascript
var array = [1, 2, 3];
_.fill(array, true);
console.log(array);
//[true, true, true];
_.fill(new Array(3), true);
//[true, true, true];
```

##### _.filter()

특정 값을 걸러내는 용도, 이름 그대로 filter의 역할을 합니다. 특정 조건을 만족하는 요소를 배열에서 반환합니다.

```javascript
var people = [
  { 
    name: 'kyunam',
    age: 25
  },
  {
    name: 'hi',
    age: 29
  }
];

var people = _.filter(people, a => a.age === 29);
console.log(people);
//{ name : 'hi', age: 29}
```

##### _.map()

배열 안의 객체의 특정 값을 배열로 뽑아내고 싶을 때 사용합니다.

```javascript
var people = [
  { 
    name: 'kyunam',
    age: 25
  },
  {
    name: 'hi',
    age: 29
  }
];

var names = _.map(people, 'name');
console.log(names);
//['kyunam', 'hi']
```

##### _.uniq()

배열의 중복 요소를 제거합니다.

```javascript
var arr = [1, 2, 3, 4, 4, 5, 6, 6, 9];

var removeDuplicatedArr = _.uniq(arr);
console.log(removeDuplicatedArr);
```

#### 메소드 체이닝

메소드 체이닝은 자신을 소유한 객체가 순서대로 호출 할 메소드들로 구성되며, Lodash를 예로 들면 LodashWrapper 객체를 매개로 함수를 서로 체이닝하면서 결과를 도출합니다.

```javascript
//예제 1
var arr = [1, 2, 3, 4, 4, 5, 6, 6, 9];

var removeDuplicatedArr = 
    _.chain(arr)
    .uniq(arr)
    .filter(a => a%2===0).value()

console.log(removeDuplicatedArr);
//[2, 4, 6]

//예제2
const isValid = val => !_.isUndefined (val) && !_.isNull(val);
const names = ['angular item' , 'angular note' , 'web_component note' ,
'react t shirt' , 'web_component note' ];

_.chain(names)
.filter(isValid)
.map(s => s.replace(/_/, ' '))
.uniq()
.map(_.startCase)
.sort()
.value()

//["Angular Item", "Angular Note", "React T Shirt", "Web
Component Note"]
```



#### 파이프라인

파이프라인이란 한 함수의 출력이 다음 함수의 입력이 되게끔 만든 방향성 함수 순차열입니다.

파이프라인이 가능하려면 연결할 함수와 앞의 함수의 리턴타입의 인자가 일치해야하고, 인자의 갯수 또한 같아야합니다.

```javascript
const add = (a, b) => a + b;
const multi = (a) => a * 5;
const pipeline = (f, g) => (...args) => g(f(...args));
const sumAndMulti = pipeline(add, multi);
const result = sumAndMulti(3, 4);

console.log(result);
//3 + 4의 결과에 5를 곱한다.

//Lodash 파이프라이닝
const fp = _.noConflict();
const result2 = fp.flow(add, multi)(2, 1);
console.log(result2);
//2 + 1 곱하기 5
```

#### 커링(Currying)

커링은 다 변수 함수가 인수를 전부 받을때까지 실행을 보류, 지연시켜서 단계별로 나뉜 단항 함수의 순차열로 전환하는 기법입니다. 예제에서 Lodash의 _.curry()를 이용하겠습니다.

##### 커링 예제 코드

```javascript
const addCurriedFunc = a => b => c => a + b + c;
console.log(addCurriedFunc(1)(2)(3));

//lodash의 _.curry()를 하면 curried된 함수가 리턴됨.
const addCurriedFunc2 = (a, b, c) => a + b + c;
const lodashCurriedFunc2 = _.curry(add3);
console.log(lodashCurriedFunc2(1)(2)(3));

const addVAT = (rate, amount) => amount * (1 + rate / 100);
console.log(addVAT(20, 1000));
console.log(addVAT(20, 1500))
const addVATcurried = _.curry(addVAT);
const addNationalVAT = addVATcurried (5);
const addStateVAT = addVATcurried (2);
console.log(addNationalVAT (1000));
console.log(addStateVAT (1500));
```

#### 부분 적용(Partial Application)

함수의 일부 매개변수 값을 고정시켜서 함수가 더 작은 함수를 생성하는 방법. 커링과는 다르게 후속 호출 시 이미 평가를 마친 상태입니다.

```javascript
const add = (a, b, c, d) => a + b + c + d;
const addPartial = (a, c) => add(a, 0, c, 0);

console.log(addPartial(10, 50));
//60
```

위처럼 4개의 변수를 받는 함수가 있을 때 2개를 받는 함수를 생성해 2개의 파라미터를 고정시켜서 작은 함수를 생성해 사용합니다.