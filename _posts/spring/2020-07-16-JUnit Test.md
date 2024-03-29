---
layout: single
classes: wide
categories: spring
title: "JUnit Test"
tags: [Spring, Test, Junit]
---

## 테스트란?

예상하고 의도했던 코드가 정확히 동작하는지를 확인해서, 만든 코드를 확신할 수 있게 해주는 작업이다.

## 작은 단위의 테스트

- 테스트는 가능하면 작은 단위로 쪼개서 집중해서 할 수 있어야 한다. (관심사의 분리)
- 작은 단위의 코드에 대해 테스트를 수행하는 것을 **단위 테스트**
- 단위 테스트를 하는 이유는 개발자가 설계하고 만든 코드가 원래 의도한 대로 동작하는지를 개발자 스스로 빨리 확인받기 위해서다.
- 확인의 대상과 조건이 간단하고 명확할수록 좋다.

## 자동수행 테스트 코드

- 번거로운 작업 없이 자주 반복할 수 있다.
- junit을 이용하여 자동화

## JUnit

Java를 테스트하기 위한 **프레임워크**

프레임워크의 특징 : IoC, 개발자가 만든 클래스에 대한 제어 권한을 넘겨 받아서 주도적으로 애플리케이션의 흐름을 제어한다.

## 테스트 메소드 전환

테스트가 main() 메소드로 만들어졌다는 건 제어권을 직접 갖는다는 의미
따라서 JUnit은 main() 메소드가 아닌 다른 메소드를 작성하여 테스트를 함

### JUnit 테스트 메소드 요구조건

1. 메소드가 public으로 선언
2. `@Test` Annotation
3. 리턴 값이 void

### 테스트 결과의 검증

`import static org.hamcrest.CoreMatchers.is`
`import static org.junit.Assert.assertThat`
assertThat()의 첫 번째 파라미터의 값을 뒤에 나오는 매처라고 불리는 조건으로 비교해서 일치하면 다음으로 넘어가고, 아니면 테스트가 실패하도록 만들어 준다.

`assertThat($VALUE1, is($VALUE2))`

### 예외상황 테스트 방법

`@Test(expected=$EXPECTED_EXCEPTION.class)`

테스트 메소드의 노테이션에 expected 엘리먼트에 원하는 예외 클래스를 넣으면 된다.
테스트 중 예상한 예외가 발생하면 테스트를 통과하고 아니면 실패한다.

### 포괄적인 테스트를 지향하자

돌아갈만한 테스트가 아닌

> "항상 네거티브 테스트를 먼저 만들어라" - 로드 존슨(스프링 창시자)

ex) 존재하지 않는 id가 주어졌을 때는 어떻게 반응할지를 먼저 결정하고, 이를 확인할 수 있는 테스트를 먼저 만든다.

## 테스트 코드에서 중복 코드 제거하는 방법

### Fixture

JUnit의 `@Before` (매번 테스트 메소드를 실행하기 전에 먼지 실행시켜주는 기능)을 활용한다

`@Test`와 `@After, @Before` 간 주고받을 정보나 오브젝느가 있다면 인스턴스 변수를 이용해야 한다.

만약, 테스트 메소드의 일부에서만 공통적으로 사용하는 코드가 있다면?

- 일반적인 메소드 추출 방법을 사용하여 테스트 메소드가 직접 호출하도록 한다.
- 공통적인 특징을 가진 테스트 메소드를 모아 별도의 클래스로 만든다.

## JUnit의 동작 순서

1. 테스트 클래스에서 `@Test`가 붙은 public이고 void형이며 파라미터가 없는 테스트 메소드를 모두 찾는다.
2. 테스트 클래스의 오브젝트를 하나 만든다.
3. `@Before` 가 붙은 메소드가 있으면 실행한다.
4. `@Test` 가 붙은 메소드를 하나 호출하고 테스트 결과를 저장해둔다.
5. `@After` 가 붙은 메소드가 있으면 실행한다.
6. 나머지 테스트 메소드에 대해 2~5번을 반복한다.
7. 모든 테스트의 결과를 종합해서 돌려준다.

### 주의

- 각 테스트 메소드를 실행할 때마다 테스트 클래스의 오브젝트를 새로 만들어 진다.
- 한번 만들어진 테스트 클래스의 오브젝트는 하나의 테스트 메소드를 사용하고 나면 버려진다.
- 각 테스트가 서로 영향을 주지 않고  독립적으로 실행됨을 확실히 보장해준다.

<br><br>

## 테스트를 위한  애플리케이션 컨텍스트 관리

### 스프링 테스트 컨텍스트 프레임워크 적용

`@Runwith(SpringJUnit4ClassRunner.class)`

Runwith은 JUnit 프레임워크의 테스트 실행 방법을 확장할 때 사용하는 애노테이션이다.
SpringJUnit4Class라는 JUnit용 테스트 컨텍스트 프레임워크 확장 클래스를 지정해주면 JUnit이 테스트를 진행하는 중에 테스트가 사용할 애플리케이션 컨택스트를 만들고 관리하는 작업을 진행해준다.

`@ContextConfiguaration("$XML_FILE")`

자동으로 만들어줄 애플리케이션 컨텍스트의 설정파일 위치

### 테스트 메소드의 컨텍스트 공유

스프링의 JUnit확장 기능은 테스트가 실행되기 전에 딱 한 번만 애플리케이션 컨텍스트를 만들고, 테스트 오브젝트가 만들어질 때마다 **특별한 방법**을 이용해 애플리케이션 컨텍스트 자신을 테스트 오브젝트의 특정 필드에 주입해 준다. 컨텍스트의 공유로 테스트 수행 속도는 매우 빨라진다.

### 테스트 클래스의 컨텍스트 공유

테스트 클래스가 **같은 설정파일**을 이용하는 경우에는 테스트 수행 중에 **단 한개의 애플리케이션 컨텍스트**만 만든다.

### @Autowired

`@Autowired`가 붙은 인스턴스 변수가 있으면, 테스트 컨텍스트 프레임워크는 변수 타입과 일치하는 컨텐스트 내의 빈을 찾아 주입해준다.

스프링 애플리케이션 컨텍스트는 초기화할 때 자기 자신도 빈으로 등록한다. 따라서 ApplicationContext타입의 빈이 존재하고 DI도 가능하다.

### @DirtiesContext

`@DirtiesContext` 는 테스트 메소드에서 애플리케이션 컨텍스트의 구성이나 상태를 변경한다는 것을 테스트 컨텍스트 프레임워크에 알려준다.

이 애노테이션이 붙은 테스트 클래스는 컨텍스트를 공유를 허용하지 않는다.

### 메소드에 DirtiesContext 적용하기

**하나의 메소드**에만 컨텍스트 상태를 변경한다면 메소드 레벨에 @DirtiesContext를 적용하는것이 낫다. 
해당 메소드의 실행이 끝나면 이후에 진행되는 테스트를 위해 변경된 애플리케이션 컨텍스트는 폐기되고 새로운 어플리케이션 컨텍스트가 만들어진다.

### 테스트를 위한 별도의 DI 설정

테스트용 DataSource 클래스가 빈으로 정의된 테스트 전용 설정파일을 따로 만들어두는 방법이 있다.
번거롭게 수동 DI 하는 코드나 `@DirtiesContext` 도 필요없다.
