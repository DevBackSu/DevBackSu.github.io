---
title: Post 작성하기 2
categories: [Blog]
tags: [study, github, post, blog]
description: >-
    텍스트, 이미지, 코드 등 내용을 꾸미는 방법 모음
math: true
mermaid: true
---

# 텍스트




# 코드

- ``` 뒤에는 아무것도 없어도 되고, 특정 언어여도 되고, 특정 파일이어도 된다.
  - 명시하지 않을 경우 : 기본값은 TEXT
  - 명시했을 경우 : 해당 언어나 파일에 맞게 하이라이팅 됨

## 명시하지 않았을 때

![code_lan](/assets/img/post_img/post_etc/text.png){: .normal width="200" height="136" }

↓

```
코드 블럭
code block
```

## 명시했을 때

### 언어 명시

![code](/assets/img/post_img/post_etc/java.png){: .normal width="200" height="136" }

↓

```java
@GetMapping("/")
public String home() {
    return "home";
}
```

### 파일명 명시

![code](/assets/img/post_img/post_etc/filename.png){: .normal width="200" height="136" }

↓

```_sass/main.bundles.scss
@import 'dist/bootstrap';
@import 'main';
```

## OPTION

### 코드 블럭의 Line 번호 없애기

`{: .nolineno}` 를 코드 블럭 하단에 넣는다.<br/>

![code](/assets/img/post_img/post_etc/line.png){: .normal width="200" height="136" }

↓

```text
첫번째 줄
두번째 줄
세번째 줄
```
{: .nolineno}


# 수식

웹 브라우저에서 수식을 작성헐 때는 [**Mathjax**](https://www.mathjax.org/)를 사용한다.<br/>
chirpy에서 Mathjax를 사용하려면 Front Matter에 `math: true`를 추가해주어야 한다.<br/>
<br/>

> Mathjax는 수식이 삽입된 문서를 아름답게 구성하는 LaTeX 방식으로 HTML 문서에 수식을 삽입할 수 있도록 구현한 JavaScript 라이브러리로, 아파치 라이선스에 따라 오픈 소스 소프트웨어로 제공된다.
{: .prompt-tip }

## 하나의 문단을 차지하는 수식 입력 방법

$$
\begin{equation}
  \sum_{n=1}^\infty 1/n^2 = \frac{\pi^2}{6}
  \label{eq:series}
\end{equation}
$$

위 수식은 아래의 코드를 통해 나타낼 수 있다.

```markdown
$$
\begin{equation}
  \sum_{n=1}^\infty 1/n^2 = \frac{\pi^2}{6}
  \label{eq:series}
\end{equation}
$$

```

## 문장 안에 수식 입력하는 방법

문장 안에 수식을 입력하면 다음과 같이 나타난다. $a \ne 0$ 일 때 $ax^2 + bx + c = 0$ 이다.<br/>
위 수식은 다음과 같다. ` $a \ne 0$ 일 때 $ax^2 + bx + c = 0$ 이다.`<br/>

## 수식 참조하는 방법

`\eqref{eq:series}`를 통해 위쪽의 문단형 수식을 참조할 수 있다. \eqref{eq:series}를 클릭하면 해당 수식 위치로 스크롤이 이동된다.<br/>
참조는 수식 코드 내 label의 {eq:(라벨)}와 \eqref의 {eq:(라벨)} 안의 내용이 동일한 곳으로 설정된다.

## 참고

[MATHJAX로 수식 입력하는 방법](https://sasamath.com/blog/tip-collection/how-to-write-equations-in-mathjax/)


# 다이어그램

Mermaid 툴을 사용하면 Markdown 문법을 이용하여 시각적인 다이어그램을 표현할 수 있다.

## 간트 차트

```markdown
\```mermaid
 gantt
  title  mermaid를 사용해 간트 차트 만들기
  UI/UX :a, 2024-07-19, 1w
  개발 :b, 2024-07-20, 1w
  회의 :crit, c, 2024-07-23, 1d
  테스트 :active, d, after c b a, 1d
\```
```

