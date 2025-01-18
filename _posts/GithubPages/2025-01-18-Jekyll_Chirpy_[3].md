---
title: Chirpy 테마를 이용한 Github Pages 개설 [3]
description: 테마 커스터마이징 및 배포
author: Minssuy99
date: 2025-01-18 22:45:00 +0900
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

<br>

<!--------------------------------------header1-------------------------------------->
## _**1. _config.yml 수정**_
---

`VS Code` 를 실행 후, <kbd>Open Folder...</kbd> 를 클릭 후, 프로젝트 폴더를 열어주세요.

![VS Code](open_folder.jpg)

사이드바에서 _`_config.yml`_ 을 클릭합니다.

![VS Code](select_config.jpg)

_`_config.yml`_ 파일을 수정하여 블로그 설정을 할 수 있습니다.

<br>

아래에 설명과 예시를 남겨놓았으니, _`_config.yml`_ 을 직접 수정해보시길 바랍니다.

_`_config.yml`_ 파일에서 <kbd>Ctrl</kbd> + <kbd>F</KBD> 키를 눌러 옵션을 빠르게 검색할 수 있습니다.

| Options            | Description                  | Example |
|---------------------|---------------------------------|-----------|
| lang        | 블로그의 언어를 설정합니다. | en, kr |
| timezone    | 블로그의 시간을 설정합니다. | Asia/Seoul |
| title       | 블로그의 제목을 설정합니다. | Minssuy99 |
| tagline     | 블로그의 부제목을 설정합니다. | Game Developer |
| description | 블로그 링크 공유 시 나타나는 설명글을 설정합니다. | Hello, there |
| url | `Repository` 의 이름과 똑같이 설정해주세요. | https://username.github.io |
| theme_mode | 블로그의 테마를 설정합니다. | light, dark |
| avatar | 사이드바의 프로필을 설정합니다. | /assets/img/avatar.jpg |
| social_preview_image | 링크 공유 시 나타나는 사진을 설정합니다. | /asssets/img/seo.jpg |
| paginate | 한 페이지에 나타낼 최대 게시물 수를 설정합니다. | 5 |

<br>

<!--------------------------------------header2-------------------------------------->
## _**2. 색상 변경**_
---

테마의 색깔, 특히 사이드바와 관련된 옵션들은 아래의 스크립트에서 수정을 할 수 있습니다.

* _`_dark.scss`_ : 다크모드일 때의 색상을 설정합니다.
* _`light.scss`_ : 라이트모드일 때의 색상을 설정합니다.
* _`_sidebar.scss`_ : 라이트모드/다크모드의 공통설정입니다.

```bash
--site-title-color     : 제목 색상
--site-subtitle-color  : 부제목 색상
--sidebar-bg           : 사이드바 배경화면 색상 or 사진으로 설정
--sidebar-border-color : 사이드바 ↔ 메인뷰 테두리 색상
--sidebar-muted-color  : 활성화 되지 않은 카테고리들의 아이콘 및 글씨 색상
--sidebar-active-color : 활성화 된 카테고리의 아이콘 및 글씨 색상
--sidebar-hover-bg     : 마우스 커서를 위로 올렸을 때의 색상
-sidebar-btn-bg        : 좌측 하단의 아이콘 배경 색상
--sidebar-btn-color    : 좌측 하단의 아이콘 색상
--avatar-border-color  : 프로필 사진 테두리 색상
```
{: file='_light.scss / _dark.scss'}


다른 `scss` 파일에서도 이보다 많은 설정들을 할 수 있으니, 하나씩 바꿔보시면서 취향껏 설정해주시면 됩니다.

<br>

아래는 제가 알아본 추가적인 설정들이니, 참고해보시길 바랍니다.

<br>

### _**사이드바 배경사진**_

```css
/* Line : 19 */
background: url('/assets/img/background.jpg');
```
{: file='_sass/layout/_sidebar.scss'}

<br>

참고로 _`_light.scss`_ 와 _`_dark.scss`_ 에서 각각 설정하면, 모드에 따라 배경사진을 다르게 설정할 수 있습니다.

```css
/* Line : 38 */
background: url('/assets/img/light_background.jpg');
```
{: file='_sass/layout/_light.scss'}

