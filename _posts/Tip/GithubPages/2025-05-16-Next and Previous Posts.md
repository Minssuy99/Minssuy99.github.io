---
title: "[Chirpy] 같은 카테고리 내에서 이전 글, 다음 글 이동"
description: GitHub Pages
author: Minssuy99
date: 2025-05-16 16:00:00 +0900
categories: [Tip, Github Pages]
tags: [Jekyll, Chirpy]
pin: false
# math: false
# mermaid: false
image:
  path: 'thumbnail.jpg'
  lqip: data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEAYABgAAD/2wBDAAIBAQIBAQICAgICAgICAwUDAwMDAwYEBAMFBwYHBwcGBwcICQsJCAgKCAcHCg0KCgsMDAwMBwkODw0MDgsMDAz/2wBDAQICAgMDAwYDAwYMCAcIDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAz/wAARCAAZADIDASIAAhEBAxEB/8QAGgABAAIDAQAAAAAAAAAAAAAAAAEHAgMICf/EACUQAAICAgEFAAEFAAAAAAAAAAECAAMEBREGBxITIVEIFCIxYf/EABcBAQEBAQAAAAAAAAAAAAAAAAACAQP/xAAeEQEAAgICAwEAAAAAAAAAAAABAAIDIRExQWFxgf/aAAwDAQACEQMRAD8A8y4iSiNY6qqszMeAAOSTESIlhdC9m9RstNq87qjqjF6bXY51XjgsrHPt15DM2Ui+JVVf12JWXP1/WWT1We5bETtD2yyO4zfu7qbMHdY2NnYfT+qyMvGydZiBaLnDNkGyxsqypbAykugSzIuqDFcSq+bWKja3ROuHDkzZK4cQ2tZADtXQHtZzyG8h8iWJ+q3uXqu7Pf3f7jQ9OaXpPRq1OBg6vVhGx6K8elKPP2Kqm5rGra1rWAZzZzwo4Va7mjycyMlGlmj41pE/E0/TTERE2TEEciIiJJdmtZyzF2byZif5E/kn8/7M8fKtxMkXU2203KxcWI5Vwx/s8j7z9P2a4iIA8RwIiIiIiIif/9k=
  # alt:
media_subpath: '/assets/img/posts/Tip/Github Pages'
---
<!--------------------------------------yaml-------------------------------------->
## _**카테고리가 늘어나며 생긴 불편함**_
---

포스트 아래쪽에는 <KBD>OLDER</KBD> 와 <KBD>NEWER</KBD> 라는 버튼을 통해 이전 글과 다음 글로 이동할 수 있다.

`Chirpy` 의 기본 세팅은 전체 글 기준에서의 이전 글과 다음 글이 링크 되어있다.

여러 주제의 글을 작성하다 보니, 같은 카테고리 내의 글이 링크가 되었으면 좋겠다는 생각이 들었다.

![img](etc/2025-05-16-원본.jpg)
_글의 주제가 따로 논다..._

<br>

## _**해결방법**_
---

