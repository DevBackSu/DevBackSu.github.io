---
title: Spring Boot + Gradle + jsp로 웹페이지 띄우기
categories: [Study, Spring boot]
tags: [Spring Boot, gradle, jsp, jstl, web, jasper]
---

# 주요 SKILL
build tool : Gradle - groovy

language : java 22

framework : spring boot 3.2.6

<br/>

# JSTL

- 자바 서버 페이지 표준 태그 라이브러리로 JAVA EE 기반의 웹 애플리케이션 개발 플랫폼을 위한 컴포넌트 모음이다.

<br/>

# jasper

- Tomcat의 JSP 엔진 중 하나로, JSP를 서블릿으로 변환시킨다.

<br/>

# Setting

## Project Structure

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
    ㄴ webapp
        ㄴ WEB-INF
            ㄴ views
                ㄴ admin // 관리자 웹 페이지
                ㄴ user  // 사용자 웹 페이지
{% endhighlight %}

<br/>

## build.gradle

- spring 3.0.0 이상 버전을 사용할 경우 아래의 JSTL 의존성을 추가해야 한다.

```gradle
implementation 'org.apache.tomcat.embed:tomcat-embed-jasper'
implementation 'jakarta.servlet:jakarta.servlet-api'
implementation 'jakarta.servlet.jsp.jstl:jakarta.servlet.jsp.jstl-api'
implementation 'org.glassfish.web:jakarta.servlet.jsp.jstl'
```

- spring 3.0.0 이하 버전을 사용한다면 아래의 JSTL 의존성을 추가한다.

```gradle
implementation 'org.apache.tomcat.embed:tomcat-embed-jasper'
implementation 'javax.servlet:jstl'
```

<br/>

## application.properties

- webapp/WEB-INF/views 내 jsp 파일을 사용하기 위해 View Resolver 경로를 변경한다.
    - prefix는 접두어로, 객체에서 선언된 view page의 위치를 의미한다.
    - suffix는 접미어로 view page의 확장자를 의미한다.

```properties
spring.mvc.view.prefix=/WEB-INF/views/
spring.mvc.view.suffix=.jsp
```

## UserController.java

@RestController는 Restful 웹 서비스 컨트롤러로, @Controller에 @ResponseBody가 추가된 것이다. @RestController는 Json 형태로 객체 데이터를 반환하기 때문에 단순 api 개발 시에는 @RestController를 사용하는 것이 좋다.

아래와 같이 @RestController를 사용할 경우, ModelAndView 객체를 생성해 객체 형태로 반환하거나 Model 방식을 통해 String 형태로 반환하는 방식을 사용해야 한다.

```java
@RestController
public class UserController {
    @GetMapping("/")
    public String login(){
        return "user/login";
    }
}
```

위 방식의 결과는 아래와 같다.

![result](/assets/img/post_img/war_jsp/restP.png)

<br/>

```java
@RestController
public class UserController {

    @GetMapping("/")
    public ModelAndView login(){
        return new ModelAndView("user/login");
    }
}
```

위 방식의 결과는 아래와 같다.

![result](/assets/img/post_img/war_jsp/restc.png)

<br/>

@Controller는 Model 객체를 만들어 데이터를 담고 View를 반환하기 위해 사용하는 Spring MVC 컨트롤러이다. @Controller 어노테이션을 사용할 경우, ModelAndView를 사용하여 view를 직접 반환하는 방식은 최근 잘 사용하지 않기 때문에 추천하지 않는다.

```java
@Controller
public class UserController {
    @GetMapping("/")
    public String login(){
        return "user/login";
    }

    // 아래 방식도 동일한 결과를 반환함
    // @GetMapping("/")
    // public ModelAndView login(){
    //     return new ModelAndView("user/login");
    // }

    /**
     * 아래처럼 view가 아닌 값을 반환하면 Whitelabel Error Page 가 발생한다.
     * @RequestMapping(value = "/getuser", method = RequestMethod.GET)
        public UsrVO getUser(){
            return usrService.getUser();
        }
     * /
}
```

