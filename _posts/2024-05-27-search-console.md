---
title: Search Console
categories: [Blog]
tags: [search console, google, daum, naver, github, blog]
description: >-
    Google의 search console, Naver의 search advisor, Daum의 검색 등록
image:
    path : /assets/img/post_img/searchconsole/google_logo.png
    alt : Google Search Console LOGO
---

> 계정명 변경 (zjarhdsumin -> devbacksu)
{: .prompt-info }

# Google Search Console
구글 서치 콘솔

- Google에서 무료로 제공하는 서비스
- 웹 마스터 도구
- 사용자가 사이트의 구글 검색 결과 인지도를 모니터링하고 관리하며 문제를 해결하도록 도움
- 구글 검색을 통해 조회가 가능하도록 도움

<br/>

# Daum 검색 등록
- 무료로 등록 가능
- 사이트 등록/변경/삭제 -> Daum 검색등록 가이드에 따라 검토
-  검색 노출을 보장 X
- 처리 결과를 이메일로 발송
- 등록 이후 검색 노출 -> 최대 5일 소요

<br/>

# Naver Search Advisor
- Naver에서 무료로 제공하는 웹 마스터 도구
- 운영하는 사이트의 검색 반영 및 제외를 위해 검색 반영 현황을 모니터링하고 관리할 수 있도록 제공하는 서비스

<br/>

---
<br/>

## Github 블로그를 Google Search Console에 등록하는 방법


