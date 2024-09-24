---
title: Spring Boot + Gradle + MyBatis Setting
categories: [Study, Spring boot]
tags: [Spring Boot, gradle, mybatis]
image:
    path : /assets/img/post_img/mybatis/mybatis.png
    alt : MyBatis Logo
description: >-
    Spring boot에 MyBatis 세팅하기
---

# 주요 SKILL
### Framework
- Spring Boot 3.2.6
- MyBatis 3.0.3

### Build Tool
- Gradle - groovy

### Database
- MySQL

### Language
- JAVA

    > Spring Boot 3.0.0 이상의 버전에서는 자바 17 이상을 사용해야 함
    {: .prompt-tip}

- xml

<br/>

# 프로젝트 구조

{% highlight text %}
main
    ㄴ java
        ㄴ com.example.mybatis
            ㄴ user
                ㄴ controller
                    ㄴ UserController.java
                ㄴ repository
                    ㄴ UserDao.java
                ㄴ service
                    ㄴ UserService.java
                    ㄴ UserServiceImpl.java
                ㄴ dto
                    ㄴ UserDto.java
            ㄴ MybatisApplication.java
    ㄴ resources
        ㄴ mapper
            ㄴ UserMapper.xml
        ㄴ aplication.properties
{% endhighlight %}

<br/>