위 방식의 결과는 아래와 같다.

![result](/assets/img/post_img/war_jsp/controller.png)


<br/>

## JSP

### 또 실수할까봐 적어놓는 오류

#### ReferenceError 오류

```jsp
<script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.7/jquery.js">
    ...html content...
</script>
```

- 처음에는 이런 식으로 작성해서 **ReferenceError: (사용자 정의 함수) is not defined**가 발생했다.

#### 해결

- `<script>` 태그는 외부 라이브러리와 사용자 정의 스크립트로 구분된다.

```jsp
<%-- 외부 라이브러리 로드 부분 --%>
<script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.7/jquery.js"></script>

<%-- 사용자 정의 스크립트 부분 --%>
<script type="text/javascript">
    html content...
</script>
```

- 이 두 script tag는 모듈화와 유지보수성을 보장하고 브라우저가 외부 스크립트를 먼저 로드한 후 사용자 정의 스크립트를 실행하도록 하기 위해 분리되어야 한다.

<br/>

#### $(...).valid is not a function

- .valid 함수가 존재하지 않는다는 의미를 가지며, 해당 함수를 포함한 플러그인을 추가해야 한다.
- 버전 충돌이 일어날 수 있으니 주의해야 한다.
<br/>
- 아래는 버전 충돌이 발생했던 플러그인이다. 검색한 내용을 마구잡이로 복붙하다보니 두 가지 버전의 jQuery가 포함되었다.

```jsp
<script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.7/jquery.js"></script>
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
```

- 아래의 플러그인으로 변경하였더니 에러가 발생하지 않았다.

```jsp
<script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.7/jquery.js"></script>
<script src="https://cdn.jsdelivr.net/jquery.validation/1.19.3/jquery.validate.min.js"></script>
```

- 특히 jquery.validate.min.js 라이브러리가 없으면 .valid() 사용 시 오류가 발생한다.


#### $(...).ajaxSubmit is not a function

- 위와 동일한 문제이나, 다른 플러그인이 필요하다.
- 아래는 오류를 해결하기 위해 추가한 플러그인이다.

```jsp
<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery.form/4.3.0/jquery.form.min.js"></script>
```

결과적으로, 포함한 외부 플러그인은 아래와 같다.

```jsp
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery.form/4.3.0/jquery.form.min.js"></script>
<script src="https://unpkg.com/jquery-validation@1.19.3/dist/jquery.validate.min.js"></script>
```

##### not a function 참고
[OKKY - ajaxSubmit is not a function](https://okky.kr/questions/617180)<br/>
[stackoverflow - $("#form1").validate is not a function](https://stackoverflow.com/questions/1871424/form1-validate-is-not-a-function)<br/>
[$.ajax / $.ajaxForm / $.ajaxSubmit is not a function](https://forthenextstep.tistory.com/227)<br/>

---

<br/>

# 참고

[개소왕 님 - Spring boot jsp 못 읽는 경우](https://dogcowking.tistory.com/326)

[yusub 님 - [Spring] Spring boot 3에서 jsp 설정방법](https://velog.io/@rhkdbtj/Spring-Spring-boot-3%EC%97%90%EC%84%9C-jsp-%EC%84%A4%EC%A0%95%EB%B0%A9%EB%B2%95)

[우롱차 님 - JSP를 사용하는 Spring Boot 프로젝트 생성하기](https://velog.io/@wooryung/JSP%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-Spring-Boot-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EC%83%9D%EC%84%B1%ED%95%98%EA%B8%B0)

[잇트루 님 - [Spring MVC] 스프링 MVC 뷰 리졸버 (View Resolver)](https://ittrue.tistory.com/237)

[무작정 개발 님 - [Spring] Model, ModelAndView 차이점 (feat.ModelAndView를 지양하자)](https://backendcode.tistory.com/253)

[망나니개발자 님 - [Spring] @Controller와 @RestController 차이](https://mangkyu.tistory.com/49)