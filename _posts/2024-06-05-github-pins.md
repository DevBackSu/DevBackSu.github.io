---
title: Github pins 커스텀하기
categories: [Study, Github]
tags: [github, pin, github-stats-box, lang-box]
---

# 개념
## pin
- 다른 사용자에게 내 작업 중 가장 보여주고 싶은 것을 빠르게 보여주기 위해 프로필에 gist나 repository를 고정하는 기능을 말한다.
- 소유한 Public repository만 고정 가능하다.

## gist
- 타인에게 코드 블럭을 간단하게 공유할 수 있는 기능이다.
- 모든 gist는 git repository이기 때문에 clone과 fork가 가능하다.
- Public gist와 Private gist 두 종류의 gist를 만들 수 있다. (repository의 puvlic과 private와 유사하다.)

## github-stats-box
- 사용자의 github stats의 통계를 보여준다.
- MIT license

## lang-box
- 사용자가 최근 작업한 프로그래밍 언어 및 마크업 언어 등의 통계를 보여준다.
- MIT license

<br/>
<br/>
<br/>

# Setting
## Github Profile
1. username과 동일한 이름의 repository를 생성한다.
    - readme.md 파일 생성을 체크하는 것을 추천한다. 어차피 생성해야 한다.
    ![repo_create](/assets/img/post_img/pin/repo_create.png)

    <br/>

2. repository 생성 후 overview 탭 화면
    ![overview](/assets/img/post_img/pin/overview-start.png)

<br/>

