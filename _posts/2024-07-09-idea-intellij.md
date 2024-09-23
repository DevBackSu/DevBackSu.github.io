---
title: 코드 스니펫 Code Snippet
categories: [IDEA]
tags: [intellij, IntelliJ, code, snippets, code-snippets, 코드 스니펫]
---

# Code Snippet
code 와 snippet 의 합성어로, 작은 코드 조각 즉, 재사용 가능한 소스 코드 또는 텍스트의 작은 영역을 지칭하는 프로그래밍 용어다. 사용자의 반복 타이핑을 줄일 수 있게 돕는 역할을 한다.<br/>
대부분의 IDEA는 코드 스니펫을 지원하는 기능이 존재한다.<br/>

## IntelliJ에서 Code Snippet 만들기

우선 Live Template이란, 축약된 특정 값을 입력하면 해당 값에 정의한 코드 구성을 불러오는 기능을 말한다.<br/>
**Settings -> Live Templates**으로 이동해 + 버튼으로 새 Live Template을 생성한다.

![code](/assets/img/post_img/idea_img/live.png)

생성한 템플릿에 반복 작성하는 코드 블럭을 넣는다. 필자는 값 출력용 print 구문을 넣었다.

![code](/assets/img/post_img/idea_img/print.png)

구문을 넣은 후 하단을 보면 **No applicable contexts** 주의문 아래에 **Define** 을 확인할 수 있다.

![code](/assets/img/post_img/idea_img/define.png)

이를 눌러 어디에서 호출할 것인지를 정한다. 필자는 자바로 개발 중이기 때문에 JAVA 전체를 선택했다.

![code](/assets/img/post_img/idea_img/change.png)

적용을 완료한 뒤 테스트를 진행했다.

![test](/assets/img/post_img/idea_img/test.png)
![test](/assets/img/post_img/idea_img/test2.png)

축약값에 대한 코드가 잘 나오는 것을 확인했다.

## VSCode에서 Code Snippet 만들기

추후 업데이트

# 참고
[dayanne 님 - 코드 스니펫(Code Snipet)이란?](https://velog.io/@day_1226/%EC%BD%94%EB%93%9C-%EC%8A%A4%EB%8B%88%ED%8E%ABCode-Snipet%EC%9D%B4%EB%9E%80)