---
layout: single
classes: wide
categories: ["JavaScript"]
title: "JavaScript의 함수 호출과 this 이해하기, `call`, `apply`, `bind`"
tags: ["JavaScript"]
---

JavaScript에서 함수를 호출할 때 this의 경계는 생각보다 많이 모호합니다.

이를 제대로 이해하기 위해서 JavaScript 내부 함수 호출의 근원을 이해할 필요가 있습니다.

## 핵심 원시 (The Core Primitive)

먼저 핵심 함수 호출인 `function`의 `call`메소드 대해 알아야 합니다. `call(thisValue, argList)`메소드는 직설적으로 함수를 호출합니다.

1. 인수 리스트(`argList`)를 1 부터 끝까지 만듭니다..
2. 첫 파라미터는 `thisValue`로 지정하고.
3. 함수의 `this`를 `thisValue`로 설정하고 인수리스트는 `argList`로 하여 함수를 호출합니다.

```javascript
function hello(thing) {
  console.log(this + " says hello " + thing);
}

// this: "Yehuda", argList: "world"
hello.call("Yehuda", "world") //=> Yehuda says hello world
```

`hello`메소드의 this가 `"Yehuda"`로 지정되고 단일 인수(arguments)로 `"world"`가 지정된 것을 알 수 있습니다. 이것이 JavaScript의 함수 호출의 핵심 근원(*사실 거짓입니다 자세한 설명은 아래에..*)입니다. 모든 다른 함수의 호출들은 desugaring(desugar: 편리한 구문이 아닌것, 좀 더 기본적이고 내부 핵심적인 표현)하면 원시형과 같은 표현으로 생각할 수 있다.

## 간단한 함수 호출

함수를 호출할 때마다 `call`을 사용하는 것은 꽤나 성가십니다. 그래서 자바스크립트는 직접적인 함수의 괄호형 구문(`hello("world")`)의 사용을 허용합니다.

```javascript
function hello(thing) {
    console.log("Hello " + thing);
}

// this: [object Window]
hello("world");

// desugars to:
hello.call(window, "world");
```

ECMAScript 5의 strict mode를 사용하면 다음과 같습니다.

```javascript
// this: undefined
hello("world");

// desugars to:
hello.call(undefined, "world");
```

간단한 함수 호출 `fn(...args)`는 `fn.call(window [ES5-strict: undefined], ..args)`와 같습니다.

추가적으로 인라인 함수의 정의 `(function(){})()`와 `(function(){}).call(window \[ES5-strict:undefined\])`은 같습니다.

## 멤버함수 Member Functions

오브젝트의 멤버로서 어떻게 호출되는지 살펴보겠습니다.

```javascript
let person = {
    name: "Brendan Eich",
    hello: function(thing) {
        console.log(this + " says hello " + thing);
    }
}

// this:
person.hello("world"); // [object Object] says hello world

// desugars to this:
person.hello.call(person, "world"); // [object Object] says hello world
```

`hello`메소드의 오브젝트에 대한 소속은 중요한 것이 아닙니다. 이전에는 `hello`메소드를 standalone 함수로 정의하였습니다. 이제는 이 메소드를 동적으로 오브젝트에 소속시켜 어떻게 되는지 살펴보겠습니다.

```javascript
function hello(thing) {
    console.log(this + " says hello " + thing);
}

let person = { name: "Brendan Eich" }

person.hello = hello;

person.hello("world"); // [object Object] says hello world

hello("world");  // [object Window] says hello world
```

함수는 'this'에 대한 영속적인 개념이 없는 것을 알 수 있습니다. 호출자의 호출하는 방식에 기반하며 언제나 호출 시간에 설정됩니다.

## Function.prototype.bind 사용하기

영속적인 `this`값이 주는 편리함 때문에, 폐쇄적인 기법을 사용하여 영속적인 `this`를 갖는 함수를 만들기도 합니다.

```javascript
let person = {
    name: "Brendan Eich",
    hello: function(thing) {
        console.log(this.name + " says hello " + thing);
    }
};

let boundHello = function(thing) {
    console.log(this); // this: Window
    return person.hello.call(person, thing); 
}

boundHello("world"); // Brendan Eich says hello world
```

이럼에도 불구하고 `boundHello`의 call은 여전히 desugar 하면 `boundHello.call(window, "world")`입니다. 이제는 원시 `call`함수의 `this`값을 원하는 것(person)으로 바꿔보겠습니다.

