---
title: 개인 프로젝트 Roomie 구조
description: >-
    현재까지 구현 내용과 세부 구조 공유
categories: [Project, Roomie]
tags: [spring, react, project, js, java, roomie, node, boot, err, error]
---

작년 4월부터 시작한 프로젝트 Roomie의 현재까지 구현 사항을 작성해보고자 한다.<br/>
우선 Roomie는 함께 생활할 수 있는 룸메이트를 찾는 과정을 보다 안전하고 효율적으로 만드는 것이 목표다.<br/>
서비스의 주요 기능은 다음과 같다.

- 최대 4명이 참여할 수 있는 그룹 채팅
- 친구 추천 알고리즘을 기반으로 한 프로필 탐색
- 게시판을 통한 룸메이트 구인 및 정보 확인
- 신고 및 관리자 대응 시스템

## 기능 구현

### 소셜 로그인

가장 먼저 로그인 방식을 어떻게 할지 고민했다. 가장 간단한 건 내부적으로 회원가입을 받고 로그인을 진행시키는 것이겠지만, 이 방법은 사용자가 또 다른 비밀번호를 고민해야 하고, 시스템적으로도 비밀번호를 관리해야 하는 부담이 생기기 때문에 **OAuth2 기반의 소셜 로그인**을 도입했다. 소셜 로그인은 가입 과정도 간소화되고, 신뢰할 수 있는 플랫폼을 거친 인증을 받기 때문에 보안에 대한 불안감을 줄일 수 있는데다가 최근 많이 사용되는 방식이라서 선택했다.<br/>
현재까지는 구글 로그인만 가능하나, 코드 상으로는 카카오와 네이버 또한 구현해두어서 추후 확장 예정이다.<br/>
<br/>
다음으로 세션 관리 방식은 **Json Web Token**을 선택했다. 왜냐하면, 최근 많이 사용되는 방식이기도 하고, 서버가 사용자의 상태를 직접 저장하지 않아 서버의 수평 확장이 용이하며, 토큰 자체에 사용자 정보를 담고 있기 때문에 마이크로서비스 아키텍처 환경에서도 유리하기 때문이다. 또한, 클라이언트 측 저장과 헤더 기반 전송으로 다양한 도메인 간 인증 처리에도 강점을 가진다.<br/>

### 마이페이지 (조회 / 수정)

마이페이지는 사용자가 자신의 프로필을 직접 조회하고 수정할 수 있는 공간이다. 현재 구현된 항목은 다음과 같다.

- 프로필 사진
  - 지정된 5개의 프로필 중 하나를 선택할 수 있다.
- 닉네임
- 이메일
  - 이 부분은 가입한 소셜 로그인의 이메일이 자동으로 뜨도록 구현하려고 시도 중이다.
- 성별
- 메인 동물
  - 부엉이와 종달새 중에 선택 가능하다.
  - 부엉이 : 야행성 / 종달새 : 주행성
- 생년월
  - 태어난 년도 / 월까지만 입력 가능하도록 구현했다.
  - 구글에서는 생년월일 정보를 넘겨주지 않기 때문에 사용자가 등록하도록 구현했다.
- 학교
  - 학교 위치를 입력할 수 있도록 한다.
  - 필수 사항이 아니며, 입력 시 친구 추천에 영향을 준다.
- 지역
  - 거주 지역을 입력할 수 있도록 한다. (상세 주소는 입력하지 않아도 무관)
  - 필수 사항이 아니며, 입력 시 친구 추천에 영향을 준다.
  - 어디까지 입력하도록 할지 고민 중이다.
- 특성
  - 지정된 10개의 특성 중 최소 1개 ~ 최대 5개의 특성을 선택할 수 있다.
- 자기소개
  - 선택한 특성 외의 필요한 내용을 작성할 수 있도록 만들었다.
  - 입력하지 않고 저장을 누르면 '만나서 반가워요!'가 자동 저장된다.

삭제 기능은 제공하지 않되, 계정 삭제 (회원 탈퇴) 시 모든 정보가 삭제된다.

### 게시판

현재까지 구현한 부분은 게시글 목록과 상세 게시물 조회 (댓글 미구현 상태)까지다.<br/>
추후 구현 예정인 기능은 다음과 같다.

- 댓글 기능
- 키워드 검색 및 필터링 (게시글 목록 부분에서 사용 가능)
- 좋아요 / 관심 기능 / 조회수
  - 조회수는 고민 중이다. 없어도 그만, 있어도 그만인 것 같다.
  - 좋아요나 관심 기능은 구현할 의향이 있다. 어떤식으로 구현할지는 고민 중이다.

