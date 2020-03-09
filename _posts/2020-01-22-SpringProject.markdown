---
layout: post
title:  "스프링 프로젝트"
date:   2020-01-22 18:00:00 +0900
categories: Rookie7
---

## 프로젝트 생성

* Spring Tool Suite 실행
* Spring Initializer
    * File -> New -> Spring Starter Project
    * Packaging : War
        * 원래 Spring에서는 Jar로 Web 개발 가능
    * Spring Boot Version : 2.1.12
    * MyBatis Framework
    * MySQL Driver
    * Spring Web
* Test
    * SampleApplicationTests.java
        * run
        * DB 설정이 누락되어 에러남
        * DB 설정 추가 후 run
* Hello World
    * HelloWorldController.java
    * 코드 작성 후 SampleApplication.java 파일 실행

``` java
package com.nhn.rookie7.sample;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class HelloWorldController {

    @GetMapping("/helloworld")
    @ResponseBody
    public String helloWorld() {
        return "Hello World!";
    }
}
```

* Tips
    * 파일 찾기
        * ctrl + shift + r
    * 클래스 찾기
        * ctrl + shift + t

## Web MVC

* pom.xml
    * pom.xml에 추가
    * jsp 설정을 위한 것

``` xml
<dependencies>
        ...
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>jstl</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-tomcat</artifactId>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>org.apache.tomcat.embed</groupId>
			<artifactId>tomcat-embed-jasper</artifactId>
			<scope>provided</scope>
		</dependency>
	</dependencies>
```

* application.properties
    * reference
        * https://docs.spring.io/spring-boot/docs/2.1.12.RELEASE/reference/html/common-application-properties.html

``` properties
spring.mvc.view.prefix=/WEB-INF/jsp/
spring.mvc.view.suffix=.jsp

application.message=Hello Rookie
```

* HelloWorldController
    * welcome method에 있는 this.message는 properties에 있는 message가 나옴.
        * @Value("${application.message:Hello World}")
            * 이 부분이 elvis operation과 같음

``` java
import java.util.Date;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class HelloWorldController {
   @Value("${application.message:Hello World}")
   private String message = "Hello World";

   @GetMapping("/")
   public String welcome(Model model) {
       model.addAttribute("time", new Date());
       model.addAttribute("message", this.message);
       return "welcome";
   }

   ...
}
```

* welcome.jsp

``` jsp
<!DOCTYPE html>
<html lang="en">
<body>
    Now : ${time}
    <br>


    Message: ${message}
</body>
</html>
```

* Reference
    * https://docs.oracle.com/javaee/6/tutorial/doc/gjddd.html
    * https://docs.oracle.com/javaee/7/tutorial/jsf-el.htm
* Tips
    * 코드 포맷팅
        * win : ctrl + shift + f
* todo.html
    * src/main/webapp/todo.html

``` html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Hello JavaScript</title>
</head>
<body>
TODO: 자바스크립트 배우기
</body>
</html>
```

* todo2.html

``` html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Hello JavaScript</title>
</head>
<body>
TODO: html/css 배우기
</body>
</html>
```

* todo 확인
    * 정적 파일 Root : `src/main/webapp`, `src/main/resources/static`

## 게시물 조회

* Model
    * Article.java

``` java
package com.nhn.rookie7.sample.model;

import java.util.Date;

public class Article {
    private Long seq;
    private String title;
    private String content;
    private Date regdate;

    // getter & setter
}
```

* tips
    * ctrl + 3 : getter 검색 후 generate
* mysql 데이터 추가

``` sql
use groupware: 

create table article(
	seq bigint primary key,
    title varchar(200),
    content varchar(200),
    regdate datetime
);

insert into article (seq, title, content, regdate) values (1, 'title1', 'content1', now());

select * from article;
```

* application.properties

``` properties
mybatis.config-location=classpath:mybatis-config.xml
```

* mybatis-config.xml
    * `typeAliases` : 타입의 별칭들
    * `mappers` : mapper xml들

``` xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <typeAliases>
        <package name="com.nhn.rookie7.sample.model"/>
    </typeAliases>
    <mappers>
        <package name="com.nhn.rookie7.sample.mapper"/>
    </mappers>
</configuration>
```

* reference
    * http://www.mybatis.org/mybatis-3/ko/index.html
* ArticleMapper.java

