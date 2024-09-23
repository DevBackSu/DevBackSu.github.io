---
title: Tomcat Error
categories: [Study, Tomcat]
tags: [tomcat, 톰캣, 에러, error]
---

# Illegal access: this web application instance has been stopped already.

(2024.08.12)<br/>
톰캣 서버에서 발생하는 문제로, 서버가 중지된 후에도 여전히 실행 중인 스레드가 리소스에 접근하려 할 때 발생한다.<br/>
특히, MySQL JDBC 드라이버와 관련된 스레드가 서버가 중지된 후에도 계속 실행 중일 때 이러한 문제가 발생한다.<br/>
<br/>
필자의 경우, spring 프레임워크 + mybatis를 빌드한 파일을 서버에 올려서 서비스 중인데, 수정한 class 파일을 교체 (정확히는 이전 파일을 삭제하고 새 class 파일을 올렸다.) 한 후 서버를 재시작했다가 위 에러가 발생, 서버를 실행해도 404 ERR를 반환했다.
<br/>
추가로 찾아보니 패키지 이름에 `java`라는 문자열이 있어도 발생하는 듯 보인다.

# 해결

1. 처음에는 수정 전 파일로 복구한 후 서버를 재시작했다. 404 에러는 해결되었지만 메인 페이지 외의 페이지로 이동하는 건 되지 않았다.
2. 페이지별 이동 로직을 확인해보니 DB 조회를 한 후 페이지를 호출했다. 그래서 DB Tool을 사용해 DB에 접근을 시도했더니 10061 에러가 발생했다. DB가 죽었다고 판단하여 DB를 재시작했더니 무사히 복구되었다.

# 추측

이는 Illegal access가 발생하면서 DB를 비정상적인 상태로 만들어 멈추게 했기 때문인 것으로 추측된다. 어떤 글을 보면 해당 액세스로 인해 DB에 과부화가 발생하기도 한단다. <br/>
다만, 이 오류가 발생하게 된 원인은 아직 찾고 있다. sql.xml을 냅다 박아서 그런건지 너무 많은 파일을 한 번에 수정해서 그런건지.. 잘 모르겠다.



# Tomcat Web Application very likely to create a memory leak

(2024.08.13)<br/>
다시 로그를 확인해보니 위의 에러보다 먼저 발생한 에러는 memory leak이었다.<br/>
해당 에러는 Spring Application War 파일을 배포할 때, 웹 애플리케이션을 너무 많이 Update하거나 Reload한 것 때문에 발생한다고 한다.<br/>
Tomcat과 JVM은 웹 애플리케이션을 삭제하고 다시 생성할 때 모든 메모리를 해제하지 않기 때문에 웹 애플리케이션을 여러 번 Reload or Update하면 할당된 메모리가 바닥 나서 동작하지 않게 된다.<br/>
<br/>
사수님의 의견으로는 class 파일 중복으로 인한 이슈인 것 같다고도 하셨다.

# 해결

1. 위 에러와 동일하게 Tomcat을 재시작하면 된다.
2. 만약 재시작을 해도 되지 않으면 Tomcat이 할당할 수 있는 메모리를 늘려주어야 한다.

--

# 참고
[따봉도관절 님 - Tomcat Web Application very likely to create a memory leak](https://gcpower.tistory.com/entry/Tomcat-Web-Application-very-likely-to-create-a-memory-leak)<br/>
[김용환 님 - Tomcat 메모리력 문제 해결 (Tomcat 7.6.0.24부터)](https://knight76.tistory.com/947)<br/>
[리커니 님 - 톰캣 에러/경고 메지 관련](https://aljjabaegi.tistory.com/378)