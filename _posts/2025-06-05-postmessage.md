---
title: 서로 다른 창 접근을 위한 postMessage()
categories: [Study, WEB]
tags: [study, web, 웹, postMessage, window, Web, post, message]
---

## [window.postMessage()](https://developer.mozilla.org/ko/docs/Web/API/Window/postMessage)

`window.postMessage()` 메소드는 window 오브젝트 사이에서 안전하게 교차 출처 (cross-origin) 통신을 할 수 있도록 지원한다.<br/>
우리가 웹사이트를 사용할 때, 서로 다른 페이지가 정보를 주고 받아야 할 때가 있다. 가장 흔히 보는 예시로, 어떤 사이트에서 로그인을 시도하면 ID와 PW를 입력하는 팝업 창이 뜨는 경우가 있다. 사용자가 정보를 입력한 후 로그인에 성공하면 팝업 창이 닫히면서 로그인 된 메인 페이지가 나타나게 된다. 이때, 즉, 팝업 창에서 로그인 인증 정보를 메인 페이지로 안전하게 전달할 때 postMessage()를 사용한다.<br/>
외에도 어떤 사이트 안에 유튜브나 구글 지도를 iframe으로 불러왔을 때 안과 바깥 페이지가 정보를 교환할 수 있도록 하려면 postMessage()가 필요하다.<br/>

### 교차 출처 cross-origin

웹은 기본적으로 동일 출처 정책이라는 보안 원칙을 가지고 있다. 그래서 다른 출처의 페이지는 기본적으로 iframe에 있든 팝업으로 열렸든 정보를 직접 읽거나 변경할 수 없다.

#### 출처 origin

우선 출처에 대해 알아보자. 출처는 **프로토콜** (https or http) / **도메인** / **포트 번호**로 이루어진다.<br/>
즉, `https://devbacksu.github.io:443`은 하나의 출처 origin이고,

- http://devbacksu.github.io (프로토콜이 다름)
- https://test.github.io (도메인이 다름)
- https://devbacksu.github.io:8080 (포트가 다름)

위 셋은 `https://devbacksu.github.io:443`과는 다른 출처(cross-origin)로 간주된다.<br/>
<br/>
https://devbacksu.github.io에서 다른 출처에 접근을 시도할 경우, CORS 오류가 발생하게 되는데, 이럴 때 window.postMessage()를 사용하면 안전하게 통신 할 수 있다.

### 문법

```js
targetWindow.postMessage(message, targetOrigin, [transfer]);
```

- **targetWindow** : 메시지를 전달 받을 window의 참조로, 메시지를 전달 받을 팝업 창이나 iframe 객체를 말한다.
- **message** : 다른 window에 보내질 데이터로, 문자열이나 숫자, 객체 등이 들어간다. 여기서 보낸 데이터 외의 것은 데이터를 전달 받는 창에서 접근할 수 없다.
- **targetOrigin** : 이 메시지를 받는 웹사이트 주소가 들어간다. 특정한 URI를 넣을 경우, 해당 URI 외의 도메인에서는 메시지를 받을 수 없다. 만약 *을 넣으면 어떤 출처든 targetWindow에서 전달한 메시지를 받을 수 있다.
  - targetOrigin을 *로 설정하면 모든 출처에서 메시지를 받을 수 있어 보안에 취약할 수 있다.
  - 보안 제한으로 사용할 수 없는 file:// 형태의 URL의 페이지는 메시지를 보내려면 * 로 해야 한다.

### 코드

아래의 예시는 자식 창에서 사용자가 어떤 작업을 완료하면, 부모 창으로 "전송 완료!" 메시지를 보내도록 한다.

#### 자식 창 (son.js)

```js
// 예: 사용자가 팝업 or iframe 내의 어떤 버튼을 클릭한 경우
document.querySelector('#etcBtn').addEventListener('click', function () {
    const isAgreed = true;

    console.log('[자식창] 사용자 버튼 클릭 여부 : ', isAgreed);

    // 부모 창에게 메시지를 보냄
    window.parent.postMessage({
        type: 'userButtonClick',  // message 부분 - key (자식 창에서 type으로 접근)
        agreed: isAgreed          // message 부분 - value (자식 창에서 agreed로 접근)
    }, 'https://example.com'      // targetOrigin 부분
    );
});
```

- window.parent.postMessage : 부모 창에 message를 보내겠다는 의미를 가진다.
- postMessage : 객체 형태의 메시지를 보낸다.
- targetOrigin : 정확한 출처를 지정해 보안을 강화한다.

#### 부모 창 (parents.js)

```js
window.addEventListener('message', function(event) {
    // 보안 검사: 메시지를 보낸 창의 출처가 신뢰할 수 있는지 확인
    if (event.origin !== 'https://trusted-child.com') return;

    if (event.data.type === 'userButtonClick') {
        console.log('[부모창] 사용자 버튼 클릭 상태 : ',
            event.data.agreed ? '동의함' : '동의 안 함'
        );

        if (event.data.agreed) {
            // 다음 단계로 이동하거나 버튼 활성화
            document.querySelector('.next-button').disabled = false;
        }
    }
});

// 아래와 같이 함수화도 가능하다.

function receiveMessage(event) {
    if (event.origin !== 'https://trusted-child.com') return;

    // 위와 동일
}

// 메시지 수신 등록
window.addEventListener('message', receiveMessage);
```

- 받은 메시지의 출처를 검사해서 보안을 유지한다. 만약 받은 event의 출처가 https://trusted-child.com이 아니면 아래 로직을 수행하지 않는다.
- 메시지 내용에 따라 특정한 로직을 수행한다.

## 결론

window.postMessage()는 서로 다른 출처 간에서도 정보를 주고 받을 수 있게 해주는 메서드이다. 특히, 팝업 창, 외부 위젯, iframe 등을 다룰 때 유용하게 사용된다. 하지만 이 기능은 보안 상 민감할 수 있기 때문에 아래의 사항을 기억해야 한다.

- targetOrigin은 정확한 출처를 지정하는 것을 지향한다.
- 메시지를 받는 쪽에서는 event.origin을 통해 출처 검증을 받는 것을 지향한다.
- 가능한 한 메시지 구조는 명확하게 정의한다.

## 회고

회사에서 업무를 하면서 자식 창에서 특정 기능이 완료된 시점을 부모 창이 파악할 수 있는 방법을 찾던 중 postMessage를 알게 되었다. 처음에는 단순히 다른 창에 데이터를 보내는 함수 정도로만 생각했는데, 실제로는 보안적인 요소도 중요하다는 것을 알게 되었다. 특히 해킹 시도에 민감한 서비스 환경에서는 보안이 더욱 철저해야 하기 때문에 출처 검증이 필수적임을 알게 되었다.