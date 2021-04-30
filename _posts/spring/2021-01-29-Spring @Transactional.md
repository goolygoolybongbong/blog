---
layout: single
classes: wide
categories: spring
title: "Spring @Transactional"
---

## Spring @Transactional

`@Transactional` 옵션은 클래스에 붙으면 클래스 내 **모든 메소드에 전체적으로** 트랜잭션이 적용되고 메소드에 붙으면 **해당 메소드만** 트랜잭션이 적용된다.

### 주의사항

`javax.transaction.Transactional`

`org.springframework.transaction.annotation.Transactional`

두 곳중 아래가 Spring 트랜잭션이고 옵션을 설정할 수 있음에 주의한다.

## @Transaction Options

### Isolation Options

레벨이 낮아지면 동시성은 증가되지만 데이터 무결성에 문제가 발생할 수 있고, 반대로 높아지면 데이터의 무결성은 유지하는 데 반해 동시성은 떨어질 수 있다. (레벨이 높아질 수록 비용이 높아진다.)

1. DEFAULT

    기본 격리 수준(기본 설정, DB의 Isolation Level을 따른다.)

2. READ_UNCOMMITED (level 0)

    - 커밋되지 않은(트랜잭션 처리중인) 데이터에 대한 읽기를 허용
    - 처리 중인 데이터를 다른 트랜잭션?에서 읽을 수 있다.
    - Shared lock이 걸리지 않는다.
    - 오라클은 이 레벨을 지원하지 않는다
    - Dirty read, Non-Repeatable read, Phantom read 현상 발생

    > Dirty Read
    다른 트랜잭션에서 수정 중인 데이터를 읽는 현상을 dirty read라고 한다. level1을 통해 막을 수 있다.

    **Non-Repeatable**<br>
    Read한 트랜잭션 내에서 같은 쿼리를 두 번 수행할 때, 그 사이에 다른 트랜잭션이 값을 수정 또는 삭제함으로써 두 쿼리가 상이하게 나타나는 비 일관성이 발생하는 것을 말한다.

    **Phantom Read**<br>
    한 트랜잭션 안에서 일정범위의 레코드를 두 번 이상 읽을 때, 첫 번째 쿼리에서 없던 유령 레코드가 두 번째 쿼리에서 나타나는 현상을 말한다.
    레벨 3의 고립화 수준을 통해 막을 수 있다.

3. READ_COMMITED (level 1)
    - 트랜잭션이 커밋된 확정 데이터만 읽기 허용
    - 처리 중인 데이터에는 접근할 수 없다(Dirty Read 불가능)

4. REPEATABLE_READ (level 2)
    - 선행 트랜잭션이 완료될 때까지 후행 트랜잭션 중 SELECT 문장을 사용하는 선행 트랜잭션이 사용하는 모든 데이터에 shared lock이 걸려 **수정**이 불가능 하다.
    - 선행 트랜잭션이 읽은 데이터는 트랜잭션이 종료될 때까지 후행 트랜잭션이 갱신하거나 삭제하는 것을 막음으로써 같은 데이터를 두 번 쿼리했을 때 일관성 있는 결과를 리턴한다.

    > Shared Lock (Read Lock, Lock-S)
    두 개 이상의 트랜잭션이 read-only 권한을 부여 받았을 때 존재한다. 한 트랜잭션이 데이터에 대한 shared lock을 받았을 때, 다른 트랜잭션이 같은 데이터를 요청하게 되면 shared lock을 받게 된다. 여러 트랜잭션이 공유할 수 있다.Shared Lock은 읽기 완전성을 지원한다. 레코드에 대한 read-only 요청을 했을 때, 레코드가 update 중이 아님을 보장한다.Shared Lock은 레코드에 대한 업데이트도 막을 수 있다.

    Exclusive Lock (Write Lock, Lock-X)
    Exclusive Lock를 갖고 있으면, 데이터에 대한 읽기 뿐만 아니라 쓰기도 가능하다. 다른 트랜잭션을 읽거나 쓸 수 없다.Exclusive Lock은 다른 그 어떤 Lock도 얻지 못하도록 막는다.오직 한 트랜잭션이 단 한 번 소유할 수 있다.Shared LockExclusive Lock읽기 전용 모드읽기 및 쓰기 모드Exclusive lock이 없으면 얻을 수 있다그 어떤 lock도 없어야 얻을 수 있다데이터가 업데이트 되는 것을 막는다데이터가 읽히고 쓰이는 것을 막는다여러 트랜잭션이 공유할 수 있다오직 한 트랜잭션만 사용할 수 있다.

5. SERIALIZABLE (level 3)
    - 완벽한 읽기 일관성 모드를 제공
    - 선행 트랜잭션이 읽은 데이터를 후행 트랜잭션이 갱신하거나 삭제하지 못할 뿐만 아니라 중간에 새로운 레코드를 삽입하는 것도 막아줌