# Source
### build.gradle
- 필자는 [Spring Boot Initializr](https://start.spring.io/)에서 프로젝트를 생성해 IntelliJ에서 Open 했다.
- 큰 차이는 없지만 IDEA에서 생성하는 것보다 보기 편해서 처음으로 사용하는 프레임워크가 있을 경우 **Spring Boot Initializr**을 사용하고는 한다.

|                Spring Boot Initializr                |                      IntelliJ                      |
| :--------------------------------------------------: | :------------------------------------------------: |
| ![init](/assets/img/post_img/mybatis/initializr.png) | ![init](/assets/img/post_img/mybatis/intellij.png) |

```gradle
    //톰캣을 사용해 웹 애플리케이션을 작성하기 위한 스타터
    implementation 'org.springframework.boot:spring-boot-starter-web'
    //mybatis를 사용한 테스트 코드 작성 시 필요
    testImplementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter-test:3.0.3'
    //mybatis build
    implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:3.0.3'
    // mysql 커넥터
    runtimeOnly 'com.mysql:mysql-connector-j'
```

> mybatis-spring-boot-starter는 JAVA 6 이상의 버전을 요구하며, 스타터의 버전에 따라 요구하는 Spring or Spring Boot의 버전이 다르다.
참고 : [Mybhatis-Spring-Boot-Starter 소개 문서 번역](https://kgmyh.github.io/blog/2017/12/22/Mybatis-SpringBoot/)
{: .prompt-info }

<br/>

### application.properties

- url의 DB명 이후는 필요한 것만 추가하면 된다.
    - useUnicode : true or false로 설정. true로 설정 시 한글 입력 가능
    - characterEncoding : encoding 방식 설정. 한글 입력 시 utf-8 or euc-kr 사용
    - allowMultiQueries : true로 설정 시 다중 쿼리 전송 가능
        - ex. String sql = "insert into user value(4,"ho"); select * from user where id = 4;"
        - 한 번의 호출로 여러 개의 쿼리를 실행할 수 있다.
    - serverTimezone : 설정한 timezon으로 시간이 설정됨
- classpath는 src/main/resources를 가리키기 때문에 resources 이후의 경로만 적어주면 된다.
- mybatis.type-aliases-package를 지정하면 mapper 파일의 result Type에 클래스명만 적어도 오류가 발생하지 않는다.
    - 만약 mapper가 여러 개로 분산되어 있다면 , (컴마)를 사용해 패키지를 구분하면 된다.
    `ex) mybatis.type-aliases-package=com.example.mybatis.user, com.example.mybatis.team`

```properties
#MySQL config
spring.datasource.url=jdbc:mysql://localhost:3306/사용할 DB명?useUnicode=yes&characterEncoding=UTF-8&allowMultiQueries=true&serverTimezone=Asia/Seoul
spring.datasource.username=username
spring.datasource.password=password
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

#MyBatis
mybatis.mapper-locations=classpath:mapper/**/*.xml
mybatis.type-aliases-package=com.example.mybatis
mybatis.configuration.map-underscore-to-camel-case=true
```

<br/>

### MybatisApplication.java
- @MapperScan은 basePackages 이하 위치에 있는 인터페이스를 모두 mapper로 사용할 수 있도록 하는 어노테이션이다. 여기서 annotationClass 속성을 사용하면 특정 인터페이스만 사용할 수 있도록 한다.
    - 필자는 @Repository 어노테이션이 선언된 class만 mapper로 사용하도록 지정했다.

```java
@SpringBootApplication
@MapperScan(basePackages = "com.example.mybatis", annotationClass = Repository.class)
public class MybatisApplication {

	public static void main(String[] args) {
		SpringApplication.run(MybatisApplication.class, args);
	}

}
```

<br/>

### UserMapper.xml

- MyBatis Mapper XML
    - 실행할 SQL을 정의한 파일로 parameter Object (ex. UserDao)를 받아오거나 result Type (ex. UserDto)에 실행 결과를 자동 바인딩하는 기능을 제공한다.
- id와 메소드명이 다르면 에러가 발생하기 때문에 주의해야 한다.
- 상단의 <\?xml ...>과 <\!DOCTYPE ..>을 적지 않으면 mapping 되지 않으니 주의해야 한다.
- 해당 sql을 실행하면 user 테이블에서 id가 3인 행의 id와 name을 반환한다.

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.example.mybatis.user.repository.UserDao">
    <select id="getUser" resultType="UserDto">
        SELECT id as userId
             ,name as userName
        FROM
            user
        where id = 3
    </select>
</mapper>
```

> parameterType과 resultType에 wrapper 자료형을 사용할 수 있다. 다만, 필자는 resultType에 int나 Integer를 넣을 때는 반환되지 않다가 integer를 넣으니까 반환되는 것을 경험했다.
{: .prompt-tip }

<br/>

### UserDto.java

```java
@Getter
@Setter
public class UserDto {
    private Integer userId;
    private String userName;
}
```

<br/>

### UserController.java

- @RequiredArgsConstructor
    - lombok 어노테이션
    - 의존성 주입 Dependency Injection의 방식 중 하나인 생성자 주입 Constructor Injection 방식을 사용한다.
    - 임의의 코드 없이 **초기화가 되지 않은 final 필드**나 **@NonNull이 붙은 필드**에 대해 자동으로 **생성자 주입**을 설정해준다.

```java
@RestController
@RequiredArgsConstructor
public class UserController {

    private final UserService userService;

    @RequestMapping(value = "/user", method = RequestMethod.GET)
    public UserDto getUser(){
        return userService.getUser();
    }
}
```

<br/>

### UserService.java

- service의 인터페이스 `UserService`와 구현체 `UserServiceImpl`를 분리하면 구현체를 독립적으로 확장, 변경할 수 있다는 장점을 가지고 있다.
- 필수로 인터페이스를 만들어야 하는 것은 아니다.

```java
public interface UserService {
    UserDto getUser();
}
```

<br/>

### UserServiceImpl.java

```java
@Service("UserService")
@RequiredArgsConstructor
public class UserServiceImpl implements UserService{

    private final UserDao userDao;

    public UserDto getUser(){
        return userDao.getUser();
    };
}
```

<br/>

### UserDao.java

- mapper의 id와 동일한 메서드명을 사용해야 한다.

```java
@Repository("UserDao")
public interface UserDao {
    UserDto getUser();
}
```

<br/>

---

<br/>

# 참고

[마진 님 - 스프링 - 마이바티스 설정 (MySQL)](https://margin1103.tistory.com/31)

[도전하는 청년 님 - Mapper XML File 이란?](https://blog.naver.com/rla8860/220671456438)