``` java
package com.nhn.rookie7.sample.mapper;

import org.apache.ibatis.annotations.Mapper;

import com.nhn.rookie7.sample.model.Article;

@Mapper
public interface ArticleMapper {

    Article findOne(Long seq);
}
```

* ArticleMapper.xml
    * src/main/resources/com/nhn/rookie7/sample/mapper/ArticleMapper.xml
    * alias 설정을 했기 때문에 Article만 적어도 됨
    * 인자가 하나인 경우 #{value}
        * 그렇지 않은 경우 인자 명으로 넣어주면 됨
    * reference
        * http://www.mybatis.org/mybatis-3/ko/index.html

``` xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.nhn.rookie7.sample.mapper.ArticleMapper">
    <select id="findOne" resultType="Article">
        select * from article where seq = #{value}
    </select>
</mapper>
```

* ArticleController

``` java
package com.nhn.rookie7.sample;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;

import com.nhn.rookie7.sample.model.Article;
import com.nhn.rookie7.sample.mapper.ArticleMapper;

@Controller
@RequestMapping("/articles")
public class ArticleController {

    @Autowired
    private ArticleMapper articleMapper;

    @GetMapping("/{seq}")
    public String detail(@PathVariable("seq") Long seq, Model model) {
        Article article = articleMapper.findOne(seq);
        model.addAttribute("detail", article);
        return "article/detail";
    }
}
```

* detail.jsp

``` jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Article</title>
</head>
<body>
    <div>
        <ul>
            <li>seq : ${detail.seq}</li>
            <li>title : ${detail.title}</li>
            <li>content : ${detail.content}</li>
            <li>regdate : ${detail.regdate}</li>
        </ul>
    </div>
</body>
</html>
```

## DevTools

* pom.xml

``` xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>
```

# Spring IoC - DI

## DI

* Without DI
    * 인터페이스는 의존성을 줄여주는 역할을 한다
    * 생성자에서 인터페이스를 new로 구현체를 생성하면 강결합이 생김
* With DI
    * setter를 사용하여 인터페이스에 구현체를 대입하면 강결합이 사라짐

## DI?

* 의존성을 주입하는 행위
* 직접 구상체를 생성해서 강결합으로 만드는 것이 아니라 클라이언트에게 의존성 주입 책임을 위임함으로써 해당 모듈 레벨에서는 약결합 상태를 유지할 수 있게 만드는 것
* xml 등 의존성 메타 정보를 빼야만 DI가 되는 것은 아니다.
    * 그저 클라이언트 또는 어떤 누군가에게 의존성의 책임을 전가하면 그것이 DI
* DI를 미뤄도 결국 상위 레벨에서 강결합 상태가 됨

## IOC

### DI를 끝까지 전가시켜보자

* 결국 누군가는 강결합을 지닐 수 밖에 없다.
* 결합도를 낮출 수가 없게 되므로 객체지향의 장점을 누를 수 없게 된다.
* 결국 프로그래머가 어플리케이션의 제어권을 가지고 있기 떄문이다.

### 그럼 제어권을 누구에게 줄까?

* DI를 관리하는 가상의 개념을 만든다
    * 개념(Concept) -> Type -> interface or class

### InstanceFactory

* 객체 생성의 책임을 위힘
    * 모든 클래스의 생성 책임을 가지고 있는 interface와 class 생성

### DependencyInjector

* 의존성 설정의 책임도 위임

### Instance + DependencyInjector = IoC Container

### Create Meta

* IoC를 meta 정보(xml)로 관리함
    * xml 파일을 파싱해서 객체 생성과 의존성을 주입
    * framework가 제어권을 가지고 있는 것
    * IoC Container를 가지고 있는 것이 framework이고 그렇지 않은 것이 Library

## 결론

* IoC란 말 그대로 `제어의 역전`
* 과거에 프로그래머가 컴파일 시에 가지고 있떤 객체 생성과 관계 설정의 `제어권`을 가상의 Container에 런타임시 넘겨주는(`역전`) 것임
* 그저 프로그래머가 할 일은 각 의존 관계 메타 정보만 정의하면 되는 것이며, 추후 어플리케이션 확장이 필요한 경우에 `코드 직접 수정이 아니라 메타 정보 변경`만으로 가능하게 되는 것이다(OCP)

## 참고
* NHN Basecamp