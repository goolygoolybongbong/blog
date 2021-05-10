---
layout: single
classes: wide
categories: ["react", "JavaScript"]
title: "JavaScript의 함수 호출과 this 이해하기"
tags: ["React", "JavaScript"]
---

JavaScript의 함수 호출에는 혼동할 수 있는 점이 있다. this의 경계에 대한 것이다.

이를 제대로 이해하기 위해서 내부 함수 호출의 근원을 이해할 필요가 있다.

## 핵심 원시 (The Core Primitive)

먼저 핵심 함수 호출인 `function`의 `call`메소드 대해 알아야 한다. `call`메소드는 상대적으로 직설적이다.

1. 인수 리스트(`argList`)를 1 부터 끝까지 만든다.
2. 첫 파라미터는 `thisValue`이다.
3. `this`를 `thisValue`로 설정하고 인수리스트는 `argList`하여 함수를 호출한다

```javascript
function hello(thing) {
  console.log(this + " says hello " + thing);
}

// this: "Yehuda", argList: "world"
hello.call("Yehuda", "world") //=> Yehuda says hello world
```

`hello`메소드의 this가 `"Yehuda"`로 지정되고 단일 인수로 `"world"`가 지정된 것을 알 수 있다. 이것이 JavaScript의 함수 호출의 핵심 근원이다. 모든 다른 함수의 호출들이 원시형에 대한 desugaring(desugar: 편리한 구문이 아닌것, 좀 더 기본적이고 내부 핵심적인 표현)으로 생각할 수 있다.

## 간단한 함수 호출

함수를 호출할 때마다 `call`을 사용하는 것은 꽤나 성가시다. 그래서 자바스크립트는 직접적인 함수의 괄호형 구문(`hello("world")`)의 사용을 허용한다.

```javascript
function hello(thing) {
    console.log("Hello " + thing);
}

// this:
hello("world");

// desugars to:
hello.call(window, "world");
```

ECMAScript 5의 strict mode를 사용하면 다음과 같다.

```javascript
// this:
hello("world");

// desugars to:
hello.call(undefined, "world");
```

짧은 버전의 함수 호출 `fn(...args)`는 `fn.call(window [ES5-strict: undefined], ..args)`와 같다.

참고로 인라인 함수의 정의 `(function(){})()`와 `(function(){}).call(window \[ES5-strict:undefined\])`와 같다.

## 멤버함수 Member Functions

오브젝트의 멤버로서 어떻게 호출되는지 살펴보겠다.

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

`hello`메소드의 오브젝트에 대한 소속은 중요한 것이 아니다. 이전에는 `hello`메소드를 standalone 함수로 정의하였다. 이제는 이 메소드를 동적으로 오브젝트에 소속시켜 어떻게 되는지 살펴보겠다.

```javascript
function hello(thing) {
    console.log(this + " says hello " + thing);
}

let person = { name: "Brendan Eich" }

person.hello = hello;

person.hello("world"); // [object Object] says hello world

hello("world");  // [object Window] says hello world
```

함수는 'this'에 대한 영속적인 개념이 없는 것을 알 수 있다. 호출자의 호출하는 방식에 기반하며 언제나 호출 시간에 설정됩니다.

## Function.prototype.bind 사용하기

때때로 영속적인 `this`값이 주는 편리함 때문에, 폐쇄 기법을 사용하여 영속적인 `this`를 갖는 함수를 만들기도 합니다.

```javascript
let person = {
    name: "Brendan Eich",
    hello: function(thing) {
        console.log(this.name + " says hello " + thing);
    }
};

let boundHello = function(thing) { return person.hello.call(person, thing); }

boundHello("world"); // Brendan Eich says hello world
```

이럼에도 불구하고 `boundHello`의 call은 여전히 desugar 하면 `boundHello.call(window, "world")`이다. 되돌려서 원시 `call`함수의 `this`값을 우리가 원하는 것으로 바꿔보겠다.

몇 가지 범용 수정을 통해 만들어 낼 수 있다.

```javascript
let bind = function(func, thisValue) {
    return function() {
        return func.apply(thisValue, arguments);
    };
}

let boundHello = bind(person.hello, person);
boundHello("world");
```

이것을 이해하기 위해 두 가지를 더 이해해야 한다.

1. `arguments`는 배열과 비슷한 오브젝트로 함수에 들어가는 모든 인수들을 표현한다.
2. `apply`메소드는 배열과 비슷한 Object를 받는것을 제외하고는 `call` 원시 메소드랑 비슷하게 동작한다.

`bind` 메소드는 단순히 새로운 함수를 반환한다. 이것이 실행되면, 새로운 함수는 원래의 값을 `this`로 설정하고 단순히 전달 된 원래 함수를 실행시킨다. 또한 `arguments`를 통해 전달됩니다.

이것은 흔한 관용구이다. ES5는 새로운 메소드 bind를 사용한다. 모든 `Function`오브젝트에 대해 this 행동을 implements 한다.

```javascript
let boundHello = person.hello.bind(person);
boundHello("world");
```

## 출처

[Understanding JavaScript Function Invocation and "this"](https://yehudakatz.com/2011/08/11/understanding-javascript-function-invocation-and-this/)