## github-stats-box
1. [github-stats-box](https://github.com/LellowMellow/github-stats-box)로 이동하여 해당 repository를 fork한다.
    ![fork](/assets/img/post_img/pin/fork.png)
    ![fork](/assets/img/post_img/pin/fork-2.png)
    <br/>
2. Token 발급을 위해 [github token](https://github.com/settings/tokens/new)로 이동한다.
    ![token](/assets/img/post_img/pin/gist-token-repo-gist-Generate-token.png)
    - Note의 이름은 사용자가 알아보기 쉽게 설정하면 된다.
    - Select scopes의 경우, 해당 token을 fork 받은 repository의 gist에서 활용할 것이기 때문에 repo와 gist를 선택해야 한다.
    - 발급 시 아래의 화면으로 페이지가 넘어가는데, 검은색으로 줄이 쳐져 있는 곳이 token이다. 토큰 값은 페이지를 벗어나면 찾을 수 없으니 꼭 어딘가에 남겨두는 것이 좋다.
    - 필자의 경우, 토큰이 만료되니 Action에서 Run Error가 발생했다. 만약 토큰을 재발급하는 과정이 번거롭게 느껴진다면 **No expiration**을 설정하는 것을 추천한다.
      - **No expiration**을 설정하면 github에서 **보안을 위해 만료 기한을 설정하라** 라는 의미의 경고문을 띄운다.
    ![token](/assets/img/post_img/pin/token-success.png)
    <br/>
3. [gist](https://gist.github.com/)에서 gist를 생성한다.
    ![gist](/assets/img/post_img/pin/gist-create.png)
    <br/>
    이때, gist의 생성은 외부 접근이 가능하도록 public으로 해야 한다.
    ![gist](/assets/img/post_img/pin/public-create.png)
    <br/>
    gist 생성 후 id는 아래 이미지 노란 줄 부분과 주소창 가장 왼쪽에서 확인할 수 있다.
    ![gist](/assets/img/post_img/pin/gist-id.png)
    <br/>
4. 생성한 token과 gist를 github-stats-box repository에 등록한다.
    - 저장소의 setting -> Secrets and variables -> Actions에서 Repository secrets의 `New repository secret`을 클릭한다.
    ![secret](/assets/img/post_img/pin/action-secret.png)
    <br/>
    - Secret 부분에 복사해두었던 token값을 넣는다.
    ![secret](/assets/img/post_img/pin/secret-token-insert.png)

    > 위 이미지의 Name 부분은 run.yml에서 호출하는 이름인 GH_TOKEN으로 설정해야 한다.
    {: .prompt-warning }
    - 같은 방식으로 secret을 생성하여 Secret 부분에 gist_id를 넣고 Name은 GIST_ID로 설정하면 아래와 같이 생성한 secret을 확인할 수 있다.
    ![secret](/assets/img/post_img/pin/secret-token-success2.png)
5. 저장소의 Actions로 이동하여 아래 버튼을 클릭하여 workflow를 활성화한다.
    ![action](/assets/img/post_img/pin/fork-3.png)
6. github-stats-box는 정각마다 정보를 update하기 때문에 수정 사항을 바로 등록하고 싶다면 run_yml을 수정해 commit 하면 된다.
    ![run](/assets/img/post_img/pin/run_yml_update.png)

    > 만약 gist_id를 secret으로 등록하는 것이 번거롭다면 위 형광펜 부분에 gist_id를 직접 넣어주면 된다. 하지만 필자는 직접 id를 넣었을 때 오류가 나서 secret으로 등록했다.
    {: .prompt-tip }
    <br/>
7. commit이 반영된 것을 확인한다.
    ![commit](/assets/img/post_img/pin/commit.png)
    <br/>
    - gist가 github-stats-box로 변경되었는지 확인한다.

    ![gist](/assets/img/post_img/pin/gist-su.png)
    <br/>
8. overview에서 pin을 등록한다. 이때, pin은 repository가 아닌 gist를 등록해야 한다.
    ![pin](/assets/img/post_img/pin/pin_update.png)
    <br/>
9. 모든 사항을 완료했으면 pins 목록에 github-status-box가 뜬다.
    ![pin](/assets/img/post_img/pin/all_success.png)
    <br/>

<br/>

## lang-box
- github-stats-box와 동일하게 진행하면 된다.

1. [lang-box 저장소](https://github.com/DevBackSu/lang-box)로 이동하여 fork한다.

2. token과 gist를 생성한다.
    - gist의 경우 필자는 다음과 같이 생성했다.
    ![lang_gist](/assets/img/post_img/pin/lang-gist-create.png)

3. fork한 lang-box에 secrets을 각각 생성하고 run.yml을 수정 후 commit 한다.
    - fork 후 gist가 lang-box로 변경되었는지 확인한다.
    ![lang_gist](/assets/img/post_img/pin/lang-success.png)

4. pin에 등록한다.

# Pins
![pins](/assets/img/post_img/pin/end.png)

---
<br/>

# gist Name 변경
- gist가 생성될 때 정해지는 기본 이름이 마음에 들지 않을 경우, 다음과 같이 수정하면 된다.
1. ![gist](/assets/img/post_img/pin/gist-name-change.png)
2. ![gist](/assets/img/post_img/pin/gist-name-change2.png)

<br/>

# gist update가 되지 않을 때
우선 토큰이 만료되었는지 확인한다.<br/>
만약 만료되었다면, 토큰을 재발급한 뒤, 해당 gist repository의 Actions로 이동한다.

![gist](/assets/img/post_img/pin/disabled.png)

토큰을 재발급한 후에는 위 이미지처럼 gist의 workflow가 disabled 상태여서 업데이트가 되지 않는다. 때문에 enabled workflow 버튼을 클릭해 gist가 정상적으로 업데이트되도록 한다.

![gist](/assets/img/post_img/pin/run.png)

Run workflow 버튼을 누르면 바로 업데이트를 시작한다.

---

## 참고
[_니지 님 - \[Github\] 깃허브 꾸미기 : github-stats-box](https://radiant515.tistory.com/331)

[Lellow_Mellow 님 - Github Profile 꾸미기](https://velog.io/@pexe99/Github-Profile-%EA%BE%B8%EB%AF%B8%EA%B8%B0)

[팔만코딩경 님 - 깃허브 프로필 꾸미기!](https://80000coding.oopy.io/865f4b2a-5198-49e8-a173-0f893a4fed45)