### 회원 탈퇴

사용자가 스스로 서비스 이용을 중단하고 싶을 때를 고려해 회원 탈퇴 기능을 제공한다. 회원 탈퇴는 모든 페이지의 푸터에 존재하며, 현재는 '정말 탈퇴하시겠습니까?'로 확인만 하고 탈퇴를 진행한다. 탈퇴 시 사용자와 관련한 모든 정보 (게시글이랑 댓글 제외 여부는 고민 중)가 삭제된다.<br/>
추후 탈퇴 사유 수집이나 정책 안내도 구현이 필요할 것 같다.

## 프로젝트 구조

```text
com.example.roomie
ㄴ Auth
  ㄴ CustomOAuth2User
  ㄴ CustomOAuth2UserService
  ㄴ GoogleOAuth2UserInfo
  ㄴ KakaoOAuth2UserInfo
  ㄴ NaverOAuth2UserInfo
  ㄴ OAuth2UserInfo
  ㄴ OAuthAttribute
  ㄴ LoginFailureHandler
  ㄴ LoginSuccessHandler
ㄴ JWT
  ㄴ JwtAuthenticationProcessionFilter
  ㄴ JwtService
ㄴ Config
  ㄴ SecurityConfig
  ㄴ WebConfig
  ㄴ SwaggerConfig
ㄴ Entity
  ㄴ User
  ㄴ Role
  ㄴ SocialType
  ㄴ Character
  ㄴ UserCharacter
  ㄴ Self
  ㄴ Post
ㄴ DTO
  ㄴ UserDTO
  ㄴ UserOtherDTO
  ㄴ UserPageDTO
  ㄴ UserSingUpDTO
  ㄴ CharacterDTO
  ㄴ RankDTO
  ㄴ PostDTO
ㄴ Controller
  ㄴ UserController
  ㄴ MainController
  ㄴ MypageController
  ㄴ PostController
ㄴ Service
  ㄴ UserService
  ㄴ MainService
  ㄴ MyPageService
  ㄴ PostService
  ㄴ Impl
    ㄴ UserServiceImpl
    ㄴ MainServiceImpl
    ㄴ MyPageServiceImpl
    ㄴ PostServiceImpl
ㄴ Repository
  ㄴ UserRepository
  ㄴ UserCharacterRepository
  ㄴ CharacterRepository
  ㄴ SelfRepository
  ㄴ MainRepository
  ㄴ PostRepository
ㄴ SwggerForm
  ㄴ Response
    ㄴ ErrorResponse
    ㄴ UserInfoResponse
  ㄴ UserControllerDocs
  ㄴ MainControllerDocs
  ㄴ MyPageControllerDocs
  ㄴ PostControllerDocs
```

### Auth

```text
Auth
  ㄴ CustomOAuth2User
  ㄴ CustomOAuth2UserService
  ㄴ GoogleOAuth2UserInfo
  ㄴ KakaoOAuth2UserInfo
  ㄴ NaverOAuth2UserInfo
  ㄴ OAuth2UserInfo
  ㄴ OAuthAttribute
  ㄴ LoginFailureHandler
  ㄴ LoginSuccessHandler
```

OAuth2 인증과 관련한 부분이 존재한다. 자세한 설명은 (추후 작성할 글의 블로그 주소)에서 확인할 수 있다.

### JWT

```text
JWT
  ㄴ JwtAuthenticationProcessionFilter
  ㄴ JwtService
```

JWT 인증과 관련한 부분이 존재한다. 자세한 설명은 (추후 작성할 글의 블로그 주소)에서 확인할 수 있다.

### Config

```text
Config
  ㄴ SecurityConfig
  ㄴ WebConfig
  ㄴ SwaggerConfig
```

설정 파일이 존재하는 디렉터리다. SecurityConfig와 WebConfig는 서버 설정과 관련한 부분이며, SwaggerConfig는 스웨거와 관련한 부분이다. 자세한 설명은 각각 (추후 작성할 글의 블로그 주소)에서 확인할 수 있다.

### Controller

```text
Controller
  ㄴ UserController
  ㄴ MainController
  ㄴ MypageController
  ㄴ PostController
```

클라이언트의 요청을 수신하고 적절한 서비스로 연결시키는 컨트롤러가 존재하는 디렉터리다. 기능별 도메인을 기준으로 모듈화되어 있어 높은 응집도와 낮은 결합도를 가진다.

### Service

