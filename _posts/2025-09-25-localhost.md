---
title: 서버 실행 오류
categories: [Study, Server]
tags: [server, 서버, 실행, error, err, 오류, 공부]
description: >-
    실행을 왜 못하니
---

## 1. 실행은 되는데 접속이 안 될 때

비륻도 되고 실행도 되는데 막상 localhost 치고 들어가면 연결이 안 된다. `netstat -ano | findstr :1099`를 확인했을 때 아무런 값도 나오지 않는 것으로 보아 사용 중인 프로세스는 없을텐데 왜 안 뜨는지 모르겠다.
<br/>
-> 인텔리제이 캐시를 삭제하기 위해 캐시 삭제 및 종료로 껐다 키면 잘 된다. 문제는 이러다가 아래의 오류가 발생한다는 것이다.


## 2. JMX 커넥터 서버 통신 오류

```log
오류: JMX 커넥터 서버 통신 오류: service:jmx:rmi://DESKTOP-UD456JE
sun.management.AgentConfigurationError: java.rmi.server.ExportException: Listen failed on port: 0; nested exception is: 
	java.net.SocketException: Bad address: listen failed
Caused by: java.rmi.server.ExportException: Listen failed on port: 1099; nested exception is: 
	... 3 more
Caused by: java.net.SocketException: Bad address: listen failed
	... 12 more
```

1099 포트가 사용 중이어서 발생하는 오류다. 문제는 이 오류가 떠서 `netstat -ano | findstr :1099`로 확인을 시도했지만 아무런 조회 결과가 반환되지 않았다.
<br/>
-> 인텔리제이 캐시 삭제 및 재시작을 하면 잘 된다.
<br/>
-> 이 오류가 뜨고 나서 빌드를 종료했다가 (보통 알아서 종료된다.) 시간을 두고 다시 시작하면 되는 경우가 종종 있다.


## 확인

완전한 해결법은 아직 찾지 못한 것 같다. 뭐 보통 캐시 삭제를 하거나 재시작을 하면 잘 되곤 한다지만. 또 다른 유사 오류가 뜨면 업데이트를 해야겠다.