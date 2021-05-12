---
layout: single
classes: wide
categories: ["JavaScript"]
title: "JavaScript 프로토타입 ____proto____"
tags: ["JavaScript"]
---
<br><br>

# 경고 DEPRECATED 된 기술입니다.

[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/proto)도 찾아보지 않고 블로그 글이 너무 좋아서 정리하가다 발생한 참사..ㅠ ![sad](/blog/assets/images/ogu/sad-with-blanket.png)

<br>
![deprecated](/blog/assets/images/javascript/2021-05-12-deprecated.png)

## 프로토타입기반 프로그래밍

객체지향 프로그래밍의 한 형태의 갈래로 클래스가 없고, 클래스 기반의 상속을 사용하는 것과는 다르게, **객체의 원형인 프로토타입**을 이용해여 새로운 객체를 만들어내는 프로그래밍 기법입니다.

클래스리스(class-less), 프로토타입 지향(prototype-oriented), 인스턴스 기반(instance-based)프로그래밍이라고 합니다.
<br><br>

## 자바스크립의 프로토타입

자바스크립트의 프로토타입이란 용어는 두가지로 나뉜다.

1. **Prototype Property가 가리키고 있는** `Prototype Object`
2. 자기 자신을 만들어낸 **객체의 원형** `Prototype Link`
<br><br>

자바스크립트 레퍼런스의 프로토타입에 대한 정의는 **객체의 원형**을 의미하는 `Prototype Link`로 정의하지만 `Prototype Property`를 이용한 객체의 확장에 대해서만 다루는데 이 둘의 차이를 제대로 이해하는 것은 중요합니다.
<br><br>

**프로토타입 정의** : 자기 자신을 생성하게 한 자신의 원형 객체

모든 함수 객체의 `Constructor`는 `prototype`이란 프로퍼티를 가지고 있다. 이 프로퍼티는 객체가 생성될 당시 만들어지는 객체 자신의 원형이될 `prototype` 객체를 가리킨다.

`prototype`은 자신을 만든 원형이 아닌 자신을 통해 만들어질 객체들이 원형으로 사용할 객체(~~Not for me, But for you~~), Default로 emtpy object를 가리킨다.

| Prototype Link | Prototype Object |
| --- | --- |
| `__proto__` | `prototype` |
| prototype에 대한 링크, 상위에서 물려 받은 객체의 프로토타입에 대한 정보 | 자신을 원형으로 만들어질 새로운 객체들이 사용할 프로토타입, 하위로 물려줄 속성들 |

![prototype-relation](/blog/assets/images/javascript/2021-05-12-prototype-relation.png)

## 출처

[Javascript 기초 - Object prototype 이해하기 \|  Insanehong's Incorrect Note](http://insanehong.kr/post/javascript-prototype/)