1. [Google Search Console](https://search.google.com/search-console/about?hl=ko){: target="_blank"}에서 [시작하기] 버튼 클릭

    ![Desktop View](/assets/img/post_img/searchconsole/start.png){:width="500" height="450"}
    <br/>

2. 만약 로그인이 되어 있지 않은 상태라면 로그인을 하고 이후 search console에 등록하고 싶은 url을 입력한다.
    나는 내 github 블로그 `https://zjarhdsumin.github.io` 를 등록하고 싶기 때문에 다음과 같이 작성했다.

    ![Desktop View](/assets/img/post_img/searchconsole/domain.png){:width="500" height="600"}
    <br/>


3. 구글에서 소유권을 확인하기 위해 제공하는 html 파일 다운로드 한다.

    ![Desktop View](/assets/img/post_img/searchconsole/html_file.png){:width="500" height="600"}
    <br/>



4. 다운로드 한 html 파일을 아래의 경로에 넣은 후 git add -> git commit -> git push

    <!-- ![Desktop View](/assets/img/post_img/searchconsole/file_st.png){:width="300" height="600" .left} -->
    <img src="/assets/img/post_img/searchconsole/file_st.png" width="300px" height="600px">
    > html 파일을 통해 소유권을 검사하기 때문에 삭제 X
    <br/>



5. github에서 push 완료를 확인 한 후 위 `(3번)` 이미지의 확인 버튼을 누르면 아래의 팝업이 뜬다.

    ![Desktop View](/assets/img/post_img/searchconsole/check_html.png){:width="600" height="500"}

    **[속성으로 이동]** 버튼 클릭 시 아래의 화면으로 이동된다.

    ![Desktop View](/assets/img/post_img/searchconsole/searchconsole_main.png){:width="700" height="700"}
    <br/>



6. gemfile 수정 후 ruby prompt에서 bundle install과 jekyll serve을 순차적으로 입력한다.
    (참고한 블로그에서는 jekyll-sitemap 추가 후 install 했을 때 로그에 추가한 내용이 찍히는데 필자는 찍히지 않았다. 이미 다운된 상태였기 때문인 것으로 추측하고 있다.)

    ![Desktop View](/assets/img/post_img/searchconsole/gemfile_sitemap.png){:width="400" height="400"}
    <br/>



7. 이후 http://localhost:4000/sitemap.xml 에 접속하면 sitemap.xml 파일 내용 확인할 수 있다. 이 xml 파일을 복사해서 Gemfile의 위치에 동일한 이름의 파일을 생성한 후 붙여 넣기 한 후 같은 위치에 robots.txt[^footnote]를 넣어야 한다. (내 경우, robots.txt가 assets 폴더 내에 있어서 아래의 경로로 이동시켰다.)

    | ![Desktop View](/assets/img/post_img/searchconsole/sitemap.png) | ![Desktop View](/assets/img/post_img/searchconsole/robots.png) |
    | :-------------------------------------------------------------- | :------------------------------------------------------------- |
    | sitemap.xml과 robots.txt 위치                                   | robots.txt 내용                                                |


    | robots.txt 변수명 | 설명                                                                                                                                                                                |
    | :---------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
    | User-agent        | robots.txt에서 지정하는 크롤링 규칙이 적용되어야 할 크롤러를 지정한다. *은 모든 검색 엔진 로봇의 접근 허용을 뜻한다. (크롤러명 : 구글 (Googlebot) / 네이버 (Yeti) / 다음 (Daum) 등) |
    | Disallow          | 크롤링을 제한할 경로                                                                                                                                                                |
    | Allow             | 크롤링을 허용하는 경로                                                                                                                                                              |
    | Sitemap           | sitemap 경로                                                                                                                                                                        |
    
    <br/>



8. 7번의 수정 내용을 github에 올린 `(add -> commit -> push)` 후 Google Search Console의 sitemap 으로 들어가 사이트맵 `sitemap 파일명` 을 추가한다.

    ![Desktop View](/assets/img/post_img/searchconsole/plus_site.png){:width="800" height="400"}
    <br/>



9. git push가 잘 됐다면 제출 버튼 클릭 시 아래의 팝업이 뜨고 제출된 사이트맵 목록도 확인이 가능하다. 위 설정 완료 시 7일 이내에 검색 엔진에 노출된다.

    ![Desktop View](/assets/img/post_img/searchconsole/success_sitemap.png){:width="800" height="500"}
    ![Desktop View](/assets/img/post_img/searchconsole/sitemap_list.png){:width="800" height="400"}
    <br/>



10. 기본적으로 sitemap만 등록하면 검색이 되지만 아닌 경우도 있다. 만약 검색이 되지 않는다면 다음의 방법을 시도해보아야 한다. 우선 URL검색에서 본인의 블로그 주소 (필자는 https://devbacksu.github.io 를 입력했다.)를 검색한다. 이후 **색인 생성 요청** 을 클릭해 직접 색인을 요청한다.
    
    ![url](/assets/img/post_img/searchconsole/url.png)

    테스트가 끝나고 색인 생성 요청 요청이 완료되면 아래의 창이 뜬다.

    ![url](/assets/img/post_img/searchconsole/url2.png)

    닫기를 누르면 색인이 요청된 상태로만 뜨다가 10분 정도 기다리면 색인 등록이 완료된다.

    ![url](/assets/img/post_img/searchconsole/url3.png)
    ![url](/assets/img/post_img/searchconsole/url4.png)
    <br/>


## 속성 삭제
삭제하고 싶은 속성을 선택한 후 아래 이미지를 따라 버튼 클릭 시 속성을 삭제할 수 있다.
![Desktop View](/assets/img/post_img/searchconsole/delete.png)
 ![Desktop View](/assets/img/post_img/searchconsole/delete2.png)

<br/>
<br/>

# Github Blog를 Daum 검색 등록에 등록하는 방법
1. [Daum 검색 등록](https://register.search.daum.net/searchForm.daum?act=insert) 사이트에 접근해 등록할 방식 (사이트 등록 / 지역 정보 / 블로그 등록)을 선택해 내 사이트 주소를 입력한다.
    ![daum](/assets/img/post_img/searchconsole/daum_1.png)
    <br/>
2. 개인 정보 수집 및 이용에 동의한다.
    ![daum](/assets/img/post_img/searchconsole/daum_2.png)
    <br/>
3. 사이트 / 블로그 / 지역 정보가 등록되거나 실패했을 때 알림을 받기 위한 메일 주소를 등록한다.
    ![daum](/assets/img/post_img/searchconsole/daum_3.png)
    <br/>
4. 등록 신청 성공 페이지가 뜨면 신청이 성공한 것이다.
    ![daum](/assets/img/post_img/searchconsole/daum_fin.png)

<br/>
<br/>

# Github Blog를 Naver Search Advisor에 등록하는 방법
1. [네이버 서치어드바이저](https://searchadvisor.naver.com/)에 접근한다.
    ![naver](/assets/img/post_img/searchconsole/naver1.png)
    <br/>
2. 등록할 사이트의 주소를 입력하고 가장 오른쪽 버튼을 클릭한다.
    ![naver](/assets/img/post_img/searchconsole/naver2.png)
    <br/>
3. Google처럼 소유권 확인을 위한 html 파일을 다운 받아 루트 디렉터리에 넣는다.
    - 파일은 형광펜으로 줄을 친 `HTML 확인 파일`을 클릭하면 다운 받을 수 있다.
    - 파일을 / 디렉터리에 넣은 후 git add -> commit -> push를 진행 한 후 아래쪽 업로드 확인을 위한 링크를 클릭하면 다음과 같은 페이지가 뜨게 된다.
    ![naver](/assets/img/post_img/searchconsole/naver5.png)
    <br/>
4. 이후 소유권 확인 버튼을 누르면 자동 등록 방지를 위한 보안 절차를 진행한 후 아래와 같은 팝업이 뜬다.
    ![naver](/assets/img/post_img/searchconsole/naver4.png)
    <br/>
5. 확인 후 다음과 같은 페이지가 나오면 등록을 완료한 것이다.
    ![naver](/assets/img/post_img/searchconsole/naver6.png)
    <br/>
6. 등록이 완료되었으면 [간단 체크] 탭으로 넘어와 검색 최적화 정보를 확인한다. 확인할 url을 입력 후 돋보기 아이콘을 클릭하면 자동 등록 방지를 위한 보안 절차를 진행한다.
    ![naver](/assets/img/post_img/searchconsole/naver7.png)
    <br/>
7. 다음처럼 모든 항목에 체크 표시가 되어 있으면 문제가 없다.
    ![naver](/assets/img/post_img/searchconsole/naver8.png)
    <br/>
8. Search Console처럼 sitemap을 등록한다.
    ![url](/assets/img/post_img/searchconsole/naverurl.png)
    <br/>
9. 웹 페이지 수집 요청도 했다.
    ![url](/assets/img/post_img/searchconsole/naverurl2.png)
    <br/>

---



# Err List

## 24.05.24 Problem
### Google Search Console
<figure class="half"> 
    <a href="link"><img src="/assets/img/post_img/searchconsole/sitemap_err.png"></a> 
    <a href="link"><img src="/assets/img/post_img/searchconsole/err_content.png"></a> 
</figure>


서치 콘솔 설정 완료 후 일주일이 지난 어느 날 (24일), 서치 콘솔을 확인하니 위 사진의 오류가 떠 있었다.



## 시도한 방법

1. sitemap 검색해보기
    - https://(내 블로그 주소)/sitemap.xml을 검색했을 때 xml 내용이 나오는지 확인

    ![](/assets/img/post_img/searchconsole/sitemap_xml.png){: w="400" h="400"}

    -> 내용을 확인할 수 있지만 여전히 실패 상태
    <br/>



2. 사이트맵 삭제 후 다시 추가하기
    - 사이트맵 삭제

    ![](/assets/img/post_img/searchconsole/sitemap_delete.png)

    - 사이트맵 추가

    ![](/assets/img/post_img/searchconsole/sitemap_plus.png)

    -> 여전히 실패 상태
    <br/>



3. 서버 사용 불가 같은 기타 Search Console 오류
    - Github의 경우 사이트맵 인식 관련 문제가 있다고 한다.
    - 2~3개월 후에 정상 인식이 되기는 한다고 하는 글도 있었다.
    
    -> 모든 방법이 먹지 않으면 일단 대기를 해야 할 것 같다.
    <br/>



4. robots.txt 문제
    - [Search Console 고객센터](https://support.google.com/webmasters/answer/7451001#errors){:target="_blank"} 확인하기
    - 대체로 Allow와 Disallow를 지우거나, Disallow의 경로만 지워도 해결되는 듯
    <br/>



5. 사이트맵에 대한 크롤링 수요가 낮음
    - 높은 품질의 컨텐츠를 제작해 수요를 높여야 한단다.
    <br/>

<br/>


## 해결 방법

#### Sitemap 수정

[sitemaps](https://www.xml-sitemaps.com/)에서 내 블로그 주소를 입력한 후 start 버튼 클릭 시 내 블로그의 sitemap.xml을 생성해준다.

![](/assets/img/post_img/searchconsole/xml_sitemaps.png)

생성한 sitemap.xml 파일을 기존 sitemap.xml 파일이 있던 곳에 넣어 덮어쓴다.

![](/assets/img/post_img/searchconsole/final_sitemap.png)
<br/>



#### IF

| ![](/assets/img/post_img/searchconsole/content_err.png) | ![](/assets/img/post_img/searchconsole/page_err.png) |
| :------------------------------------------------------ | :--------------------------------------------------- |
| search console 오류                                     | 브라우저에서 sitemap.xml 검색 시 오류                |

만약 모두 시도한 이후에도 위와 같은 에러가 발생할 경우

- 새로 다운 받은 sitemap의 `<urlset>` 부분에 이전 sitemap의 `<urlset>`을 추가 (같은 내용은 삭제)
- sitemap 내 주석 및 엔터 부분 모두 삭제
- 수정 후 git push 했는지 확인하기

> 내 경우, 주석을 지운 후 등록한 sitemap이 오류를 발생시키지 않았다. 하지만 브라우저에서 sitemap을 검색했을 때는 내가 지웠던 주석이 그대로 남아있던 것을 보아 아무래도 google은 무언가를 등록/수정/삭제한 이후에는 구글이 내 변경 사항을 적용할 때까지 기다려야 할 것 같다.

> 240529 - sitemap 검색 시 주석이 삭제되었다! 추측 상 `<urlset>`을 수정한 후부터 오류가 사라진 것 같다.

> 240604 - push와는 상관 없이 수정하면 search console에 sitemap을 직접 등록해야 하는 것 같다. 초반에는 push랑 연동인가? 하고 push만 했는데 변경 사항이 적용되지 않았다.

<br/>

## 24.06.10
### Naver Search Advisor
1. 검색 최적화 조회를 했을 때 robots.txt 항목에 X 표시가 있었다.
    - 내 robots.txt를 확인해보니 sitemap의 경로가 변경 전 계정의 주소로 되어 있었다.
    - sitemap 경로 수정 후 push한 뒤 다시 확인해보니 Naver Search Advisor의 7번 이미지처럼 모든 항목에 V 표시가 되어 있음을 확인했다.

<br/>
---
<br/>

## 참고

[네이버 웹마스터 가이드](https://searchadvisor.naver.com/guide/seo-basic-robots){: target="_blank"}

[Dodev님 - 초보자를 위한 Github Blog 만들기](https://wlqmffl0102.github.io/posts/Making-Git-blogs-for-beginners-2/){: target="_blank"}

[SuperMemi님 - 나만의 블로그 만들기](https://supermemi.tistory.com/entry/%EB%82%98%EB%A7%8C%EC%9D%98-%EB%B8%94%EB%A1%9C%EA%B7%B8-%EB%A7%8C%EB%93%A4%EA%B8%B0-Git-hub-blog-GitHubio){: target="_blank"}

[KiTFOx's Notepad님 - jekyll 테마 적용시킨 Github 블로그 로컬에서 변경사항 확인하기](https://dana3711.tistory.com/67){: target="_blank"}

<br/>

---
<br/>

### 추가
- 사용한 테마 : jekyll chirpy

<br/>

---

[^footnote]: 검색 로봇에게 웹 페이지 접근을 허용 및 제한하는 국제 권고안으로, 크롤링해서는 안 되는 사이트 내 URL이나 디렉터리를 검색 엔진에게 알려주는 텍스트 파일의 이름. 반드시 사이트의 루트 디렉터리에 위치해야 하며 텍스트 파일로 접근이 가능해야 한다.