이와 관련된 정보가 게시된 [블로그 글들(클릭)](#reference) 을 보았지만 다른 테마이거나 기존의 스타일에서

변형이 일어난 형태라서, 해당 게시글들을 참고하며 직접 수정을 진행하였다.

<br>

수정을 한 후의 모습과, 코드는 아래와 같다.

추가로, 타이틀 위의 `OLDER` 과 `NEWER` 표기 또한 `PREV` 와 `NEXT` 로 변경하였다.

![img](etc/2025-05-16-수정2.jpg)
_같은 카테고리, PREV 및 NEXT 로 표기 수정_

<br>

### _**Code**_

`_include/post-nav.html`{: .filepath} 경로로 들어간 뒤, 기존의 코드를 전부 지우고 아래의 코드를 붙여 넣어주면 된다.

{% raw %}
```csharp
{% assign cat = page.categories[1] %}
{% assign cat_list = site.categories[cat] %}
{% for post in cat_list %}
  {% if post.url == page.url %}
    {% assign prevIndex = forloop.index0 | minus: 1 %}
    {% assign nextIndex = forloop.index0 | plus: 1 %}
    {% if forloop.first == false %}
      {% assign next_post = cat_list[prevIndex] %}
    {% endif %}
    {% if forloop.last == false %}
      {% assign prev_post = cat_list[nextIndex] %}
    {% endif %}
    {% break %}
  {% endif %}
{% endfor %}

<div class="post-navigation d-flex justify-content-between">
  {% if prev_post %}
    <a
      href="{{ site.baseurl }}{{ prev_post.url }}"
      class="btn btn-outline-primary"
      aria-label="{{ site.data.locales[include.lang].post.button.previous }}"
    >
      <p>{{ prev_post.title }}</p>
    </a>
  {% else %}
    <div
      class="btn btn-outline-primary disabled"
      aria-label="{{ site.data.locales[include.lang].post.button.previous }}"
    >
      <p>-</p>
    </div>
  {% endif %}

  {% if next_post %}
    <a
      href="{{ site.baseurl }}{{ next_post.url }}"
      class="btn btn-outline-primary"
      aria-label="{{ site.data.locales[include.lang].post.button.next }}"
    >
      <p>{{ next_post.title }}</p>
    </a>
  {% else %}
    <div
      class="btn btn-outline-primary disabled"
      aria-label="{{ site.data.locales[include.lang].post.button.next }}"
    >
      <p>-</p>
    </div>
  {% endif %}
</div>
```
{: file="_include/post-nav.html"}
{% endraw %}

<details>
    <summary><i>원본코드</i></summary>
<div markdown ="1">

{% raw %}
```csharp
<!-- Navigation buttons at the bottom of the post. -->

<nav class="post-navigation d-flex justify-content-between" aria-label="Post Navigation">
  {% assign previous = site.data.locales[include.lang].post.button.previous %}
  {% assign next = site.data.locales[include.lang].post.button.next %}

  {% if page.previous.url %}
    <a
      href="{{ site.baseurl }}{{ page.previous.url }}"
      class="btn btn-outline-primary"
      aria-label="{{ previous }}"
    >
      <p>{{ page.previous.title }}</p>
    </a>
  {% else %}
    <div class="btn btn-outline-primary disabled" aria-label="{{ previous }}">
      <p>-</p>
    </div>
  {% endif %}

  {% if page.next.url %}
    <a
      href="{{ site.baseurl }}{{page.next.url}}"
      class="btn btn-outline-primary"
      aria-label="{{ next }}"
    >
      <p>{{ page.next.title }}</p>
    </a>
  {% else %}
    <div class="btn btn-outline-primary disabled" aria-label="{{ next }}">
      <p>-</p>
    </div>
  {% endif %}
</nav>
```
{: file="_include/post-nav.html"}
{% endraw %}

</div>
</details>

<br>

다음은 `OLDER` 과 `NEWER` 를 수정하기 위해서, 현재 본인이 사용하고 있는 언어의 파일을 수정해야한다.

본인 블로그의 기본언어는 루트폴더의 `./_config.yml`{: .filepath} 를 통해 확인할 수 있다.

```yml
plugins:
   - jekyll-target-blank

theme: jekyll-theme-chirpy

lang: ko-KR # 블로그 기본언어

timezone: Asia/Seoul

title: Minssuy99
```
{: file="./_config.yml"}

<br>

기본 언어를 찾았다면, `_data/locales/설정언어.yml`{: .filepath} 으로 들어간다.

필자 같은 경우는 `ko-KR` 이 기본언어 이기 때문에, `_data/locales/ko-KR.yml`{: .filepath} 을 수정한다.

```yml
# ----- Posts related labels -----
post:
  written_by: By
  posted: Posted
  updated: Updated
  words: words
  pageview_measure: views
  read_time:
    unit: min
    prompt: read
  relate_posts: Further Reading
  share: Share
  button:
    next: Next # Newer → Next 로 수정
    previous: Prev # Older → Prev 로 수정
    copy_code:
      succeed: Copied!
    share_link:
      title: Copy link
      succeed: Link copied successfully!
```
{: file="_data/locales/ko-KR.yml"}

<br>

여기까지 수정하였다면, 기존의 스타일은 유지된 채로 같은 카테고리 간의 링크가 연결된 모습을 확인할 수 있다.

<br>

## _**Reference**_
---
* [Jihwan Blog - \[Github Blog\] 같은 카테고리 내 이전글, 다음글 이동](https://jihwan98.github.io/posts/%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC-%EB%82%B4-%EC%9D%B4%EC%A0%84%EA%B8%80,-%EB%8B%A4%EC%9D%8C%EA%B8%80/)
* [공부하는 식빵맘 - [Github 블로그] 같은 카테고리 내에서의 이전글, 다음글 이동](https://ansohxxn.github.io/blog/prevnext/)
* [Jekyll Topics - How to Link to Next and Previous Posts for Same Blog Category?](https://talk.jekyllrb.com/t/how-to-link-to-next-and-previous-posts-for-same-blog-category/629/1)
