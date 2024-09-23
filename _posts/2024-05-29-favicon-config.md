---
title: Favicon 설정
categories: [Blog]
tags: [github, blog, favicon]
---

# favicon이란?
![Desktop View](/assets/img/post_img/favicon/ex.png){:width="300" height="200"}
- 브라우저 주소창에 위치하는 아이콘으로, 웹사이트나 웹페이지를 대표함
- 특정 웹사이트나 웹페이지와 관련된 하나 이상의 작은 아이콘이 포함된 파일


---


# 설정 방법


1. favicon으로 설정하고 싶은 이미지를 결정해 저장한다.
    - 무료 이미지 사이트
        - [Pixabay](https://pixabay.com/ko/)
        - [Unsplash](https://unsplash.com/ko)
        - [Freepik](https://www.freepik.com/)


2. [Real Favicon Generator](https://realfavicongenerator.net/)에서 [select your Favicon image]를 클릭해 찾은 이미지를 넣어 favicon으로 만든다.

    ![Desktop View](/assets/img/post_img/favicon/select.png)


3. 로딩 후 Your master picture 팝업이 떴을 때 마음에 들면 [Continue with this picture]을, 마음에 들지 않으면 [Cancel and submit another picture]을 클릭한다.

    ![Desktop View](/assets/img/post_img/favicon/continue.png)


4. [Continue with this picture]을 누르면 내가 설정한 이미지가 favicon으로써 어떤 식으로 보이는지 확인할 수 있다.
    보고 괜찮은 것 같다 싶으면 하단의 [Generate your Favicons and HTML code] 버튼을 클릭한다.

    ![Desktop View](/assets/img/post_img/favicon/favicon.png)


5. 로딩 후 [Favicon package]를 클릭해 .zip 파일 다운 후 압축을 푼 뒤에 체크 표시 된 파일 `browserconfig.xml / site.webmanifest` 삭제한다.

    ![Desktop View](/assets/img/post_img/favicon/floder.png)


6. 이미지 파일을 assets/img/favicons 아래에 넣는다.
    > 주의점 : /favicons 폴더 아래에는 browserconfig.xml / site.webmanifest 파일이 존재함. 이 파일은 해당 테마 기본 설정 파일이기 때문에 삭제하면 안 됨

    ![Desktop View](/assets/img/post_img/favicon/favicons.png)


7. _includes/favicons.html 수정한다.

| ![Desktop View](/assets/img/post_img/favicon/rudfh.png) | ![Desktop View](/assets/img/post_img/favicon/vytl.png) |
| :----- | :--------- |
| 파일 구조 | 파일별 코드 위치 |


# 결과

![Desktop View](/assets/img/post_img/favicon/image.png){:width="300" height="200"}

적용 완료!


---


### 참고

- 사용한 테마 : jekyll chirpy