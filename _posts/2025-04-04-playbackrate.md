---
title: 비디오 속도 변경 및 제한하기
categories: [Study,WEB]
tags: [웹, WEB, web, JS, js, javascript, JavaScript, html, html, video, playbackRate, Object, defineProperty]
description: >-
    영상의 배속을 변경하거나 제한하는 방법을 알아본다.
---

## 영상 속도 변경하기

```js
document.querySelector('video').playbackRate = 16;
```

비디오 콘텐츠를 문서에 삽입할 때 사용하는 '**video**' 요소의 재생 속도를 최고 속도인 16으로 지정한다.


### [document.querySelector()](https://developer.mozilla.org/ko/docs/Web/API/Document/querySelector)

()의 선택자와 일치하는 문서 내 첫 번째 Element를 반환한다. 만약 일치하는 요소가 없다면 null을 반환한다.


### [playbackRate](https://developer.mozilla.org/ko/docs/Web/API/HTMLMediaElement/playbackRate)

HTMLMediaElement.playbackRate 속성은 미디어가 재생되는 속도를 설정한다.<br/>

|        양수         |     음수      |
| :-----------------: | :-----------: |
| 재생 속도 증가/감소 | 거꾸로 재생됨 |

기본 값은 1.0으로, 정상 속도를 의미하며 1.0보다 작으면 느리게, 1.0보다 크면 빠르게 재생된다.<br/>
비디오는 최대 16배속까지 가능하다.


## 영상 속도 제한하기

```js
const maxValue = 1;

document.addEventListener("DOMContentLoaded", function(){
    var od = Object.getOwnPropertyDescriptor(HTMLMediaElement.prototype, 'playbackRate');

    Object.defineProperty(HTMLVideoElement.prototype,'playbackRate',{
        set:function(value){
            if(value > maxValue) {
                console.log( maxValue + "보다 큰 배속은 허용하지 않습니다.")
                return;
            }
            od.set.call(this,value);
        },
        get:od.get
    });
})
```

HTMLVideoElement.prototype.playbackRate의 Setter를 오버라이드해서 playbackRate의 속도를 특정 값 이상으로 설정되는 것을 막는다.


### [document.addEventListener()](https://www.javascripttutorial.net/javascript-dom/javascript-domcontentloaded/)

HTML 파싱이 끝나 DOM이 완전히 로드된 후에 함수가 실행되도록 한다. DOM을 조작할 수 있다.

> DOM : 웹 브라우저가 html 페이지를 인식하는 방식 혹은 문서 객체와 관련된 객체의 집합 ([Aldev 님 - [JavaScript]DOM이란 무엇인가?](https://m.blog.naver.com/magnking/220972680805))
{: .prompt-info }


### Object.getOwnPropertyDescriptor(HTMLMediaElement.prototype, 'playbackRate');

[Object.getOwnPropertyDescriptor()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptor) 메서드는 주어진 객체에 대한 속성 설명자를 반환한다. 객체가 존재할 경우, 주어진 속성의 설명자가 반환되지만 없으면 undefined가 반환된다. <br/>

위 코드는 브라우저에 내장된 HTMLMediaElement 객체의 playbackRate 속성에 대한 속성 설명자를 가져와 od 변수에 저장한다. 아래는 od의 출력값이다.<br/>

![od](/assets/img/post_img/blog_img/palybackrate_return.png)

즉, HTMLMediaElement에서 playbackRate라는 속성이 어떤 get / set / 속성 설정으로 구성되어 있는지를 가져오는 것이다.


### [Object.defineProperty(HTMLVideoElement.prototype,'playbackRate',{ ... })](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)

HTMLVideoElement의 속성 중 playbackRate의 속성을 재정의하는 함수다.


### set ... get

만약 값을 변경하기 위해 콘솔에 [영상 속도 변경하기](#영상-속도-변경하기)의 코드를 입력했을 경우, 아래의 동작이 진행된다.

1. 사용자가 해당 코드 입력 시 브라우저는 내부적으로 playbackRate의 setter 함수를 호출한다.
2. if문에 의해 입력된 value가 maxValue보다 크면 console.log를 출력하고 return으로 아무 것도 주지 않는다.
3. od.set.call(this.value)를 통해 브라우저가 원래 제공하는 setter를 그대로 실행하여 기본값 (1.0)을 유지한다.
4. get에는 어떤 제한 조건이 존재하지 않기 때문에 playbackRate의 값을 읽을 때는 기존 getter를 그대로 사용하여 정상 동작을 유지한다.


## 왜 속성 설명자를 꺼낼 때는 HTMLMediaElement에서 꺼내고, 재정의할 때는 HTMLVideoElement에서 재정의할까?

우선, playbackRate는 HTMLMediaElement 라는 상의 클래스에서 정의되고, `<audio>`와 `<vidio>` 가 HTMLMediaElement를 상속 받는다. 따라서 원본 playbackRate의 속성 설명자는 공통 기반인 HTMLMediaElement에서 가져와야 한다.<br/>
반면, HTMLVideoElement는 video만 상속 받는 클래스이기 때문에 HTMLVideoElement의 playbackRate만 재정의 한다. 이로써 `<audio>` 태그는 원본의 playbackRate를, `<video>`는 재정의한 playbackRate를 가지게 되는 것이다.


## prototype을 사용하는 이유

![외에도 더 있음](/assets/img/post_img/blog_img/prototype.png)
_이미지 내 속성과 메서드 외에도 더 있다. 위 이미지는 HTMLMediaElement.prototype을 log로 찍은 결과다._

HTMLMediaElement.prototype에는 브라우저가 미리 정의한 것들이 들어 있다. 즉, `<video>`와 `<audio>`가 공유하는 공통 기능이 들어있기 때문에 `<video>`와 `<audio>`가 자동으로 기능을 사용할 수 있다..<br/>
ckarhfh HTMLVideoElement.prototype에는 `<video>`에만 있는 기능이 정의되어 있다.


---

## 참고

[알 수 없는 사용자 님 - HTMLMediaElement.playbackRate 조작이슈](https://programmingnote.tistory.com/82)