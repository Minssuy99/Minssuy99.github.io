---
title: Chirpy 테마를 이용한 Github Pages 개설 [3]
description: 테마 커스터마이징
author: Minssuy99
date: 2025-01-16 20:54:00 +0900
categories: [Github Pages]
tags: [Jekyll, Chirpy]
pin: false
# math: false
# mermaid: false
image:
  path: '/Jekyll.jpg'
  lqip: data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEAYABgAAD/2wBDAAIBAQIBAQICAgICAgICAwUDAwMDAwYEBAMFBwYHBwcGBwcICQsJCAgKCAcHCg0KCgsMDAwMBwkODw0MDgsMDAz/2wBDAQICAgMDAwYDAwYMCAcIDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAz/wAARCAAZADIDASIAAhEBAxEB/8QAGgABAAIDAQAAAAAAAAAAAAAAAAEHAgMICf/EACUQAAICAgEFAAEFAAAAAAAAAAECAAMEBREGBxITIVEIFCIxYf/EABcBAQEBAQAAAAAAAAAAAAAAAAACAQP/xAAeEQEAAgICAwEAAAAAAAAAAAABAAIDIRExQWFxgf/aAAwDAQACEQMRAD8A8y4iSiNY6qqszMeAAOSTESIlhdC9m9RstNq87qjqjF6bXY51XjgsrHPt15DM2Ui+JVVf12JWXP1/WWT1We5bETtD2yyO4zfu7qbMHdY2NnYfT+qyMvGydZiBaLnDNkGyxsqypbAykugSzIuqDFcSq+bWKja3ROuHDkzZK4cQ2tZADtXQHtZzyG8h8iWJ+q3uXqu7Pf3f7jQ9OaXpPRq1OBg6vVhGx6K8elKPP2Kqm5rGra1rWAZzZzwo4Va7mjycyMlGlmj41pE/E0/TTERE2TEEciIiJJdmtZyzF2byZif5E/kn8/7M8fKtxMkXU2203KxcWI5Vwx/s8j7z9P2a4iIA8RwIiIiIiIif/9k=
  # alt:
media_subpath: '/assets/img/Github Pages'
---
<!--------------------------------------yaml-------------------------------------->

> 본 게시물은 `Windows 10` 환경에서 진행됨을 알려드립니다.
{: .prompt-info }

> `25.01.14` 일자 기준으로 진행하며, `Chirpy` 의 버전은 `7.2.4v` 입니다.
{: .prompt-info }

## _**북마크**_
---

{% include embed/bookmark.html
url="https://minssuy99.github.io/posts/Jekyll_Chirpy_-1/"
title="Chirpy 테마를 이용한 Github Pages 개설 [1]"
description="Repository 생성 및 클론, Ruby 와 Jekyll 설치" %}

{% include embed/bookmark.html
url="https://minssuy99.github.io/posts/Jekyll_Chirpy_-2/"
title="Chirpy 테마를 이용한 Github Pages 개설 [2]"
description="로컬 서버 생성 및 테마 설치" %}

> ☝ 위의 단계를 진행하지 않으신 분들께서는 위 링크를 클릭하여 이전 글을 확인해주시기 바랍니다.

이번 글에선 테마를 커스터마이징을 하고, `github` 에 업로드하여 배포를 하는 과정을 설명하겠습니다.

<!--------------------------------------header1-------------------------------------->
## _**1. _config.yml 수정**_
---

`VS Code` 를 실행 후, <kbd>Open Folder...</kbd> 를 클릭 후, 프로젝트 폴더를 열어주세요.

![VS Code](open_folder.jpg)

사이드바에서 _`config.yml`_ 을 클릭합니다.

![VS Code](select_config.jpg)