```css
/* Line : 38 */
background: url('/assets/img/dark_background.jpg');
```
{: file='_sass/layout/_dark.scss'}

<br>

### _**코드블럭 버튼색상 변경**_

제 블로그의 코드블럭은 좌측 위의 버튼색상이 `MacOS` 스타일처럼 빨, 노, 초 로 되어있습니다.

이와 같이 설정하려면 아래와 같이 변경해주시면 됩니다.

```bash
/* Line : 140 */
background-color: #EB6150; /* default : var(--code-header-muted-color); */
box-shadow: (v.$code-dot-size + v.$code-dot-gap) 0 0#F3B63B,
    /* default : var(--code-header-muted-color), */
    (v.$code-dot-size + v.$code-dot-gap) * 2 0 0
          #54BB44; /* var(--code-header-muted-color); */
```
{: file='_sass/base/_syntax.scss'}

<br>

색상 커스터마이징은 여기까지 간단히 다뤄보고, 추후 다른 커스터마이징에 대해서도 다뤄보겠습니다.

<br>

## _**3. GitHub 배포 및 설정**_
---

**1. Github Push**

여기까지 진행되신 분들께서는 `VS Code` 에서 `bash` 를 실행시켜주세요.

터미널창을 켜는 단축키는 <KBD>Ctrl</KBD> + <KBD>Shift</KBD> + <KBD>~</KBD> 입니다.

우측의 <KBD>+</KBD> 기호 오른쪽에 아래 화살표를 클릭 후 `bash` 를 선택해주시면 됩니다.

![VS Code](bash.jpg)

이후 아래와 같이 명령어를 실행해주시면 배포는 끝납니다.

```bash
$ git add .
$ git commit -m 'Commit'
$ git push origin main
```

<br>

**4. Jekyll.yml 추가**

일단, `GitHub` 사이트로 접속 후, 본인의 `github.io` `Repository` 로 들어가줍니다.

이후 우측 위의 메뉴 중 `Repository` 의 `Settings` 로 들어가주세요.

![GitHub](github_settings.jpg)

<br>

좌측의 `Pages` 로 들어가서 `Source` 를 `GitHub Actions` 로 변경해줍니다.

![GitHub](github_pages.jpg)

<br>

그럼 아래와 같이 `Jekyll` 이라는 박스가 나타나는데, <KBD>Configure</KBD> 을 클릭해줍니다.

![GitHub](configure.jpg)

<br>

이후 우측 위의 <KBD>Commit changes...</KBD> 를 클릭해주시면 됩니다.

![GitHub](commit_changes_1.jpg)

<br>

한번 더 <KBD>Commit changes...</KBD> 를 클릭해주시면 끝이 납니다.

![GitHub](commit_changes_2.jpg)

<br>

`VS Code` 로 돌아와서, 터미널에 아래의 명령어를 입력해주세요.

```bash
git pull
```

_`username.github.io/.github/workflows`_ 에 _`jekyll.yml`_ 이 추가되었다면 성공입니다.

![GitHub](jekyll_yml.jpg)

<br>

## _**4. Jekyll.yml 수정**_

`VS Code` 를 통해 `jekyll.yml` 파일을 열어줍니다.

그리고 설치하신 `Ruby` 버전에 맞게 수정해주신 뒤, 제가 표시해둔 주석 처리된 내용을 추가해주세요.

```yml
# Line : 29
.
.
jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Setup Ruby
        uses: ruby/setup-ruby@v1
        with:
          ruby-version: '3.2' # Ruby 버전에 맞게 수정
          bundler-cache: true
          cache-version: 0
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: npm build                   # 이 부분을 추가해주세요.
        run: npm install && npm run build # 이 부분을 추가해주세요.
.
.
```
{: file='.github/workflows/jekyll.yml'}

<br>

## _**마치며**_
---

이로써 간단한 커스터마이징 후 `Github` 에 `Push` 후, 추가적인 설정까지 끝마쳤습니다.

다음엔 `favicon` 설정에 대해 다뤄보겠습니다.

긴 글 읽어주셔서 감사합니다.


### _**Reference**_

* [프로의 개발일지](https://devpro.kr/)
* [하얀눈길 블로그](https://www.irgroup.org/)
* [걷는보리](https://walkbori.com/)