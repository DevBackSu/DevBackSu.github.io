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

![code](/assets/img/post_img/idea_img/vs_snippets.png)

vscode에서는 File -> Preferences -> Configure Snippets를 클릭한다.

![code](/assets/img/post_img/idea_img/global.png)

그러면 위와 같은 창이 뜨는데, 여기서 New Global Snippets file은 모든 파일에서 등록한 Snippets을 사용할 수 있는 코드 조각을 의미한다. 만약 특정 파일 안에서만 사용할 수 있는 스니펫을 만들고 싶다면 New Snippets file for ~ 을 클릭하거나 하단의 파일 형식을 클릭한다.

![code](/assets/img/post_img/idea_img/name.png)

스니펫을 등록할 파일을 선택했다면 스니펫 이름을 설정한다. 입력 후 엔터를 누르면 아래와 같이 (정의한 스니펫명).code-snippets 파일이 열리면서 등록한 스니핏 파일이 생성된다.

![code](/assets/img/post_img/idea_img/snippets.png)

스니펫 파일은 JSON 형식이라서 스니펫을 등록할 때는 JSON 형식에 따라 입력해야 한다.

```json
{
    "스니펫 이름" : {
        "scope" : "스니펫이 동작 가능한 언어",
        "prefix" : "스니펫을 호출할 때 사용되는 단축어",
        "body" : [
            "console.log('스니펫의 코드 조각')",
            "console.log('$1 or $2 : 탭 정지')",
            "console.log('$0 : 스니펫이 끝난 후 최종 커서의 위치를 지정함')",
            "console.log('스니펫 시작')",
            "console.log('스니펫 끝')"
        ],
        "description" : "스니펫 설명"
    }
}
```

위 예시를 바탕으로 실제 등록한 스니펫은 아래와 같다.

![code](/assets/img/post_img/idea_img/testsnippets.png)

.js 파일에서 테스트 한 결과는 아래와 같다.<br/>
scope에 등록한 확장자가 아닌 다른 확장자의 파일에서 prefix를 입력하면 스니펫이 뜨지 않는다.

![code](/assets/img/post_img/idea_img/testinsert.png)
![code](/assets/img/post_img/idea_img/testend.png)

$1과 $2, $0이 있는 부분에 커서가 멈춰있는 것을 확인할 수 있다.


# 참고
[dayanne 님 - 코드 스니펫(Code Snipet)이란?](https://velog.io/@day_1226/%EC%BD%94%EB%93%9C-%EC%8A%A4%EB%8B%88%ED%8E%ABCode-Snipet%EC%9D%B4%EB%9E%80)