## Propagation Options (전파 옵션)

트랜잭션 동작 도중 다른 트랜잭션을 호출하는 상황에 선택할 수 있는 옵션

@Transaction의 Propagation 속성을 통해 피호출 트랜잭션의 입장에서는 호출한 쪽의 트랜잭션을 그대로 사용할 수도 있고, 새롭게 트랜잭션을 생성할 수 있다.

1. REQUIRED
    - 디폴트 속성, 부모 트랜잭션 내에서 실행하며 부모 트랜잭션이 없을 경우 새로운 트랜잭션을 생성한다.

2. SUPPORTS
    - 이미 시작된 트랜잭션이 있으면 참여하고 그렇지 않으면 트랜잭션 없이 진행하게 만든다.

3. REQUIRED_NEW
    - 부모 트랜잭션을 무시하고 무조건 새로운 트랜잭션이 생성

4. MANDATORY
    - REQUIRED와 비슷하게 이미 시작된 트랜잭션이 있으면 참여한다.
    - 반면에 트랜잭션이 시작된 것이 없으면 새로 시작하는 대신 예외를 발생시킨다.
    - 혼자서는 독립적으로 트랜잭션을 진행하면 안되는 경우에 사용한다.

5. NOT_SUPPORTED
    - 트랜잭션을 사용하지 않게 한다.
    - 이미 진행중인 트랜잭션이 있으면 중첩 트랜잭션을 보류시킨다.

6. NEVER
    - 트랜잭션을 사용하지 않도록 강제한다.
    - 이미 진행중인 트랜잭션도 존재하면 안된다 있다면 예외를 발생시킨다.

7. NESTED
    - 이미 진행중인 트랜잭션이 있으면 중첩 트랜잭션을 시작한다.
    - 중첩 트랜잭션은 트랜잭션 안에 다시 트랜잭션을 만드는 것이다.
    - 하지만 독립적인 트랜잭션을 만드는 REQUIRES_NEW와 다름
    - REQUIRED와 동일하게 작동하지만 중요한 차이점은 SAVEPOINT를 지정한 시점까지 롤백이 가능하다.
        - 중첩된 자식 트래잭션이 롤백 되더라도 부모 트랜잭션은 영향 받지 않음..
        그렇다면 독립적인 트랜잭션을 만드는 것과 차이는?
        부모가 롤백 된다면 자식이 커밋한 것 까지 롤백 된다. REQUIRED_NEW같은 경우에는 독립된 트랜잭션이라 롤백되지 않는다!

## readOnly Option (읽기 전용 옵션)

- 트랜잭션을 읽기 전용으로 설정할 수 있다.
- 성능을 최적화하기 위해 사용할수도 있고, 특정 트랜잭션 작업 안에서 쓰기 작업이 일어나는 것을 의도적으로 방지하기 위해 사용할 수 있다.
- 일부 트랜잭션 매니저는 쓰기 작업을 허용할 수 있다.
- 읽기 전용 트랜잭션이 시작된 이후 INSERT, UPDATE, DELETE 같은 쓰기 작업이 진행되면 예외를 발생한다.

## 트랜잭션 롤백 예외 (rollback-for, rollbackfor, rollbackForClassName)

- 선언적 트랜잭션에서는 런타임 예외가 발생하면 롤백한다.
- 반면에 예외가 전혀 발생하지 않거나 체크 예외가 발생하면 커밋한다.

    > 체크 예외를 커밋 대상으로 삼은 이유는 체크 예외가 예외적인 상황에서 사용되기보다는 리턴 값을 대신해서 비즈니스적인 의미를 담은 결과를 돌려주는 용도로 많이 사용되기 때문이다.

- 스프링에서는 데이터 액세스 기술의 예외는 런타임 예외로 전환돼서 던져지므로 런타임 예외만 롤백 대상으로 삼은 것이다.
- 하지만 기본 동작방식을 바꿀 수도 있다.
  - 체크 예외지만 롤백 대상으로 삼아야 하는 것이 있다면 XML의 rolback-for 애트리뷰트나 애노테이션의 rollbackFor 또는 rollbackForClassName 앨리먼트를 이용해서 예외를 지정하면 된다.
  - rollback-for 나 rollbackForClassName 은 예외 이름을 넣으면 되고, rollbackFor 는 예외 클래스를 직접 넣는다.

- @Transactional 에서는 다음과 같이 클래스 이름 대신 클래스를 직접 사용해도 된다.
@Transactional(readOnly=true, rollbackFor=NoSuchMemberException.class)

▶ rollbackFor 속성

특정 예외가 발생 시 강제로 Rollback

설정 예: @Transactional(rollbackFor=Exception.class)

▶ noRollbackFor 속성

특정 예외의 발생 시 Rollback 처리되지 않음

설정 예: @Transactional(noRollbackFor=Exception.class)

## 출처

토비의 스프링(책)
