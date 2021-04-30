---
layout: single
classes: wide
categories: spring
title: "의존성 역전 원리(Dependency Inversion Principle)"
tags: [Design Pattern]
---

의존성 역전 원리는 모듈간 낮은 결합도(loose coupling)와 테스트 용이성(testablility)을 확보하는데 유용하다. 확장 가능하고(scalable) 클라우드 환경에 적응력있는(adaptive) 응용프로그램 디자인을 위해 의존성 역전의 원리가 필요하다.

## IoC(Inversion of Control, 제어 역전)

IoC는 전통적인 방식에 반대되는 흐름으로 코드가 진행되는 것을 말하는 일반적인 용어입니다. Dependency Injection과 혼동하지 않도록 주의해야 한다. 오히려 **Dependency Inversion Principle이 IoC의 한 형태이다**.

Martin Fowler는 IoC와 Dependency Injection과 동일한 축소된 의미로 사용을 허용하는 모습을 맘에 들어하지 않는다.

라이브러리와 프레임워크의 관계는 IoC를 설명하는 단골 예시다. 라이브러리를 사용할 때는 내 코드가 라이브러리 코드(외부 코드)를 호출하지만 프레임워크를 사용할 때는 프레임워크(외부 코드)가 내 코드를 호출한다. 상호작용(interactive) 프로그래밍 모델과 반응형(reactive) 프로그래밍 모델도 하나의 예다.

IoC는 헐리웃에서 오디션이 끝난 후 오디션 참가자에게 하는 말때문에 Hollywood Principle로 불리기도 한다. (합격했는지 귀찮게 전화 말라. 합격하면 우리가 너에게 연락할 것이다.)

> Don't call us, we'll call you.

### IoC 목적

- 실행에서 작업의 수행을 분리하기 위해(To decouple the execution of a task from implementation.)
- 의도한 작업 모듈에 집중하기 위해(To focus a module on the task it is designed for.)
- 모듈을 가정으로 부터 자유롭게 하기 위해(To free modules from assumptions about how other systems do what they do and instead rely on contracts.)
- 모듈 교체에 따른 부작용을 막기 위해(To prevent side effects when replacing a module.)

## Dependency Inversion Principle(DIP, 의존성 역전 원리)

DIP는 의존관계를 갖는 모듈 인스턴스의 구성이 추상화에 의존하는 것을 뜻한다.

1. High-level modules should not depend on low-level modules. Both should depend on abstractions.
2. Abstractions should not depend on details. Details should on abstractions.

의존 주체 모듈 인스턴스가 의존 대상 모듈 인스턴스를 직접 생성하는 것이 전통적인 흐름이라고 하면 이 전통적 흐름에서는 의존 대상 코드의 형식이 의존 주체 코드에 의해 **결정되기** 때문에 의존 대상 코드의 다형성이 동작하기 어렵다. 반면 DIP가 사용되면 구체적인 의존 관계가 추상화에 의한 **런타임에 결정**되기 때문에 다형성을 적극적으로 활용할 수 있으며 **모듈의 재사용성**이 높아진다.

## Dependency Injection(의존성 주입)

많은 사람들이 Dependency Injection과 Dependency Inversion Principle을 혼동합니다.

DI는 DIP를 구현하는 기법 중 하나입니다. 의존 주체 모듈의 공용(public)으로 노출된 멤버를 통해 의존 대상 모듈 인스턴스가 제공된다. DI는 다시 세부적으로 구분된다.

### Constructor Injection(생성자 주입)

DI에서 가장 많이 사용되는 방법이다. 의존 주체 모듈의 생성자 매개변수로 의존 대상 모듈 인스턴스가 주입된다. 의존 주체 인스턴스가 생성됨과 동시에 의존성이 해결되기 때문에 일단 의존 주체 인스턴스가 생성되면 의존성 해결 여부를 검사할 필요가 없다. 정적 언어의 경우 의존성이 해결되지 않으면 의존 주체 인스턴스를 생성할 수 없기 때문에 모듈간 의존 관계가 문법적으로 명확히 드러나며 의존성이 누락될 위험이 컴파일러에 의해 차단된다.

### Property Injection(속성 주입)

공용으로 노출된 쓰기 가능한 속성을 통해 의존성을 주입하는 방법이다. Setter Injection이라고도 부른다. 속성이 외부에 노출된 정보를 의미하는지 의존 대상 모듈을 의미하는지 별도의 설명이 없으면 구분하기 어렵다. 속성 설정은 의존 주체 인스턴스가 생성된 후에 진행되기 때문에 의존 주체 인스턴스는 필요한 의존성이 해결되지 않은 상태일 수 있다.

### Interface Injection(인터페이스 주입)

Interface Injection은 주입을 위한 수단(공용 멤버)를 인터페이스를 통해 정의한다. Property Injection과 비슷하지만 인터페이스를 통해 의존성이 노출되기 때문에 의존관계가 명확하며 모듈 사이의 결합도가 추상화를 통해 더 낮아진다.

## IoC Container

IoC Container는 Dependency Inversion Principle이 적용된 모듈의 조립을 도와주는 도구이다. 하지만 DIP 디자인이 사용되었다고 해서 IoC Container가 반드시 필요한 것은 아니다.

IoC Container는 모듈 조립 기능과 더불어 모듈 개체의 수명을 관리하는 기능을 제공한다. 예를 들어 HTTP 서버의 경우 요청마다 각각 의존 대상 모듈 인스턴스를 만들고 요청에 대한 응답이 완료되면 요청 처리 과정에서 생성된 모든 인스턴스를 삭제(혹은 폐기)할 수 있다.

## Service Locator

Service Locator는 Dependency Injection과 다른 DIP 적용법이다. DI에서 의존 주체 모듈은 의존성을 외부의 주입에 의해 수동적으로 해결하는 반면 Service Locator를 사용하는 의존 주체 모듈은 의존성이 필요한 시점에 능동적으로 의존성 해결을 Service Locator에 요청한다.

### Service Locator는

의존 주체 모듈이 매개변수를 가지지 않는 기본 생성자를 통해 생성되어야 하는 제약 조건이 있는 경우나
의존 대상 모듈이 준비되기 전에 의존 주체 모듈 인스턴스가 만들어져야 하는 경우 등에 사용될 수 있다.

- Service Locator가 적용된 의존 주체 모듈은 공용 API를 통해 외부 모듈 의존관계가 노출되지 않는다.
- 의존 주체 모듈은 IoC Container를 직접 사용하기 때문에 IoC Container의 존재를 알고 있으며 필수적으로 의존한다.
- IoC Container가 정적인 코드를 통해 제공되기 때문에 병렬 테스팅이 어렵다.

## 출처

토비의 스프링(책)
