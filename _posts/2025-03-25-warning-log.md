---
title: Tomcat Log
categories: [Study,Tomcat]
tags: [log,tomcat,warning]
description: >-
    log 파일 종류와 확인한 Warning log
---

## 톰캣 폴더 구조

```
(필자의 톰캣 설치 경로)
- bin
    ㄴ (.sh / .bat / 일부 jar 파일이 위치함)
    ㄴ catalina.sh
    ㄴ tomcat-juli.jar
- conf
    ㄴ ....
    ㄴ logging.properties
    ㄴ server.xml
- lib
    ㄴ (라이브러리).jar
- logs
    ㄴ (로그 파일)
- temp
    ㄴ (톰캣이 실행되는 동안 임시 파일이 위치하는 임시 저장용 폴더)
- webapps
    ㄴ docs
    ㄴ examples
    ㄴ host-manager
    ㄴ manager
    ㄴ ROOT
- work
    ㄴ Catalina
        ㄴ (server.xml에 등록된 host)
            ㄴ docs
            ㄴ examples
            ㄴ host-manager
            ㄴ manager
            ㄴ ROOT
            ㄴ upload
- .bash_history
- daemon.sh
- LICENSE
- NOTICE
- RELEASE-NOTES
- RUNNING.txt
```

* webapps : 톰캣이 제공하는 웹 애플리케이션의 기본 위치이다.
  * docs : 톰캣에 대한 도움말을 사이트로 만든 것
  * examples : 톰캣 예제
  * host-manager : 톰캣 안에 각각의 웹 애플리케이션을 관리
  * manager : 톰캣 자체를 관리
  * ROOT : 웹 사이트 구조에 대한 톰캣이 제공하는 샘플
