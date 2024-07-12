---
title: Spring Boot + Gradle 배포 파일 변경
description : jar에서 war로 패키징 방식 변경하기
categories: [Study, Spring boot]
tags: [Spring Boot, gradle, jar, war]
---

# Jar
- Java Archive
- class 파일과 메타 데이터, resources를 하나의 파일로 압축한 패키지 파일로, 배포와 전달이 용이하다.
- JSP나 서블릿 컨테이너에 대한 표준 기능을 활용하기 어려우며 동적 웹 컨텐츠 생성 및 관리가 제한적이기 때문에 웹 애플리케이션 개발을 할 때는 war 파일에 비해 제약 사항이 존재한다.
- 템플릿 엔진인 Thymeleaf 사용 시 JAR 패키징이 가능하다.
  - Thymeleaf은 스프링 부트가 권장하는 서버 사이드 템플릿 엔진으로, 스프링 부트에서 자동 설정을 지원하기 때문에 웹 서버가 필요한 War가 없어도 브라우저에 직접 띄울 수 있다. 또한, Thymeleaf는 서블릿으로 변환되지 않아 비즈니스 로직이 분리된다.
  - Thymeleaf은 JSP와 달리 순수 HTML 파일을 최대한 유지하기 때문에 웹에서 파일을 직접 열어도 내용 확인이 가능하다.
  - 단점은 JSP보다 응답 속도가 느리다는 것이다.
  - Spring의 경우, Thymeleaf에게 View의 역할을 맞기는 것을 추구한다.
- 확장자는 .jar이다.

<br/>

# War
- Web Archive
- 웹 구성 요소인 JSP, 서블릿 등과 웹 애플리케이션 실행을 위한 서블릿 컨테이너 (웹 컨테이너) 설정 파일, 라이브러리, 리소스 등을 포함해 한 번에 배포 및 실행이 가능하기 때문에 JAVA 웹 애플리케이션을 패키징하는 데 최적화 되어 있다.
- 웹 컨테이너나 서버에 종속적이기 때문에 특정한 웹 컨테이너 환경이 필요하며 배포 및 전달에 있어서 jar 파일에 비해 크기가 크고 번거로울 수 있다.
- 만약 View를 JSP로 구성한다면 WAR 패키징만 가능하다. 내장 톰캣을 사용하는 Spring Boot Application에서 JSP를 지원하지 않기 때문이다.
  - 서버 사이드 템플릿 엔진인 JSP는 서블릿 형태로 변환되어 실행된다. 서블릿은 자바 소스로 이루어져 있어서 JSP에서 자바 코드를 사용할 수 있는데, 이렇게 되면 View에 비즈니스 로직이 포함되어 복잡하고 무겁다.
  - JSP는 일반적인 Java MVC 구조에서 View 역할을 하며, HTML 코드와 JSP 코드가 함께 있기 때문에 서버를 통해 렌더링 되고, HTML 응답 결과를 받아야만 내용을 확인할 수 있다.
- 확장자는 .war이다.

<br/>

# JAR vs WAR
- JAR
    - 독립적인 실행이 가능한 애플리케이션을 구성할 때 사용하는 것을 추천한다.
    - 필자의 경우, 안드로이드와 협업을 할 때, 안드로이드에서 사용할 API를 개발하여 JAR 파일로 패키징을 했다.
- WAR
    - 웹 환경에서 실행되는 애플리케이션을 구성할 때 사용하는 것을 추천한다.
    - 필자의 경우, jsp 등을 활용하여 front를 구성하고 spring boot로 back을 구성할 때 사용했다.


<br/>

# 변경 방법

1. build.gradle에 필요한 plugin, bootWar, dependencies를 추가한다.

    ```gradle
    plugins {
        id 'java'
        id 'org.springframework.boot' version '3.2.6'
        id 'io.spring.dependency-management' version '1.1.5'
        id 'war'
    }

    ...

    bootWar {
        enabled = true
    }

    ...

    dependencies {
        ...
        implementation 'org.springframework.boot:spring-boot-starter-tomcat'
        ...
    }
    ```

<br/>

2. MybatisApplication.java에 SpringBootServletInitializer를 상속 받아 오버라이딩 한다.

    - SpringBootServletInitializer
        - Sevlet이 업데이트 되면서 아파치 톰캣이 외부 톰캣에서 동작하도록 하기 위해 읽었던 web.xml을 WebApplicationInitializer 인터페이스로 대신하게 되었다.
        - 이로 인해 web.xml이 없는 **Spring Boot** 웹 앱을 외부 톰캣에서 동작하려면 WebApplicationInitializer 인터페이스를 구현한 SpringBootServletInitializer 인터페이스를 상속 받아야 한다.
        - 위의 이유로, war 파일로 배포를 진행하지 않는다면 상속 받을 필요가 없다.

    ```java
    @SpringBootApplication
    @MapperScan(basePackages = "com.example.mybatis", annotationClass = Repository.class)
    public class MybatisApplication extends SpringBootServletInitializer {

        public static void main(String[] args) {
            SpringApplication.run(MybatisApplication.class, args);
        }
    }
    ```

<br/>

# Error
## FAILURE: Build failed with an exception

jar를 war로 변경하는 과정에서 FAILURE: Build failed with an exception가 발생했을 경우, .gradle 경로를 지정하면 된다.

![setting](/assets/img/post_img/war_jsp/setting.png)

<br/>

---

<br/>

# 참고

[cob 님 - JAR WAR 배포 차이점과 장단점](https://cocococo.tistory.com/entry/JAR-WAR-%EB%B0%B0%ED%8F%AC-%EC%B0%A8%EC%9D%B4%EC%A0%90%EA%B3%BC-%EC%9E%A5%EB%8B%A8%EC%A0%90)

[사람냄새나는 개발일기 님 - SpringBootServletInitializer 란 무엇이고 왜 상속을 받고 있는가?](https://serverwizard.tistory.com/165)

[민뭉아치 님 - [Spring boot] 배포 패키징 jar 에서 war로 변경](https://readinggeneral.tistory.com/entry/Spring-boot-%EB%B0%B0%ED%8F%AC-%ED%8C%A8%ED%82%A4%EC%A7%95-jar-%EC%97%90%EC%84%9C-war%EB%A1%9C-%EB%B3%80%EA%B2%BD)

잘못된 정보를 발견하시면 제보 부탁드립니다.