---
title: Chirpy 테마를 이용한 Github Pages 개설 [2]
description: 로컬 서버 생성 및 테마 설치
author: Minssuy99
date: 2025-01-16 12:54:00 +0900
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

> ☝ 1단계를 진행하지 않으신 분들께서는 위 링크를 클릭하여 이전 글을 확인해주시기 바랍니다.

이번 글에서는 이전에 생성한 `Repository` 를 바탕으로 로컬 서버를 실행해보고, 테마를 적용해보도록 하겠습니다.

<br>

<!--------------------------------------header1-------------------------------------->

## _**1. 로컬 서버 실행하기**_
---

**1. Jekyll 프로젝트 생성하기**

<kbd>Win</kbd> 키를 누르고, `PowerShell` 을 검색 후 실행하신 뒤, 아래의 명령어를 입력하여 현재 디렉토리의 위치를 확인합니다.

```bash
$ pwd
C:\Users\username
```

<br>

현재 디렉토리가 파악이 되었다면, `Powershell` 에서 바탕화면에 있는 `username.github.io` 폴더로 이동해주세요.

디렉토리를 이동할 땐, `cd` 명령어를 이용하시면 됩니다.

> 프로젝트 폴더에 직접 들어가서 파일 탐색기 위쪽에 있는 경로를 복사한 후<br>`cd` 명령어 뒤에 붙여넣기 하시면 빠르게 이동하실 수 있습니다.
{: .prompt-tip}

```bash
$ cd .\Desktop\username.github.io\
```

<br>

반대로, 이전 디렉토리로 이동하고 싶으시다면 `cd ..` 명령어를 입력해주시면 됩니다.

`cd` 와 `..` 사이에는 공백이 있으니, 이 점 확인 바랍니다.

```bash
$ cd ..
```

<br>

이동하셨다면, 마지막으로 한번 더 `pwd` 를 입력하여 현재 디렉토리가 아래와 같은지 확인해봅시다.

```bash
$ pwd
C:\Users\MinSeong\Desktop\username.github.io
```

<br>

해당 디렉토리로 오셨다면, 아래의 명령어를 실행하여 `Jekyll` 프로젝트를 생성합니다.

> 아래의 명령어를 실행하기 전, 프로젝트 폴더 내부에 파일이 있다면 <br> 전부 삭제 후 명령어를 실행하시기 바랍니다.
{: .prompt-warning}

> 프로젝트 폴더 내부에 파일이 있다면, 명령어 실행 시 아래와 같은 에러가 나타나게 됩니다.<br> Conflict: C:/Users/Username/Desktop/username.github.io exists and is not empty. <br> Ensure C:/Users/Username/Desktop/username.github.io is empty or else try again with `--force` to proceed and overwrite any files.
{: .prompt-danger}

```bash
$ jekyll new ./
```

명령어를 실행한 후, 프로젝트 폴더에 아래와 같이 파일들이 생성되었다면 성공입니다.

![jekyll](jekyll_new.jpg)

<br>

설치가 완료되었다면, 아래의 명령어를 순서대로 실행합니다.

```bash
$ bundle install
$ bundle update
$ bundle install
```

<br>

`install` 이 완료되었다면, 아래의 명령어를 입력하여 로컬 서버를 실행합니다.

```bash
$ bundle exec jekyll serve
```

![jekyll](bundle_exec_jekyll_server.jpg)

실행을 한 뒤 사진의 빨간색 박스를 보시면, `http://127.0.0.1:4000/` 이 적혀있습니다.

인터넷 브라우저를 켜신 뒤, <http://127.0.0.1:4000/> 로 접속해줍니다.

<br>

사진과 같이 `Welcome to Jekyll!` 문구가 있는 사이트가 열린다면 성공입니다.

![jekyll](./welcome_to_jekyll.jpg)

<br>

#### **Q. 로컬서버는 무엇이고, 이것을 사용하는 이유는 무엇인가요?**

> 로컬 서버는 쉽게 말해서, 내 PC 에서만 작동하는 웹 서버입니다. 사용하는 이유는 주로 `Jekyll` 블로그를 개발하고 수정하는 과정에서 빠르게 결과를 확인하기 위함입니다. 코드를 수정 후 페이지를 새로고침 하면, 실제로 게시글을 배포하기 전의 모습을 확인할 수 있고, 오류를 미리 파악하여 배포 후 발생하는 버그를 줄일 수 있습니다.
{: .prompt-info}

<br>

<!--------------------------------------header2-------------------------------------->
## _**2. Chirpy 테마 설치 및 초기화**_
---

**1. Chirpy 테마 다운로드**

> 본문은 chirpy 테마를 기준으로 진행합니다.
{: .prompt-info}

{% include embed/bookmark.html
url="http://jekyllthemes.org/"
title="More Jekyll Themes"
description="다른 테마를 알아보고 싶으신 분들은 여기를 클릭하세요." %}

