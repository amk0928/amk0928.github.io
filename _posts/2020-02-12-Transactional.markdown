---
layout: post
title:  "@Transactional"
date:   2020-02-12 18:00:00 +0900
categories: Rookie7
---

# AOP

## Aspect Oriented Programming

* OOP(Object Oriented Programming)을 돕는 보조적인 기술로, 애플리케이션을 다양한 측면에서 독립적으로 모델링하고, 설계하고, 개발할 수 있도록 만들어주는 것.
* 애플리케이션을 다양한 관점에서 바라보며 개발할 수 있게 도와준다.
![1.PNG](/assets/img/Transactional/1.PNG)
(토비의 스프링 3.1 Vol. 1 그림6-21)
* 왼쪽 그림처럼 부가기능이 핵심기능 안으로 침투해서 들어가면, 핵심기능 설계에 객체지향 기술의 가치를 온전히 부여하기가 힘들다.
* AOP는 애스펙트를 분리함으로써 핵심기능을 설계하고 구현할 때 객체지향적인 가치를 지킬 수 있도록 도와주는 것이다.
* 스프링 AOP는 프록시 방식의 AOP이다.
    \* 스프링은 AOP를 프록시 방식으로 만들어서 DI로 연결된 빈 사이에 적용해 타깃의 메소드 호출 과정에 참여해서 부가기능을 제공하도록 만들었다.
    \* 독립적으로 개발한 부가기능 모듈을 다양한 타깃 오브젝트의 메소드에 다이내믹하게 적용해주기 위해 가장 중요한 역할을 맡고 있는 것이 `프록시`

## AOP의 용어

* `타깃` : 부가기능을 부여할 대상
    \* 핵심 기능을 담은 클래스일 수도 있지만, 부가기능을 제공하는 프록시 오브젝트일 수도 있다.
* `어드바이스` : 타깃에게 제공할 부가기능을 담은 모듈
    \* 오브젝트로 정의하기도 하지만 메소드 레벨에서 정의할 수도 있다.
    \* 메소드 호출 과정에 전반적으로 참여하는 것도 있지만, 예외가 발생했을 때만 동작하는 어드바이스도 있다.
* `조인 포인트` : 어드바이스가 적용될 수 있는 위치
    \* 스프링의 프록시 AOP에서 조인 포인트는 메소드의 실행 단계뿐이다.
    \* 타깃 오브젝트가 구현한 인터페이스의 모든 메소드는 조인 포인트가 된다.
* `포인트컷` : 어드바이스를 적용할 조인 포인트를 선별하는 작업 또는 그 기능을 정의한 모듈
    \* 스프링 AOP의 조인 포인트는 메소드의 실행이므로 스프링의 포인트컷은 메소드를 선정하는 기능을 갖고 있다.
    \* 포인트컷 표현식은 메소드의 실행이라는 의미인 execution으로 시작하고, 메소드의 시그니처를 비교하는 방법을 주로 이용한다.
    \* 메소드는 클래스 안에 존재하는 것이기 때문에 메소드 선정이란 결국 클래스를 선정하고 그 안의 메소드를 선정하는 과정을 거치게 된다.
* `프록시` : 클라이언트와 타깃 사이에 투명하게 존재하면서 부가기능을 제공하는 오브젝트
    \* `DI(Dependency Injection)`를 통해 타깃 대신 클라이언트에 주입되며, 클라이언트의 메소드 호출을 대신 받아서 타깃에 위임해주며, 그 과정에서 부가기능을 부여한다.
        \* DI(의존관계 주입, 의존성 주입) : 오브젝트 레퍼런스를 외부로부터 제공(주입)받고 이를 통해 여타 오브젝트와 동적으로 의존관계가 만들어지는 것
    \* 스프링은 프록시를 이용해 AOP를 지원한다.
* `어드바이저` : 포인트컷과 어드바이스를 하나씩 갖고 있는 오브젝트
    \* 어떤 부가기능(어드바이스)를 어디에(포인트컷) 전달할 것 인가를 알고 있는 AOP의 가장 기본이 되는 모듈
    \* 스프링은 자동 프록시 생성기가 어드바이저를 AOP 작업의 정보로 활용한다.
    \* 어드바이저는 스프링 AOP에서만 사용되는 특별한 용어
