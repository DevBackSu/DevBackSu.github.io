1. 통신 관련

ping - ICMP 통신 (ICMP : network 계층인 3계층 프로토콜) -> port 불가능

telnet -  ssh 하위호환으로 원격 터미널 접속 기능이 있음. 7계층 프로토콜이라서 port 번호 사용 가능

ssh - 

curl - HTTP 외의 프로토콜도 사용 가능. 서버와 통신 가능한 커맨드 명령어 도구. 웹 브라우저처럼 터미널에서 요청을 보내고 응답을 받기 위해 사용됨. 구글로 쏴보니까 html 반환됨

이외의 방법

dev/tcp/[IP]/[PORT] - [설명](http://devmes.com/dev-tcp-seolmyeong-mic-sayongbangbeob/) / [설명2](https://medium.com/@stefanos.kalandaridis/bash-ing-your-network-f7069ab7c5f4)


각 예시 추가하기

----

2. 주석 관련
ibatis (mybatis) -> xml에 쿼리 적을 때 insert into 부분은 -- 써도 상관 없는데 값을 입력 받는 부분 #{ } (동적쿼리) 여기 앞에 -- 붙이면 오류 남
<!-- -->를 쓰는 것이 좋다.
아니면 의도하지 않은 문제가 발생할 수 있음

예를 들어, 아래는 회원 가입에 관한 내용임.

이거는 전부 -- 처리한 것. 값을 받지 않는 email과 friend_id도 값을 받으려고 함. 근데 주는 게 없으니까 ?로 뜸
t_usr ( usr_nm ,telno -- ,email -- ,friend_id ,cpl_yn ,send_yn ,del_yn ,rgr_dtm ) VALUES ( ? ,aes_enc(?) -- ,aes_enc(?) -- ,? ,'N' ,'N' ,'N' ,sysdate() ) 


-> 내 경우에는 정확인 어떤 Exception임 하고 뜨진 않았는데 sqlSessionUtils가 트랜젝션 sql session을 release하고 transaction 동기화를 등록 해제해버림

오류(?)문
15:38:05.914 [http-nio-80-exec-9] DEBUG org.mybatis.spring.SqlSessionUtils - Releasing transactional SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@4c6a4faa]
15:38:05.914 [http-nio-80-exec-9] DEBUG org.mybatis.spring.SqlSessionUtils - Transaction synchronization deregistering SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@4c6a4faa]
15:38:05.914 [http-nio-80-exec-9] DEBUG org.mybatis.spring.SqlSessionUtils - Transaction synchronization closing SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@4c6a4faa]
15:38:05.934 [http-nio-80-exec-9] DEBUG org.springframework.jdbc.datasource.DataSourceUtils - Returning JDBC Connection to DataSource
15:38:05.934 [http-nio-80-exec-9] INFO  jdbc.connection - 10. Connection closed  

-> 그리고 catch의 내용이 반환됨. 오류이긴 한 듯.



이거는 into 안은 -- 쓰고 values 안에는 <!-- -->로 처리함
t_usr ( usr_nm ,telno ,cpl_yn ,send_yn ,del_yn ,rgr_dtm ) VALUES ( ? ,aes_enc(?) ,'N' ,'N' ,'N' ,sysdate() ) 

-> 오류 없이 동작함

참고
https://ddururiiiiiii.tistory.com/382
https://velog.io/@jonghne/Mybatis-%EC%A3%BC%EC%84%9D%EC%9C%BC%EB%A1%9C-%EC%9D%B8%ED%95%9C-%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0-%EC%84%B8%ED%8C%85-%EC%98%A4%EB%A5%98