{% include embed/bookmark.html
url="http://jekyllthemes.org/"
title="Jekyll Chirpy Theme"
description="Chirpy 테마를 다운로드 하기 위해 여기를 클릭하세요." %}

<br>

위 링크를 클릭하여 사이트에 접속한 뒤, <kbd>Releases v7.2.4</kbd> 를 클릭합니다.

![jekyll](chirpy_github.jpg)

<br>

<kbd>Source code (zip)</kbd> 를 클릭하여 `jekyll-theme-chirpy-7.2.4.zip` 을 다운로드 합니다.

![jekyll](source_code.jpg)

<br>

다운로드한 압축파일을 안의 모든 파일들을 본인의 프로젝트 폴더 `username.github.io` 에 전부 넣어주세요.

파일들을 넣으실 때, 중복 경고가 나타나게 되는데 덮어쓰기 해주시면 됩니다.

![jekyll](move_files.jpg)

![jekyll](overwrite.jpg)

<br>

**2. Chirpy 테마 초기화**

초기화는 말 그대로 테마의 순정상태를 만드는 것 입니다.

`Linux` 에서는 _**`bash tools/init.sh`**_ 라는 명령어로 초기화를 쉽게 할 수 있습니다.

하지만 `Windows` 환경에서는 동작하지 않아서, 직접 삭제하는 과정을 거쳐야 합니다.

아래의 폴더 및 파일을 찾아서 삭제해주시면 되겠습니다.

* _`username.github.io/.github/workflows/starter`_ 폴더 삭제
* _`username.github.io/docs`_ 폴더 삭제
* _`404.html`_ 파일 삭제
* _`about.markdown`_ 파일 삭제
* _`index.markdown`_ 파일 삭제

<br>

**3. github message 수정**

_`username.github.io/.husky`_ 경로에 들어가시면 `commit-msg` 라는 파일이 있습니다.

이 파일을 더블클릭 후, `VS Code` 로 실행해주세요.

![jekyll](commit-msg.jpg)

`commit-msg` 파일 내부엔 아래와 같은 코드가 작성되어 있는데, **내용을 전부 지워주시면 됩니다.**

```bash
npx --no -- commitlint --edit $1
```

<br>

## _**3. Node.js 및 npm 설치**_
---

현재 목차 2번까지 진행한 상태에서 _`bundle exec jekyll server`_ 을 실행했을 때

아래와 같은 에러메세지가 발생하는 분들이 계실겁니다.

> assets/js/dist/*.min.js Not Found
{: .prompt-danger}

이는 _`username.github.io/assets/js`_ 내부에 `dist` 폴더 및 `js` 파일이 존재하지 않아 발생하는 오류입니다.

<br>

이를 해결하기 위해서 `Node.js` 및 `npm` 을 설치합니다.

{% include embed/bookmark.html
url="https://nodejs.org/ko"
title="Node.js"
description="Node.js 를 설치하기 위해서 여기를 클릭하세요." %}

<br>

`Node.js` 를 설치하셨다면, `npm` 도 함께 설치됩니다.

설치를 해주신 뒤, `Powershell` 을 실행하여 아래의 명령어를 입력했을 때, 아래와 같이 출력되면 성공입니다.

```bash
$ node -v
v22.13.0
```

```bash
$ npm -v
10.9.2
```

<br>

이후 `PowerShell`을 통해서 프로젝트의 루트 디렉토리로 이동합니다.

```bash
$ cd .\Desktop\username.github.io\
```

아래의 명령어를 순서대로 실행해주시면 됩니다.

```bash
$ npm install
$ npm run build
```

아래 사진과 같이 `dist` 폴더 및 파일들이 생성되었다면 성공입니다.

![jekyll](dist.jpg)

<br>

이후 아래의 명령어를 실행 후 로컬서버로 접속합니다.

```bash
$ bundle exec jekyll server
```

<br>

로컬서버의 주소는 [목차 1번](#1-로컬-서버-실행하기) 에서 보여셨듯이, <http://127.0.0.1:4000> 로 접속해주시면 됩니다.

아래와 같은 화면이 출력된다면 성공입니다.

![jekyll](local_server.jpg)

<br>
<br>

## _**마치며**_
---

이번 글에선 로컬서버를 통해서 블로그에 `chirpy` 테마를 적용하고 확인하는 단계까지 끝마쳤습니다.

다음 글에선, 블로그를 커스텀하고 `github` 에 `push` 까지 하는 과정을 알아보겠습니다.

긴 글 읽어주셔서 감사합니다.


### _**Reference**_

* [프로의 개발일지](https://devpro.kr/)
* [하얀눈길 블로그](https://www.irgroup.org/)
* [걷는보리](https://walkbori.com/)