↓

```mermaid
 gantt
  title  mermaid를 사용해 간트 차트 만들기
  UI/UX :a, 2024-07-19, 1w
  개발 :b, 2024-07-20, 5d
  회의 :crit, c, 2024-07-23, 1d
  테스트 :active, d, after c b a, 1d
```

## 플로우차트

```markdown
\```mermaid
flowchart TD
  A[구매] --> B;
  B[사용자, 요청값, \n 이슈 확인] --> C;
  C{사용자가 구매함} -->|Yes| E;
  C -->|No| G;
  E[결제창으로 이동] --> G;
  G[DB 저장] --> I;
  I[응답 반환];
\```
```

↓

```mermaid
flowchart TD
  A[구매] --> B;
  B[사용자, 요청값, \n 이슈 확인] --> C;
  C{사용자가 구매함} -->|Yes| E;
  C -->|No| G;
  E[결제창으로 이동] --> G;
  G[DB 저장] --> I;
  I[응답 반환];
```

# 이미지 & 동영상

## 이미지

### 캡션

![test](/assets/img/favicons/favicon-16x16.png)
_테스트용 이미지의 캡션입니다._

```markdown
![img-description](/assets/img/img_path)
_Image Caption_
```
{: .nolineno}

위와 같이 이미지에 캡션을 넣고 싶으면 이미지 아래에 이탤릭체로 캡션 내용을 적으면 된다.


### 크기 설정

![test](/assets/img/favicons/favicon-16x16.png){: width="100" height="100"}

↓

```markdown
![test](/assets/img/favicons/favicon-16x16.png){: width="100" height="100"}
```
{: .nolineno}

{중괄호} 안에 width와 height를 넣어 크기를 지정할 수 있다. chirpy 버전(5.0.0 이상)에 따라 width -> w / height -> h로 사용이 가능하다.


### 배치 설정

|                        default                        |                       normal                        |                      left                       |                       right                       |
| :---------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------: | :-----------------------------------------------: |
| ![default](/assets/img/post_img/post_etc/default.png) | ![normal](/assets/img/post_img/post_etc/normal.png) | ![left](/assets/img/post_img/post_etc/left.png) | ![right](/assets/img/post_img/post_etc/right.png) |

↓

```markdown
// default
![default](/assets/img/path/test.png){: width="100" height="100"}

// normal
![normal](/assets/img/path/test.png){: .normal width="100" height="100"}

//left
![left](/assets/img/path/test.png){: .left width="100" height="100"}

//right
![right](/assets/img/path/test.png){: .right width="100" height="100"}
```

- default (옵션 작성 X) : 중앙 정렬
- normal : 왼쪽 정렬
- left : 텍스트와 양쪽 정렬이되, 이미지가 왼쪽에 위치함
- right : 텍스트와 양쪽 정렬이되, 이미지가 오른쪽에 위치함

### 다크 모드 / 화이트 모드

모드별로 다른 이미지가 나오도록 할 수 있다. 다크 모드 설정 시 왼쪽 이미지만, 화이트 모드 설정 시 오른쪽 이미지만 출력된다.

|                 Dark mode only                 |                  Light mode only                  |
| :--------------------------------------------: | :-----------------------------------------------: |
|               `![](..){: .dark}`               |                `![](..){: .light}`                |
| ![dark](/assets/img/main_img/man.png){: .dark} | ![light](/assets/img/main_img/cute.png){: .light} |




## 동영상

아래는 유투브의 영상을 첨부하는 방법이다. id값은 url 뒤쪽, v= 다음에 있는 문자열을 넣으면 된다.

```markdown
// url : https://www.youtube.com/watch?v=mQ8k4T5wVgE
{% include embed/youtube.html id='mQ8k4T5wVgE' %}
```

↓

{% include embed/youtube.html id='mQ8k4T5wVgE' %}