* `애스펙트` : OOP의 클래스와 마찬가지로 애스펙트는 AOP의 기본 모듈
    \* 한 개 또는 그 이상의 포인트컷과 어드바이스의 조합으로 만들어지며 보통 싱글톤 형태의 오브젝트로 존재한다
    \* 클래스와 같은 모듈 정의와 오브젝트와 같은 실체(인스턴스)의 구분이 특별히 없다.

# @Transactional과 AOP에서의 주의사항

## @Transcational

```
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface Transactional {
@AliasFor("transactionManager")
String value() default "";
@AliasFor("value")
String transactionManager() default "";
Propagation propagation() default Propagation.REQUIRED;
Isolation isolation() default Isolation.DEFAULT;
int timeout() default TransactionDefinition.TIMEOUT_DEFAULT;
boolean readOnly() default false;
Class<? extends Throwable>[] rollbackFor() default {};
String[] rollbackForClassName() default {};
Class<? extends Throwable>[] noRollbackFor() default {};
String[] noRollbackForClassName() default {};


}
```

* 타깃 : `메소드와 타입`
    \* 메소드, 클래스, 인터페이스에 사용 가능
    \* @Transacational 애노테이션을 트랜잭션 속성 정보로 사용하도록 지정하면 스프링은 @Transactional이 부여된 모든 오브젝트를 자동으로 타깃 오브젝트로 인식
* 포인트컷 : `TransactionAttributeSourcePointcut`
    \*  TransactionAttributeSourcePointcut : 스스로 표현식과 같은 선정 기준을 갖고 있진 않고, @Transactional이 타입 레벨이든 메소드 레벨이든 상관없이 부여된 빈 오브젝트를 모두 찾아서 포인트컷의 선정 결과로 돌려준다.
\*  `대체(Fallback) 정책`
    \*  메소드 속성을 확인할 때 타깃 메소드, 타깃 클래스, 선언 메소드, 선언 타입(클래스, 인터페이스)의 순서에 따라서 @Transactional이 적용됐는지 차례로 확인하고, 가장 먼저 발견되는 속성정보를 사용하게 한다.
        \*  끝까지 발견되지 않으면 트랜잭션 속성 적용 대상이 아니라고 판단한다.
    \*  대체 정책에서 지정한 순서에 따라 항상 메소드에 부여된 @Transactional이 가장 우선적으로 적용된다.

### @Transactional의 사용

``` java
@Transactional
public int signUp(User user) {
    if(loginMapper.addressChk(user)==0) {
        loginMapper.insert(user);
        return 1;
    }
    else {
        return 0;
    }
}
```

* @Transactional 애노테이션을 적용하면 적용된 클래스 또는 메소드에 트랜잭션이 적용된다.

### @Transactional 설정

| Property | Type | Description | Default |
| -------- | ---- | ----------- | ------- |
| value | String | 사용할 트랜잭션 매니저를 선택하는 구분자 | "" |
| propagation | enum propagation | 트랜잭션 전파 설정 | REQUIRED(트랜잭션이 존재한다면 참여하고 없으면 새로 생성) |
| isolation | enum isolation | 트랜잭션 격리 수준 설정 | DEFAULT(데이터베이스에 의존) |
| timeout | int(second 단위) | 지정 시간 내에 해당 메소드 수행이 완료되지 않은 경우 rollback, propagation이 REQUIRED나 REQUIRED\_NEW인 경우에만 적용 | -1(데이터베이스에 의존하거나 없음) |
| readOnly | boolean | read만 가능하도록 설정, REQUIRED나 REQUIRED\_NEW에만 적용됨 | false |
| rollbackFor | Array of Class(Throwable을 상속받아야 함) | rollback 할 클래스 객체(ex.class) | {} |
| rollbackForClassName | Array of Class(Throwable을 상속받아야 함) | rollback 할 클래스 이름("ex") | {} |
| noRollbackFor | Array of Class(Throwable을 상속받아야 함) | rollback을 하지 않을 클래스 객체(ex.class) | {} |
| noRollbackForClassName | Array of Class(Throwable을 상속받아야 함) | rollback을 하지 않을 클래스 이름("ex") | {} |