```javascript
let person = {
    name: "Brendan Eich",
    hello: function(thing) {
        console.log(this.name + " says hello " + thing);
    }
};

let bind = function(func, thisValue) {
    return function() {
        return func.apply(thisValue, arguments);
    };
}

let boundHello = bind(person.hello, person);
boundHello("world");
```

1. `arguments`는 배열과 비슷한 오브젝트로 함수에 들어가는 모든 인수들을 표현합니다.
2. `apply` 메소드는 배열과 비슷한 Object(arguments)를 받는것을 제외하고는 `call` 원시 메소드랑 비슷하게 동작합니다. `apply(thisValue, [arg1, arg2, arg3 ...])`, `call(thisValue, arg1, arg2, arg3)`

여기서 사용된 `bind` 메소드는 단순히 새로운 **함수를 반환**합니다. 이것이 실행되면, 새로운 함수는 원래의 값을 `this`로 설정하고 단순히 전달 된 원래 함수를 실행시킵니다. 또한 `arguments`를 통해 전달됩니다.

이것은 흔한 관용구이다. ES5는 새롭게 정의된 메소드 bind를 사용합니다. 모든 `Function`오브젝트에 대해 this 행동을 implements 합니다.

```javascript
let boundHello = person.hello.bind(person);
boundHello("world");
```

## PS 주의할 거짓말인 부분

`func.call`이 원시라는 부분은 거짓입니다. 사실은 명세에서는 원시형(내부적으로 참조하는 `[[Call]]`)은 `func.call`과 `\[obj.\]func()`을 사용합니다.

`func.call`의 정의를 살펴보면

1. `IsCallable(func)`이 거짓이면, `TypeError` 예외를 던집니다.
2. argList는 빈 리스트가 될 수 있습니다.
3. this 메소드가 둘 이상의 인수로 호출되면, 첫 arg 부터 왼쪽에서 오른쪽 순서로 각 인수를 argList의 마지막 요소로 추가합니다.
4. this value를 `thiaArgs`로 제공해주고 arguments의 리스트를 `argList`로 제공해주고, func의 `[[Call]]` 내부 메소드를 호출함으로써 결과를 반환합니다.

`func.call`의 정의를 통헤 매우 간단한 원시`\[\[Call\]\]`동작에 대한 JavaScript 언어 바인딩임을 알 수 있다.

`argList`와 `thisValue`가 결정된다면 본질적으로 동일한 표현입니다.

## 정리

1. `call`메소드는 `call(thisValue, arg1, arg2, arg3)`로 **함수를 호출**합니다.
2. `apply`메소드는 `apply(thisValue, [arg1, arg2, arg3])`로 **함수를 호출**합니다.
3. `bind`메소드는 `bind(thisValue, arg1, arg2, arg3)`로 **함수를 반환**하며, arg에 들어간 값은 호출 시 자동으로 할당됩니다.

여기서 `call`과 `apply`는 단순히 인자의 차이만 있는 것이 아닙니다.

### .call과 .apply

- 둘 모두 Function.prototype이 소유한 메소드입니다.
- 함수와 메서드가 실행될 때 바인딩할 객체를 지정하여 함수가 실행될때 context의 유효범위를 직접 지정하여 this 할당할 수 있습니다.

- 호출의 동적인 변화에 따라 차이가 드러납니다. 정적인 호출에는 `call`을 동적인 호출에는 `apply`를 사용합니다.
- 즉, 호출시 동적인 인자전달이 필요한 경우에는 `apply`를 정적으로 고정된 함수를 호출하는 경우에는 `call`을 사용합니다.
- *동적인 인자전달*은 인자의 갯수를 유동적으로 받는 것을 의미합니다. JavaScript에서는 배열과 비슷한 `arguments`를 사용하여 인자를 일일이 지정하지 않고 동적으로 인자전달을 받을 수 있습니다.

- bind() 메소드나 동적 callback을 구현할 때 apply가 사용되는 이유이기도 합니다.

## 출처 및 참고

[Understanding JavaScript Function Invocation and "this"](https://yehudakatz.com/2011/08/11/understanding-javascript-function-invocation-and-this/)

[(JavaScript) 함수의 메소드와 arguments - call, apply, bind - ZeroCho Blog](https://www.zerocho.com/category/JavaScript/post/57433645a48729787807c3fd)

[front-end 개발자 인터뷰 문제 - javascript 영역 |  Insanehong's Incorrect Note](http://insanehong.kr/post/front-end-developer-interview-javascript/)