```text
Service
  ㄴ UserService
  ㄴ MainService
  ㄴ MyPageService
  ㄴ PostService
  ㄴ Impl
    ㄴ UserServiceImpl
    ㄴ MainServiceImpl
    ㄴ MyPageServiceImpl
    ㄴ PostServiceImpl
```

비즈니스 로직을 포함하며 레포지토리와 직접 연동되는 서비스가 존재하는 디렉터리다.<br/>
인터페이스와 구현체를 분리한 이유는 아래와 같다.

- 실무에서 사용되는 구조라고 알고 있음
- 유지보수성
- 확장성
- 인터페이스 기반 설계 원칙 준수

우선 인터페이스와 구현체를 분리하면 서비스 인터페이스는 단위 테스트 등에 사용할 수 있고, 기능 정의에 집중할 수 있다. 또한 실제 로직을 Impl에 구현하면 추상화를 통해 의존성을 낮추고 유연한 구조를 만들 수 있다. 즉, 구현체가 교체되더라도 전체 구조를 수정하지 않아도 된다.<br/>
만약 서비스가 커지게 되면 Impl이 복잡해질 수 있다. 이때, 기능 단위로 구현체를 나눠 관리하면 하나의 도메인에 존재하는 서비스는 인터페이스에서 한 번에 확인할 수 있고, 세세한 로직은 서로 다른 파일에서 관리하여 가독성을 높일 수 있다.<br/>
그리고 비록 1인 개발이지만 추후 누군가가 함께 참여할 수도 있어서 분리해두었다. 인터페이스만 먼저 정의해두면 내부 로직은 병렬적으로 개발할 수 있기 때문이다.

### Repository

```text
Repository
  ㄴ UserRepository
  ㄴ UserCharacterRepository
  ㄴ CharacterRepository
  ㄴ SelfRepository
  ㄴ MainRepository
  ㄴ PostRepository
```

JPA 기반의 데이터 접근 계층을 담당한다. 레포지토리 또한 도메인 중심으로 구조화되어 있다.

### Entity

```text
Entity
  ㄴ User
  ㄴ Role
  ㄴ SocialType
  ㄴ Character
  ㄴ UserCharacter
  ㄴ Self
  ㄴ Post
```

도메인 중심으로 구조화되어 있으며, 핵심 비즈니스 개체를 정의한다. User, Role, SocialType, Character, UserCharacter, Self는 모두 사용자와 관련된 엔티티이며, Post는 게시글 정보 저장 구조를 나타낸다. 자세한 설명은 (추후 작성할 블로그 글 주소)에서 확인할 수 있다.

### DTO

```text
DTO
  ㄴ UserDTO
  ㄴ UserOtherDTO
  ㄴ UserPageDTO
  ㄴ UserSingUpDTO
  ㄴ CharacterDTO
  ㄴ RankDTO
  ㄴ PostDTO
```

API 응답 및 요청 간 데이터 전달을 위해 구조화된 DTO 클래스다. 엔티티와 유사하게 UserDTO, UserOtherDTO, UserPageDTO, UserSingUpDTO는 사용자 정보와 관련되어 있고, RankDTO는 메인 화면, PostDTO는 게시글 정보를 처리한다.

### SwaggerForm

```text
SwggerForm
  ㄴ Response
    ㄴ ErrorResponse
    ㄴ UserInfoResponse
  ㄴ UserControllerDocs
  ㄴ MainControllerDocs
  ㄴ MyPageControllerDocs
  ㄴ PostControllerDocs
```

API 문서화 및 응답 포맷을 정의하는 부분이다. Response 하위에는 표준 응답 템플릿이 존재하며, 각 ControllerDocs는 Swagger 문서화용 클래스다.

## 추후 개발할 기능

- 채팅
- 추천
- 알림 메일 or 톡 전송
- 관리자 부분
- 가능하다면 결제 시스템?
- 프론트 / 백 테스트 코드 작성하기
- (추후 필요한 기능이 생기면 작성)

## 마무리

Roomie는 기능별로 분리된 구조를 바탕으로 구현하였으며 아직 구현되지 않은 기능도 전체 설계에 따라 유기적으로 추가될 수 있도록 기반을 마련해두었다. 따라서 이후에도 지속적인 기능 보완 및 개선이 가능하다.<br/>
코드의 구조와 로직에 집중하며 서비스 운영에 필요한 요소를 직접 혼자 구현해가는 것은 처음이라 쉽지 않으나, 앞으로도 꾸준히 진행할 예정이다.