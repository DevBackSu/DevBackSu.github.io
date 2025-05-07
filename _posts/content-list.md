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


4. postMessage 방식

자식창에서 부모 창에 필요한 데이터를 전달 -> 부모 창에서 데이터를 받아 처리하는 방식 (자식 창과 부모 창의 도메인이 다를 때 사용)

son.js
// 슬라이드 설정
const mySlider = new Swiper('.slider', {
    ...
});

mySlider.on('slideChange', function () {  // 슬라이드 변경 시 아래 함수 실행
  const nextBtn = document.querySelector('.swiper-button-next');
  const isDisabled = nextBtn && nextBtn.tabIndex === -1;
// 마지막 페이지 (이후에 페이지가 없을 때) tabIndex === -1인 것을 파악, 해당 값으로 마지막 페이지인지 확인 (마지막 페이지면 -1을 isDisabled에 담아 반환)

  console.log('[팝업창] 마지막 페이지 이동 여부 : ', isDisabled);

  window.parent.postMessage({ // 자식 창에서 부모 창에 message 보내기
    type: 'nextButtonStatus', // key (type) : value (nextButtonStatus)
    disabled: isDisabled      // key (disabled) : value (isDisabled)
  }, 'https://test.com'); // * -> 모든 도메인에서 허용 (보안 이슈 발생 가능) / 특정 도메인 입력 시 해당 도메인에만 message를 보냄 (아니면 에러 발생)
});


parents.js

...

window.addEventListener('message', function(event) {
        if (event.origin !== 'https://slider.com') return; // 보안용 (호출 받은 이벤트의 도메인이 slider면 return함)
        
        if (event.data.type === 'nextButtonStatus') {
            console.log('[부모창] 다음 버튼 상태:', event.data.disabled ? '비활성화됨' : '활성화됨');
        }
        if (event.data.disabled) {
            ...
        }
    });

....

--------------------------------------


5. spring boot + jpa + pagination
페이지네이션 (spring boot jpa랑 mybatis 모두 적용 가능)
데이터를 정렬 기준, 페이지 크기, 몇 번째 페이지인지를 토대로 정보를 전달함
직접 구현도 가능하고 Spring에서 제공하는 pageable을 사용할 수 있음

Pageable 클래스랑
Page 클래스가 있음


참고 : https://velog.io/@soluinoon/Spring-Pageable-%ED%8C%8C%ED%97%A4%EC%B9%98%EA%B8%B0
참고 : https://velog.io/@albaneo0724/Spring-Pagination%EA%B3%BC-Page-%EA%B7%B8%EB%A6%AC%EA%B3%A0-Pageable

커서 기반 페이지네이션 : https://velog.io/@znftm97/%EC%BB%A4%EC%84%9C-%EA%B8%B0%EB%B0%98-%ED%8E%98%EC%9D%B4%EC%A7%80%EB%84%A4%EC%9D%B4%EC%85%98Cursor-based-Pagination%EC%9D%B4%EB%9E%80-Querydsl%EB%A1%9C-%EA%B5%AC%ED%98%84%EA%B9%8C%EC%A7%80-so3v8mi2
외에는 오프셋 기반 페이지네이션이 있음


-------------------------------------

6. MockMvc 테스트 -> 단위 테스트용

묵 테스트를 이용하면 json 형식의 api response를 검증할 수 있음 (컨트롤러 동작 테스트)
이때 사용하는 게 jsonpath()

perform() : 요청을 전송하는 역할
andExcpect() : 리턴값 검증 및 확인
get : HTTP 메소드 결정 (post / put / delete 등). 인자로는 경로를 보냄
params : 키:값 파라미터를 전달할 수 있음. 여러 개일때는 params() / 하나일 때는 param()


참고 : https://0soo.tistory.com/190   /   https://adjh54.tistory.com/347


----------------------------------------


7. TOOL

Shadcn UI : Vercel의 shadcn이 만든 UI 도구. (https://ui.shadcn.com/)

twine : 웹에서 제공하는 오픈 소스 제작 툴. 간단한 명령어로 하이퍼텍스트 html 파일을 만들 수 있다. => 텍스트 게임을 만들 수 있는 툴이다. (https://twinery.org/)