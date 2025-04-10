1. 통신 관련

ping - ICMP 통신 (ICMP : network 계층인 3계층 프로토콜) -> port 불가능

telnet -  ssh 하위호환으로 원격 터미널 접속 기능이 있음. 7계층 프로토콜이라서 port 번호 사용 가능

ssh - 

curl - HTTP 외의 프로토콜도 사용 가능. 서버와 통신 가능한 커맨드 명령어 도구. 웹 브라우저처럼 터미널에서 요청을 보내고 응답을 받기 위해 사용됨. 구글로 쏴보니까 html 반환됨

이외의 방법

dev/tcp/[IP]/[PORT] - [설명](http://devmes.com/dev-tcp-seolmyeong-mic-sayongbangbeob/) / [설명2](https://medium.com/@stefanos.kalandaridis/bash-ing-your-network-f7069ab7c5f4)


각 예시 추가하기

----------------
2. httpd.service

회사 서비스 중에 하나의 아파치 위에서 여러 개의 톰캣을 돌리는 경우가 있음.
갑자기 하나의 톰캣이 503 뜨면서 connection time out이 발생함.

그래서 톰캣을 재시작 했는데 여전함!

결국 아파치 재시작 함.

근데 아파치 재시작하는 명령어가 httpd.service인거임. 톰캣은 ./shutdown 이랑 ./startup으로 종료/시작을 하는데 왜 아파치는 httpd.service일까? 가 궁금해짐.

httpd : 아파치 하이퍼텍스트 전송 프로토콜 (HTTP) 서버 프로그램

아파치 -> 웹 서버 (html 등 정적 소스 처리) -> httpd 제공
톰캣 -> 웹 애플리케이션 서버 (java 등 동적 소스 처리)


참고
https://httpd.apache.org/docs/2.4/programs/httpd.html



-------------------

3. kworker

3번 처리 전, top 확인 -> kworker가 톰캣마다 도니까 여러 개 돌면서 cpu를 많이 잡아먹은 것으로 추정



4 root       0 -20       0      0      0 S   0.0  0.0   0:00.00 kworker/0:0H
5 root      20   0       0      0      0 S   0.0  0.0   1:35.17 kworker/u80:0
16 root       0 -20       0      0      0 S   0.0  0.0   0:00.00 kworker/1:0H
17 root      20   0       0      0      0 S   0.0  0.0   0:19.58 kworker/u81:0



그래서 kworker가 뭔지 궁금해졌다.

참고
https://askubuntu.com/questions/33640/kworker-what-is-it-and-why-is-it-hogging-so-much-cpu
https://austindhkim.tistory.com/43
https://community.frame.work/t/tracking-kworker-stuck-at-near-100-cpu-usage-with-ubuntu-22-04/23053


----------------------------------------------------------------------------------------------------------