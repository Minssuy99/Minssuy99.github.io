---
title: Chirpy 테마를 이용한 Github Pages 개설 [1]
description: Repository 생성 및 클론, Ruby 와 Jekyll 설치
author: Minssuy99
date: 2025-01-14 00:00:00 +0900
categories: [Github Pages, Chirpy]
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

<!------------------------------------Header-------------------------------------->
## _**이 글을 시작하기 전에 ...**_
---

이 글을 보고 계시는 분들께서도 많은 고민 끝에 `Github Pages` 를 선택하셨을겁니다.

저 같은 경우엔 `Velog`, `Notion`, `Tistory` 를 전부 사용해보고, 최종적으로 `Github Pages` 를 사용하기로 결정했습니다.

여러분들께서는 각 플랫폼의 장단점, 차이점을 비교한 블로그의 글들을 충분히 보고 오셨을겁니다.

플랫폼 별 비교는 생략하고, 바로 `Github Pages` 를 만드는 방법을 설명하겠습니다.

<br>

<!------------------------------------Header1------------------------------------->
## _**1. Git Repository 생성 및 클론**_
---

> 본문에선 `Git` 과 `VS Code` 의 사용법에 대해 다루지않습니다.
{: .prompt-warning}

**시작하기에 앞서, 기본적으로 아래의 준비사항이 필요합니다.**

1. Git 이 설치되어야 합니다. [링크](https://git-scm.com/)
2. Github 의 계정이 필요합니다. [링크](https://github.com/)
3. Visual Studio Code 가 설치되어야 합니다. [링크](https://code.visualstudio.com/)

<br>

**1. Repository 생성하기**

`Github` 의 우측 위에 보이는 <kbd>+</kbd> 를 클릭 후, <kbd>New repository</kbd> 를 선택합니다.

![Github](./new_repository_button.jpg)

클릭 시 아래와 같은 화면이 나타나게 됩니다.

![Github](./Creat_a_new_repository.jpg)

`Repository name *` 에 `{username}.github.io` 를 입력합니다.

`username` 은 `Github` 에 로그인 하실 때 입력하시는 `ID` 입니다.

예시를 들자면, 제 `ID` 는 `Minssuy99` 이니, _`Minssuy99.github.io`_ 가 되겠네요.

그 외의 설정은 사진과 같이 해두시고, 우측 아래의 <kbd>Create repository</kbd> 를 클릭해주세요.

<br>

**2. 생성한 Repository 를 Clone 하기**

`Clone` 하기 위해선, `VS Code` 는 `github` 의 계정에 로그인 되어 있어야합니다.

좌측 아래에 프로필 아이콘을 클릭하여 `github` 로 로그인해주세요.

`VS Code` 를 실행하신 후, <kbd>Clone Git Repository...</kbd> 를 클릭합니다.

<kbd>Clone Git Repository...</kbd> 를 누르신 뒤, `VS Code` 의 상단의 <kbd>Clone from GitHub</kbd> 를 눌러주세요.

![Github](./clone_git_repository.jpg)

<br>

그럼 아까 전 저희가 생성해두었던 `{username}.github.io` 가 선택창에 나타나게 됩니다.

![Github](./click_my_repository.jpg)

해당 `Repository` 를 클릭 시, 저장위치를 설정 할 수 있습니다.

생성위치는 바탕화면으로 하고 설명을 진행하겠습니다.



마치셨다면, 바탕화면에 `username.github.io` 라는 빈 폴더가 생성되면 성공입니다.

제 폴더의 이름은 `Minssuy99.github.io` 가 되겠네요.

<br>

<!------------------------------------Header2------------------------------------->
## _**2. Ruby 설치**_
---

> `Ruby` 설치 시, 기본으로 설정된 경로로 설치해주시기 바랍니다.
{: .prompt-warning }

> `Ruby` 는 `3.3.4` 이상의 버전을 설치하지 마시기 바랍니다.
{: .prompt-danger }

**1. Ruby 다운로드**

👉 [Ruby Installer Archives 바로가기](https://rubyinstaller.org/downloads/archives/)

위 링크를 클릭하여 사이트에 접속한 뒤, 원하시는 버전을 선택 후 설치해주세요.

저는 <kbd>Ruby+Devkit 3.2.2-1 (x64)</kbd> 를 설치하였으며, 이를 권장합니다.

이러한 이유는, `Github Pages` 에서 `Ruby 3.3.4v` 이상의 버전을 지원하지 않기 때문입니다.

[여기](https://pages.github.com/versions/)를 클릭하여 현재 `Github Pages` 가 지원하는 프로그램들의 버전을 확인하실 수 있습니다.

<br>

**2. 설치 확인**

설치 후, 마지막 화면에서 _**`Run ridk install to setup`**_ 에 체크 하신 뒤 <kbd> Finish</kbd> 를 눌러주세요.

그럼 터미널 창이 뜨게 될텐데, <kbd>Enter</kbd> 를 눌러주시면 셋업이 자동으로 진행됩니다.

셋업이 끝나면 한번 더 <kbd>Enter</kbd>를 요구할텐데, 똑같이 <kbd>Enter</kbd>를 눌러주시면 됩니다.

<br>

모든 설치가 끝났다면, `cmd` 혹은 `터미널` 에 `ruby -v` 명령어를 입력해보시기 바랍니다.

아래와 같이 출력된다면 성공적으로 설치가 완료된 것입니다.

```bash
$ ruby -v
ruby 3.2.2 (2023-03-30 revision e51014f9c0) [x64-mingw-ucrt]
```

<br>

<!------------------------------------Header3------------------------------------->
## _**3. Jekyll 설치**_
---

**1. 터미널 실행 후 명령어 입력**

`cmd` 혹은 `PowerShell` 을 실행하신 뒤, 아래와 같이 명령어를 입력 후 실행합니다.

```bash
$ gem install jekyll
```

설치가 완료되었다면, 아래의 명령어도 추가로 입력 후 실행합니다.

```bash
$ gem install bundler
```

<br>

**2. 설치 확인**

설치가 완료되었다면, 아래의 명령어를 실행하여 확인해보시기 바랍니다.

```bash
$ jekyll -v
jekyll 4.3.4
```
```bash
$ bundler -v
Bundler version 2.6.2
```

<br>
<br>


## _**마치며**_
---

이로써 `Repository` 생성과 `Ruby`, `Jekyll` 설치에 대해 알아보았습니다.

어렵지 않으니 차근차근 따라하신다면 충분히 만드실 수 있습니다.

다음 글에선 로컬 서버 생성 및 테마 적용에 대해 알아보겠습니다.

긴 글 읽어주셔서 감사합니다.



### _**Reference**_

* [프로의 개발일지](https://devpro.kr/)
* [하얀눈길 블로그](https://www.irgroup.org/)
* [걷는보리](https://walkbori.com/)