---
title: github 블로그 만들기
categories: [Blog]
tags: [study, github, jekyll, chirpy, theme, blog]
description: >-
    jekyll chirpy theme로 github 블로그 만들기
---

# 개념

## [jekyll](https://jekyllrb.com/)

- Github의 공동 설립자인 톰 프레스턴 워너가 개발한 정적 웹 사이트 생성기
- Ruby 언어 기반
- MIT


## Github Pages

- Github를 통해 호스트되고 게시되는 Public 웹 페이지
- Jekyll 테마 적용 가능


# 초기 세팅

## 1. Repository 생성

![repository](/assets/img/post_img/blog_img/repo.png)

사용자 페이지 생성을 위해 본인 계정의 이름으로 시작하는 github.io 공개 저장소를 만든다.<br/>
만약 계정 이름과 저장소 이름이 다르면 프로젝트 페이지로 설정이 되어서 url이 복잡해진다.

<br/>

이후 저장소를 local에 clone한다.

## 2. Jekyll Theme 선택

- [jekyll-themes](https://jekyll-themes.com/)
- [Free Jekyll themes](https://jekyllthemes.io/free)
<br/>
위는 Jekyll 테마 list를 보여주는 사이트다. 마음에 드는 테마를 골라보자.<br/>
필자는 [jekyll-theme-chirpy](https://github.com/cotes2020/jekyll-theme-chirpy)를 선택했다.<br/>
테마를 선택했다면 저장소를 clone한 위치에 해당 테마의 압축 파일을 풀거나 해당 테마의 저장소를 clone하여 pull 받는다.<br/>
<br/>
이후의 이슈 (~.min.js)를 대비하기 위해 저장소 위치에서 `bash tools/init`를 실행해 asset/js/dist/를 빌드하자.

## 3. Ruby 설치

Jekyll은 Ruby로 만들어졌기 때문에 해당 프로그래밍 언어를 설치해야 한다. [Ruby Installer](https://rubyinstaller.org/downloads/)에 들어가 필요한 버전을 선택해 다운로드 하자.<br/>
만약 어떤 버전을 설치해야 할 지 모르겠다면 추천 버전을 설치하거나 선택한 테마가 사용하는 버전을 설치하면 된다.<br/>

<br/>

테마의 버전은 .github/workflows/starter/pages-deploy.yml에서 확인할 수 있다.

<br/>

설치 후 **Start Command Prompt with Ruby**를 실행해 저장소가 clone된 디렉터리 위치로 이동한다. 필자의 경우, C 드라이브 내 blog로 clone 했기 때문에 C:\blog. 즉, `cd C:\blog` 로 이동했다.

```text
chcp 65001
```

위 명령어를 입력해 저장소 위치를 UTF-8로 인코딩을 하고 아래 이미지처럼 **Active code page: 650001**가 출력되면 성공이다.<br/>

![ruby](/assets/img/post_img/blog_img/ruby.png)

> chcp 는 인코딩 방식을 변경하는 명령어다. cmd에서 기본적으로 사용하는 인코딩 방식은 cp949로 EUC-KR 확장 버전이며, 위 명령어로 설정한 ch65001은 UTF-8이다. 인코딩 방식을 변경하는 이유는 해당 테마가 UTF-8 형식을 사용하고 있기 때문이며, 만약 설정하지 않았다면 사이트 생성 단계에서 **Liquid Exception: Incompatible character encoding**가 발생할 수 있다.
{: .prompt-tip }

## 4. bundle 실행

commend로 저장소 디렉터리 위치로 이동한 후 아래 명령어를 실행한다.

```text
bundle install
bundle exec jekyll serve
```

위 명령을 통해 bundle을 install하고 아래 명령어로 jekyll을 실행한다. 이후 출력되는 주소 ([http://127.0.0.1:4000/](http://127.0.0.1:4000/))에서 세팅한 테마가 실행되면 성공이다.

<br/>

만약 실행 도중 노란색 경고문이 출력된다면 이는 ~.min.js 파일이 존재하지 않기 때문이라서 `npm install`과 `npm build`를 실행하면 된다.

<br/>

[해당 이슈 참고](https://velog.io/@lzlko/github-%EB%B8%94%EB%A1%9C%EA%B7%B8)

# 블로그 세팅

## 1. 필요없는 파일 삭제 / 변경
git push 전, chirpy 테마는 몇 가지 삭제 및 변경해야 하는 파일이 존재한다. 아래 파일과 폴더를 삭제, 변경하자.

1. docs 폴더 삭제
2. .github 내 workflows 폴더 외 전부 삭제
3. .github/workflows/ 아래에 starter 폴더 외 전부 삭제
4. .github/workflows/starter/pages-deploy.yml 파일을 확인하고 확장자에 .hook이 있다면 .hook 삭제
5. pages-deploy.yml 파일을 열어서 branches를 main만 사용할  수 있도록 변경
6. .gitignore에서 # Misc 아래 부분을 주석 처리 한다. (dist가 속한 부분 모두 주석 처리)

![pages-deploy](/assets/img/post_img/blog_img/page.png)

## 2. Github Action 활성화

![action](/assets/img/post_img/blog_img/action.png)

저장소의 Settings -> Pages 로 이동하여 Build and deployment를 Deploy from branch에서 GitHub Actions로 변경한다.

## 3. PUSH

모든 설정을 완료한 후 git add . -> git commit -> git push 한다. 5분 정도 기다렸다가 Settings -> Pages -> Visit site로 블로그에 들어가면 데모와 동일한 화면을 볼 수 있다. push 전 적용이 제대로 되어 있나 확인하고 싶으면 jekyll을 실행해 로컬에서 확인해도 된다.<br/>

![demo](/assets/img/post_img/blog_img/blog.png)

<br/>

위 화면 중 이름 등의 프로필 부분은 필자가 수정한 내용이다.

## 4. 커스터마이징

### 1. _config.yml 수정하기

config 파일을 수정하여 위 이미지처럼 이름 등을 커스터마이징 해보자. 필자가 수정한 부분은 아래와 같다.

```yml
theme: jekyll-theme-chirpy

lang: ko-KR

timezone: Asia/Seoul

title: DevSu

tagline: BackSu's BackEnd Study

description: >- # SEO를 위한 키워드
  Blog for Java, Spring, and Relational Databases

url: "https://DevBackSu.github.io/" # 블로그로 접속할 URL

github:
  username: DevBackSu

social:
  name: (포스트에 표시할 이름)
  email: (이메일 주소)
  links: .... (사용하는 소셜 링크들)

...

theme_mode: # [light | dark]

cdn: ""

avatar: "/assets/img/main_img/cute.png"

social_preview_image: # string, local or CORS resources

toc: true

comments:
  provider: # [disqus | utterances | giscus]
  disqus:
    shortname: # fill with the Disqus shortname. › https://help.disqus.com/en/articles/1717111-what-s-a-shortname
  utterances:
    repo: # <gh-username>/<repo>
    issue_term: # < url | pathname | title | ...>
  giscus:
    repo: # <gh-username>/<repo>
    repo_id:
    category:
    category_id:
    mapping: # optional, default to 'pathname'
    strict: # optional, default to '0'
    input_position: # optional, default to 'bottom'
    lang: # optional, default to the value of `site.lang`
    reactions_enabled: # optional, default to the value of `1`

...

baseurl: ""

```

#### lang

_data/locales에 있는 파일 중 선택하면 된다. 필자는 한국어로 설정했는데 번역이 마음에 들지 않거나 영어로 설정하는게 더 예쁘다고 판단된 부분이 있어서 ko-KR.yml을 수정했다. 내부 코드가 어렵지 않아서 쉽게 변경할 수 있다.

#### theme_mode

모드 전환 버튼을 설정하고 싶다면 해당 부분을 비워두어야 한다. 만약 한 가지의 모드를 설정한다면 모드 전환 버튼은 보이지 않는다.<br/>
기본 모드는 light다.

#### cdn

만약 이 위치에 어떠한 경로를 입력하면 이미지를 해당 경로에서 조회한다. 즉, "https://cdn"을 적어두었다고 하자. 이후 게시글에 이미지를 첨부하기 위해 /assets/img/img.png를 넣는다면 블로그는 https://cdn/assets/img/img.png를 찾게 된다.<br/>
따라서 cdn 서버가 없다면 비워두어야 한다.

#### avatar

![avatar](/assets/img/post_img/blog_img/avatar.png)<br/>

귀여운 우주인 이미지가 있는 부분이다. /assets/img 아래 경로에 해당 이미지를 저장한 후, 이미지 경로를 넣어주면 된다.

#### Toc 설정

![toc](/assets/img/post_img/blog_img/toc.png)<br/>

포스트의 오른쪽에 위치하는 목차를 표시할 지 말지를 결정한다.

#### comments

사용할 댓글 기능 설정을 입력하는 부분이다. 필자는 giscus를 사용할 것이다.<br/>
[Giscus 사용하기](https://devbacksu.github.io/posts/comment/)


### 2. 사이드바 설정하기

_sass/addon/commons.scss를 수정하면 사이드바를 설정할 수 있다. commons 파일의 666 line 정도에 주석으로 **/* --- sidebar layout --- */**라고 되어 있는 부분이 있는데, 이 아래를 수정해야 한다. 필자는 아래와 같이 수정했다.

```scss
#sidebar {
  @include pl-pr(0);

  position: fixed;
  top: 0;
  left: 0;
  height: 100%;
  overflow-y: auto;
  width: $sidebar-width;
  z-index: 99;
  background-image: url('/assets/img/main_img/man.png');
  background-size: cover;
  background-repeat: no-repeat;
  background-position: center;
  border-right: 1px solid var(--sidebar-border-color);

  ...
}
```

변경 전 코드는 `background: var(--sidebar-bg);` 이다. 만약 이미지 파일로 설정하고 싶지 않다면 --sidebar-bg 값을 원하는 색상으로 변경하면 되고, 이미지 파일을 넣고 싶다면 위 코드처럼 변경하면 된다.

<br/>

사이드바의 색상값은 typography-dark.scss와 typography-light.scss에 정의되어 있다. 다크 모드에는 dark 파일의 설정값이, 화이트 모드에는 light 파일의 설정값이 적용된다.

<br/>

사용하지 않는 SNS가 있고, 그 로고가 사이드바에 뜨는 게 마음에 들지 않는다면 _includes/pageviews/sidebar.html을 수정하면 된다. 코드 내부에서 삭제하고 싶은 로고의 이름을 검색하고, 그 부분을 주석 처리하거나 삭제하자.

## 5. 완료

모든 설정을 완료했다면 다시 push한다.

![end](/assets/img/post_img/blog_img/end.png)<br/>

액션이 완료된 후 5분 정도 대기했다가 사이트에 들어가 새로고침을 하면 설정이 적용된 것을 확인할 수 있다.


# ERR

## 1. Can't not find stylesheet to import

![err](/assets/img/post_img/blog_img/err1.png)

<br/>

### 해결 방법
해당 경로의 dist가 있는지 확인한다.<br/>
필자의 경우, _sass/dist가 없어서 문제가 발생했었다.<br/>
[error: Jekyll::Converters::Scss encountered an error while converting 'assets/css/jekyll-theme-chirpy.scss'](https://github.com/cotes2020/jekyll-theme-chirpy/issues/1744)


## 2. Liquid Exception: undefined method `gsub' for 0

![err](/assets/img/post_img/blog_img/err2.png)

<br/>

post의 카테고리나 태그에 null or 0이 있을 경우 발생한다. 이와 같은 값을 넣기 위해서는 ' ' or " "로 문자열 표시를 해주어야 한다.

---

# 참고

[평생 무료로 개인 블로그 운영하기](https://www.peterkimzz.com/github-pages-nuxtjs)<br/>
[나만의 블로그 만들기](https://supermemi.tistory.com/entry/%EB%82%98%EB%A7%8C%EC%9D%98-%EB%B8%94%EB%A1%9C%EA%B7%B8-%EB%A7%8C%EB%93%A4%EA%B8%B0-Git-hub-blog-GitHubio)<br/>
[초보자를 위한 github 블로그 만들기 시리즈](https://wlqmffl0102.github.io/posts/Making-Git-blogs-for-beginners-2/)<br/>