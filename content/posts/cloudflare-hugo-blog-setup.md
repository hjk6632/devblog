---
date: 2026-03-15
draft: false
tags:
- blog
- cloudflare
- hugo
- devlog
title: Cloudflare Workers + Hugo로 개발 블로그 구축하기 (설치부터
  배포까지)
---

# Cloudflare Workers + Hugo로 개발 블로그 구축하기

개발을 하다 보면 구현 과정이나 트러블슈팅 내용을 정리해두고 싶을 때가
많다.\
검색해서 다시 찾기 어려운 내용이나, 같은 문제를 다시 겪었을 때 빠르게
참고하기 위한 기록이기도 하다.

그래서 개인 개발 기록을 남길 공간으로 **정적 사이트 기반 블로그**를
구축하기로 했다.

이번 글에서는 **GitHub 저장소 생성부터 Cloudflare 배포, Hugo 테마
적용까지**\
오늘 실제로 진행했던 블로그 구축 과정을 정리한다.

------------------------------------------------------------------------

# 1. GitHub 저장소 생성

먼저 블로그 소스를 관리하기 위한 GitHub 저장소를 생성했다.

devblog

GitHub를 사용하는 이유는 다음과 같다.

-   블로그 소스를 버전 관리할 수 있음
-   Cloudflare와 연동하여 자동 배포 가능
-   Markdown 기반으로 글 관리 가능
-   변경 이력 관리가 쉬움

초기 저장소 설정은 다음과 같이 진행했다.

echo "\# devblog" \>\> README.md git init git add README.md git commit
-m "first commit" git branch -M main git remote add origin
https://github.com/{username}/devblog.git git push -u origin main

이 과정을 통해 GitHub에 블로그 소스 저장소를 준비했다.

------------------------------------------------------------------------

# 2. Cloudflare Workers & Pages 배포 환경 구성

다음 단계는 GitHub 저장소를 Cloudflare와 연결하여 자동 배포 환경을
만드는 것이다.

Cloudflare는 다음과 같은 기능을 제공한다.

-   정적 사이트 호스팅
-   CDN 제공
-   무료 HTTPS
-   GitHub 연동 자동 배포

GitHub 저장소를 Cloudflare에 연결하면 다음과 같은 흐름으로 배포가
이루어진다.

GitHub push\
↓\
Cloudflare build\
↓\
정적 파일 생성\
↓\
Workers / CDN 배포

이 구조를 사용하면 **GitHub에 코드를 push하는 것만으로 블로그가 자동으로
배포된다.**

------------------------------------------------------------------------

# 3. Hugo 정적 사이트 생성기 사용

블로그 엔진으로 **Hugo**를 사용했다.

Hugo는 대표적인 정적 사이트 생성기(Static Site Generator)이며 다음과
같은 장점이 있다.

-   Markdown 기반 글 작성
-   매우 빠른 빌드 속도
-   정적 HTML 생성
-   다양한 테마 지원
-   서버 없이 운영 가능

Cloudflare에서 Hugo 빌드를 실행하도록 설정했다.

Build command : hugo\
Root directory : /

Hugo는 빌드를 수행하면 다음 폴더에 정적 사이트를 생성한다.

public/

Cloudflare는 이 **public 폴더에 생성된 HTML 파일을 웹사이트로
배포**한다.

------------------------------------------------------------------------

# 4. Hugo 테마 적용

처음 Hugo를 빌드했을 때 다음과 같은 경고 메시지가 나타났다.

WARN found no layout file for "html"

이 메시지는 **Hugo가 페이지를 렌더링할 HTML 레이아웃을 찾지 못했다는
의미**이다.

즉, Hugo는 **테마(Theme)가 있어야 화면 레이아웃을 생성할 수 있다.**

그래서 Hugo 기본 테마 중 하나인 **Ananke 테마**를 추가했다.

git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke
themes/ananke

이 명령을 실행하면 저장소 안에 다음과 같은 구조가 생성된다.

themes/ananke

그리고 Hugo 설정 파일에 테마를 지정했다.

hugo.toml

theme = "ananke"

이렇게 설정하면 Hugo가 페이지를 렌더링할 레이아웃을 사용할 수 있다.

------------------------------------------------------------------------

# 5. GitHub 인증 토큰 발급

로컬 환경에서 GitHub에 push하기 위해 **Personal Access Token**을
발급했다.

최근 GitHub는 보안 정책으로 인해 **비밀번호 인증 대신 토큰 기반 인증
방식**을 사용한다.

토큰을 발급한 후 Git Bash에서 push를 진행했다.

git add . git commit -m "add hugo theme" git push

GitHub에 push가 완료되면 Cloudflare가 자동으로 빌드를 시작한다.

------------------------------------------------------------------------

# 6. Cloudflare 자동 배포

GitHub에 코드가 push되면 Cloudflare는 자동으로 다음 과정을 수행한다.

git push\
↓\
Cloudflare build 실행\
↓\
Hugo 정적 사이트 생성\
↓\
public 폴더 업로드\
↓\
workers.dev 도메인 배포

배포가 완료되면 다음 주소에서 블로그를 확인할 수 있다.

https://devblog.heejin63.workers.dev

이 구조의 장점은 **별도의 서버 운영 없이도 블로그를 운영할 수 있다는
점**이다.

------------------------------------------------------------------------

# 7. 현재 블로그 프로젝트 구조

현재 GitHub 저장소 구조는 다음과 같다.

devblog\
├ content\
│ └ posts\
├ themes\
│ └ ananke\
├ hugo.toml\
└ README.md

Hugo에서는 **content 폴더에 Markdown 파일을 추가하면 자동으로 게시글
페이지가 생성된다.**

예를 들어 다음과 같은 파일을 생성하면

content/posts/example.md

Hugo가 이를 HTML로 변환하여 블로그 게시글로 제공한다.

------------------------------------------------------------------------

# 마무리

이번 작업을 통해 **Cloudflare와 Hugo를 이용한 개발 블로그 환경을
구축했다.**

정적 사이트 기반 블로그의 장점은 다음과 같다.

-   빠른 로딩 속도
-   무료 호스팅 가능
-   Git 기반 관리
-   Markdown 작성 편의성

앞으로 이 블로그에는 다음과 같은 내용을 기록할 예정이다.

-   개발 트러블슈팅
-   서버 운영 경험
-   코드 구현 기록
-   개인 프로젝트 정리

개발 과정에서 얻은 경험을 기록하고 공유하는 공간으로 활용할 계획이다.