* 모든 속성에 Default가 정해져 있기 때문에 모두 생략 가능하다.

### @Rollback

* 테스트 코드에서 @Transactional을 사용하게 되면 테스트가 끝나면 자동으로 롤백된다.
* 테스트 코드에서 적용한 트랜잭션을 그대로 반영하고 싶다면 @Rollback(false)로 롤백을 하지 않을 수 있다.
    \* @Rollback(false)를 지정하면 예외가 발생하지 않는 한 트랜잭션은 커밋된다.

## AOP에서의 주의사항

### 프록시 방식 AOP는 같은 타깃 오브젝트 내의 메소드를 호출할 때는 적용되지 않는다.

* 프록시 방식의 AOP에서는 프록시를 통한 부가 기능의 적용은 `클라이언트`로부터 호출이 일어날 때만 가능하다.
    \* `클라이언트` : 인터페이스를 통해 타깃 오브젝트를 사용하는 다른 모든 오브젝트
    \* 타깃 오브젝트가 자기 자신의 메소드를 호출할 때는 프록시를 통한 부가기능의 적용이 일어나지 않는다.
![2.PNG](/assets/img/Transactional/2.PNG)
(토비의 스프링 3.1 Vol. 1 그림6-23)
* 그림의 [1]과 [3]처럼 클라이언트로부터 메소드가 호출되면 프록시를 통해 타깃 메소드로 호출이 전달되므로 트랜잭션 부가기능이 전달될 것이다.
* 그림의 [2]는 타깃 오브젝트 내로 들어와서 타깃 오브젝트의 다른 메소드를 호출하는 경우에는 프록시를 거치지 않고 직접 타깃의 메소드가 호출된다.
    \* 따라서 [1]의 클라이언트를 통해 호출된 delete() 메소드에는 부가기능이 적용되지만 [2]를 통해 update()메소드가 호출될 때는 update()메소드에 지정된 부가기능이 반영되지 않는다.

``` java
public int signUpMapper(User user) {
    return signUp(user);
}


@Transactional(readOnly=true)
public int signUp(User user) {
    if(loginMapper.addressChk(user)==0) {
        loginMapper.insert(user);
        return 1;
    }
    else {
        return 0;
    }
}
```

``` java
int result = loginServiceImpl.signUpMapper(user);
```

* 위 코드는 signUp에서 write 트랜잭션을 하지만 에러가 발생하지 않는다. 동일한 오브젝트 내의 signUpMapper메소드가 signUp 메소드를 호출했기 때문에 @Transactional의 readOnly 설정이 signUp 메소드에 적용되지 않기 때문이다.

``` java
@Transactional(readOnly=true)
public int signUpMapper(User user) {
    return signUp(user);
}


public int signUp(User user) {
    if(loginMapper.addressChk(user)==0) {
        loginMapper.insert(user);
        return 1;
    }
    else {
        return 0;
    }
}
```

* 위 코드는 에러가 발생한다. signUpMapper 메소드의 @Transactional readOnly 설정이 signUpMapper 메소드에서 호출한 signUp 메소드에 전파되었기 때문이다.

### private 메소드에 @Transactional이 적용되지 않는 이유

* 프록시 방식의 AOP에서는 protected, private, package-visible 메소드에 프록시를 통한 부가기능을 적용한 경우에 오류는 발생하지 않지만, 부가기능이 메소드에 적용되지 않는다.
    \* protected, private, package-visible 메소드를 호출하기 위해서는 같은 타깃 오브젝트 내에서 호출해야 하는데, 프록시 방식 AOP는 같은 타깃 오브젝트 내의 메소드를 호출할 때 적용되지 않기 때문이다.

## 정리

* 스프링 AOP는 프록시 방식의 AOP이다.
* @Transactional 애노테이션을 이용해 클래스나 메소드, 인터페이스에 트랜잭션을 적용할 수 있다.
* 프록시 방식 AOP는 같은 타깃 오브젝트 내의 메소드를 호출할 때는 적용되지 않는다.

## 참고

https://docs.spring.io/spring/docs/current/spring-framework-reference/data-access.html#transaction
https://docs.spring.io/spring/docs/3.2.4.RELEASE/spring-framework-reference/html/aop.html#aop-proxying
토비의 스프링 3.1 Vol. 1