* work : jsp 파일을 서블릿 형태로 변환한 java 파일과 class 파일을 저장한다.
* 자세한 폴더 구조는 [Inpa Dev - [TOMCAT] 아파치 톰캣 개념 구성 & 설정 100 정리](https://inpa.tistory.com/m/18)에서 확인할 수 있다.



## 톰캣 로그 파일

- 톰캣은 로깅 서비스를 제공하는데, 이를 위해 사용하는 패키지가 **JULI** 이다.
- 기본 경로는 ${INSTANCE_DIR}/log/ 아래에 생성된다.
- 로그 설정 파일은 /conf/logging.properties 이며, 해당 파일을 통해 Tomcat 로그, 클래스 패키지 수준의 로그 구성을 처리한다.

|              파일명               | 설명                                                                                                                                                                                                                              | 비고                                      |
| :-------------------------------: | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------- |
|           catalina.out            | 톰캣의 표준 출력 / 표준 에러 출력을 기록하는 파일로, 톰캣 서버가 구동되면서 발생하는 모든 콘솔 출력을 기록한다. 별도의 설정을 하지 않으면 크기 제한 없이 무한히 커지며 날짜별 분리를 하지 않는다.                                 | 톰캣 서버 구동 중 발생한 로그만 기록      |
|       catalina.(년월일).log       | 톰캣 서버의 내부 로그 메세지를 기록한다. (주로 catalina 엔진의 로그를 포함한다.) 표준 출력 / 표준 에러 출력을 기록하지 않는다.                                                                                                    | 톰캣에서 발생한 로그만 기록               |
|       manager.(년월일).log        | Tomcat Manager Web App 로그를 기록한다. Tomcat-9.X.X 버전까지는 톰캣 실행 시 기본적으로 생성되었지만 Tomcat-10.X.X부터는 Manager Web App 로그가 발생하는 시점에서 로그 파일이 생성된다.                                           | 웹 콘솔                                   |
|     host-manager.(년월일).log     | Tomcat Host Manager Web App 로그를 기록한다.                                                                                                                                                                                      | 가상 호스트 매니저저                      |
|      localhost.(년월일).log       | Tomcat이 관리하는 호스트 (/conf/server.xml에 기록된 호스트) 중 특정 호스트에서 발생하는 로그 메세지를 기록한다.                                                                                                                   |                                           |
| localhost_access_log.(년월일).txt | Tomcat 서버의 접근 로그를 기록한다. 클라이언트의 IP 주소, 요청 시간, 요청 URL, HTTP 상태 코드, 응답 시간 등의 HTTP 요청 및 응답과 관련된 정보를 기록한다. 즉, 어느 호스트에서 언제, 어떤 방식으로 데이터를 요청했는지를 기록한다. | access 로그의 경우, AccessLogValve가 담당 |


## JULI

JAVA 1.4부터 Java.util.logging이라는 Java Logging Framework가 제공되었으나, 이 프레임워크는 JVM 레벨의 설정만 가능했다. 즉, 웹 애플리케이션 단위로는 로그를 남길 수 없었다. 따라서 톰캣은 이러한 단점을 해결하기 위해 Java Logging을 확장한 **JULI** 라는 이름의 로그 관리 패키지를 사용하고 있다. 해당 파일은 (톰캣 설치 경로)/bin/tomcat-juli.jar로 존재하고, 설정 방법은 Java Logging과 기본적으로 동일하다.

JULI는 Java Logging 설정 뿐 아니라 classloader 당 설정이 가능하다. 따라서 다음과 같이 logging.properties를 위치시킬 수 있다.

1. (톰캣 설치 경로)/conf/logging.properties
    - Java Logging에 대한 설정 파일이다. 이 경로는 Tomcat에 의해 java.util.logging.config.file이라는 JVM 시스템 속성으로 정의되어 있다. 만일 이 속성이 없다면 ${JAVA_HOME}/lib/logging.properties를 사용하게 된다.
2. WEB-INF/classes/logging.properties
    - 각 애플리케이션[^foot]의 설정 파일이다.

### access log

사용자 request 기록을 위한 access 로그는 (톰캣 설치 경로)/conf/server.xml 파일 내 org.apache.catalina.valves.AccessLogValve를 활성화함으로서 생성 가능하다. <br/>
Tomcat 배포 버전에 따라 활성화 여부가 다르다.

자세한 사항은 [sarc.io](https://sarc.io/index.php/tomcat/900-apache-tomcat-java-logging-juli)에서 확인할 수 있다.


<br/>

# Log

## org.apache.catalina.realm.LockOutRealm.filterLokedAccounts An attempt was made to authenticate the locked user "tomcat (or admin or root ...)"

catalina.out을 살펴보는데 위와 같은 WARNING 로그가 있었다.

### 원인 및 확인

bot에 의한 브루트 포스 해킹 시도로, /manager에 접근을 시도할 때 기록된다. 이에 대한 자세한 내용은 access.log에서 확인할 수 있다.

![access_log](/assets/img/post_img/blog_img/accesslog.png)

순서대로 **접속 IP**   - -   [**접속 시간**]   "**HTTP 요청 메소드**    **요청 URL**"    **HTTP 상태 코드**    **HTTP 헤더를 제외한 전송 크기** 를 나타낸다.

### 해결 방안

server.xml에서 IP를 차단하거나, Tomcat의 매니저 애플리케이션 설정을 비활성화 하면 된다.

#### 1. IP 차단

```server.xml
...
<Value ClassName = ... deny = "차단 IP, 차단 IP, ... ">
# deny 를 allow로 변경하면 해당 IP만 접근이 허용된다.
```

#### 2. 매니저 애플리케이션 설정 비활성화

필자의 사내 톰캣에는 이미 설정되어 있어서 직접 시도해보지 않았다. <br/>
자세한 사항은 [H'academy - Tomcat Manager 설정](https://docs.sysout.co.kr/web/back-end/apache-tomcat/tomcat-manager-extrenal-access)에서 확인할 수 있다.

<br/>
---
<br/>

[^foot]: server.xml에 등록한 각 Context를 말한다.