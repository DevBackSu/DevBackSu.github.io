---
title: java.rmi.server.ExportException
categories: [Study, Server]
tags: [java, spring boot, server, error, port, ExportException, SocketException]
description: >-
    java.rmi.server.ExportException: Listen failed on port: (포트번호); nested exception is: java.net.SocketException: Bad address: listen failed
---

## 오류

최근 코드 수정 중 예기치 못한 오류가 발생했다.

```text
java.rmi.server.ExportException: Listen failed on port: (포트번호); nested exception is: java.net.SocketException: Bad address: listen failed
```

처음에는 코드 문제인가 싶었지만, 몇 번 종료 후 빌드하는 과정을 반복하니 정상적으로 실행됐다. 즉, 코드 자체의 문제는 아니었다.


## 의심되는 원인

문제 해결을 위해 검색을 해보니 아래의 원인이 자주 언급되었다.

- JDK 버전 불일치
  - 예를 들어, 프로젝트는 JDK8로 설정되어 있는데 톰캣은 JDK11 등 다른 버전으로 빌드되는 경우를 말한다.
  - 내 경우, JDK 설정은 오류가 발생하기 전부터 동일하게 유지되고 있기 때문에 해당되지 않는다.
- 이전 서버 인스턴스가 정상적으로 종료되지 않음
  - 이미 해당 포트를 점유하고 있는 프로세스가 있기 때문에 새로 실행하려는 서버가 바인딩에 실패하는 경우를 말한다.
  - 그래서 netstat -ano 명령어로 포트 상태를 확인해봤는데 해당 포트가 사용 중인 흔적이 없다.
  - 즉, 비어있는 포트인데도 JVM은 listen에 실패한 것이다.


## 예상 이유

1. JVM 내부 캐시나 소켓 상태가 꼬였을 경우
    - 검색을 타다가 마크 서버 관련 레딧을 보게 됐는데 거기서도 동일한 문제가 종종 발생한다고 했다. JAVA 업그레이드를 통해 해결했다는 사람도 있었지만 내 경우에는 적용하기 어렵기 때문에 시도하지 않았다.
    - 소켓이나 네트워크 문제라고 하는 사람도 있었는데 이건 마크 서버의 문제인지라 마찬가지로 적용할 수 없었다.
2. 방화벽 문제
   - 포트 접근을 제한하나 싶긴 했는데 그럼 재빌드 했을 때도 실행되는 경우가 없어야 했다.
3. 일시적인 프로토콜 스택 오류
   - 인터넷 프로토콜 스택 (응용 계층 / 전송 계층 / 인터넷 계층 / 너트워크 계층)의 일시적 오류로 인한 문제일까 싶긴한데.. 소켓 자원 누수면 포트가 열려 있어야 하는데 확인했을 때는 포트가 사용 중이지 않았다.
   - 프로세스가 비정상적으로 종료되었기 때문일까? 하지만 제대로 중지 버튼을 눌러서 종료한 이후에도 동일하게 오류가 발생했다.
   - 장비 오류이거나 용량 오류일 수도 있다고 생각하긴 한다. 용량이 작은 건 아닌데 뭔가 작음.


## 결론

이 문제는 코드의 문제가 아니라 시스템 환경이나 설정, JVM 상태에 따라 발생하는 오류로 판단된다.<br/>
빌드 -> 종료 -> 재실행을 반복하다보면 언젠간 되긴 하지만 이건 너무 번거롭고 근본적인 해결책은 아니기 때문에 아직 확인 중이다. 보다 정확한 해결법을 찾으면 글을 수정해야겠다.


## 2025.09.05
### io.netty.channel.AbstractChannel$AnnotatedSocketException: Bad address: connect: /127.0.0.1:(port)

비슷해보이는 오류가 발생했다. 이 오류는 127.0.0.1:(port)에 연결을 시도했지만 해당 포트에 서버가 없거나, 막혔거나, 포트 설정이 잘못된 경우에 발생한다고 한다.<br/>
이전에 했던 것처럼 cmd 창을 열어서 netstat -ano로 확인해보니 동일한 포트 번호가 LISTENING 상태였다.

![netstat_result](/assets/img/post_img/blog_etc/tomcat.png)

즉, 포트는 정상적으로 열려 있고, 연결도 되는 상태였다. 다만 자세한 해결법을 찾지 못해서 우선 이것저것 해보려고 한다.<br/>

1. 이미 사용 중인 포트이기 때문일 수 있으니 빌드 설정을 변경하려고 했다. 하지만 어디에도 오류가 발생한 포트를 사용하는 부분은 없었다. 다 기본 설정인데 왜 문제가 발생하는건지 모르겠다.
2. listen failed와 동일하게 중지 -> 재빌드 하면 정상적으로 실행되는 경우가 있었다. -> 중지하고 좀 기다렸다가 다시 시작하니 또 된다. 중지하는데 시간이 오래 걸리는 것 같다.