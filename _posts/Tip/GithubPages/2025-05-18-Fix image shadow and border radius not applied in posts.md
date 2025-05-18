---
title: "[Chirpy] 포스트 내 이미지의 그림자 및 둥근 모서리 미적용 버그 수정"
description: Fix Image Shadow and Border Radius Not Applied in Posts
author: Minssuy99
date: 2025-05-18 01:00:00 +0900
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
# _**이미지 그림자 및 라운딩 미적용 되는 현상**_
---

`Chirpy` 의 튜토리얼 포스트를 보면 아래와 같은 설명이 기재되어 있다.

![img](etc/2025-05-18-explain-shadow.jpg){: .shadow}
_How to apply shadow to image_

<br>

그리고 다른 게시글의 작성된 마크다운을 보면 아래와 같은 형식으로 작성되어 있다.

```markdown
![img](/assets/img/image.jpg){: .light .w-75 .shadow .rounded-10 w='700' h='550' }
```
{: file="Text and Typography"}

<br>

그리고 이것이 적용된 게시물의 사진은 아래와 같다.

![img](etc/2025-05-18-non-apply-image.jpg)
_...this is false_

<br>

[게시물](https://chirpy.cotes.page/posts/text-and-typography/#darklight-mode--shadow) 을 보면 알테지만 애초에 둥근 이미지를 삽입하였고 `.rounded` 는 적용되지 않았고,

`.shadow` 도 마찬가지로 미적용 되어있는 모습을 확인할 수 있다. (내가 눈이 나쁜 줄 알았다...)

이 `2` 개의 버그를 수정해보도록 하자.

<br>

<!------------------------------------------------------------------------------------------->

## _**이미지 라운딩 수정**_
---

> 2025년 5월 12일 이후 개설한 블로그는 버그가 수정되었음. 
{: .prompt-info }

코드가 길어서 토글로 접어두었다.

`./_includes/refactor.content.html`{: .filepath} 경로에 해당 파일을 수정한다.

기존 코드를 전부 지운 뒤, 수정 코드를 복사 및 붙여넣기를 하면 이미지 라운딩이 정상적으로 동작한다.

이와 관련된 정보는 아래의 [링크](#reference) 참조.

<details>
    <summary><i>기존 코드</i></summary>
<div markdown ="1">

{% raw %}
```html
<!-- Refactor the HTML structure -->

{% assign _content = include.content %}

<!--
  In order to allow a wide table to scroll horizontally,
  we suround the markdown table with `<div class="table-wrapper">` and `</div>`
-->

{% if _content contains '<table' %}
  {% assign _content = _content
    | replace: '<table', '<div class="table-wrapper"><table'
    | replace: '</table>', '</table></div>'
    | replace: '<code><div class="table-wrapper">', '<code>'
    | replace: '</table></div></code>', '</table></code>'
  %}
{% endif %}

<!--
  Fixed kramdown code highlight rendering:
  https://github.com/penibelst/jekyll-compress-html/issues/101
  https://github.com/penibelst/jekyll-compress-html/issues/71#issuecomment-188144901
-->

{% if _content contains '<pre class="highlight">' %}
  {% assign _content = _content
    | replace: '<div class="highlight"><pre class="highlight"><code', '<div class="highlight"><code'
    | replace: '</code></pre></div>', '</code></div>'
  %}
{% endif %}

<!-- Change the icon of checkbox -->

{% if _content contains '<input type="checkbox"' %}
  {% assign _content = _content
    | replace: '<input type="checkbox" class="task-list-item-checkbox" disabled="disabled" checked="checked" />',
      '<i class="fas fa-check-circle fa-fw checked"></i>'
    | replace: '<input type="checkbox" class="task-list-item-checkbox" disabled="disabled" />',
      '<i class="far fa-circle fa-fw"></i>'
  %}
{% endif %}

<!-- Handle images -->

{% assign IMG_TAG = '<img ' %}

{% if _content contains IMG_TAG %}
  {% assign _img_content = null %}
  {% assign _img_snippets = _content | split: IMG_TAG %}

  {% for _img_snippet in _img_snippets %}
    {% if forloop.first %}
      {% assign _img_content = _img_snippet %}
      {% continue %}
    {% endif %}

    {% assign _left = _img_snippet | split: '>' | first %}
    {% assign _right = _img_snippet | remove: _left %}

    {% unless _left contains 'src=' %}
      {% continue %}
    {% endunless %}

    {% assign _left = _left | remove: ' /' | replace: ' w=', ' width=' | replace: ' h=', ' height=' %}
    {% assign _attrs = _left | split: '" ' %}

    {% assign _src = null %}
    {% assign _lqip = null %}
    {% assign _class = null %}

    {% for _attr in _attrs %}
      {% unless _attr contains '=' %}
        {% continue %}
      {% endunless %}

      {% assign _pair = _attr | split: '="' %}
      {% capture _key %}{{ _pair | first }}{% endcapture %}
      {% capture _value %}{{ _pair | last | remove: '"' }}{% endcapture %}

      {% case _key %}
        {% when 'src' %}
          {% assign _src = _value %}
        {% when 'lqip' %}
          {% assign _lqip = _value %}
        {% when 'class' %}
          {% assign _class = _value %}
      {% endcase %}
    {% endfor %}

    <!-- take out classes -->
    {% if _class %}
      {% capture _old_class %}class="{{ _class }}"{% endcapture %}
      {% assign _left = _left | remove: _old_class %}
    {% endif %}

    {% assign _final_src = null %}
    {% assign _lazyload = true %}

    {%- capture _img_url -%}
      {% include media-url.html src=_src subpath=page.media_subpath %}
    {%- endcapture -%}

    {% assign _path_prefix = _img_url | remove: _src %}

    {% unless _src contains '//' %}
      {% assign _final_src = _path_prefix | append: _src %}
      {% assign _src_alt = 'src="' | append: _path_prefix %}
      {% assign _left = _left | replace: 'src="', _src_alt %}
    {% endunless %}

    {% if _lqip %}
      {% assign _lazyload = false %}
      {% assign _class = _class | append: ' blur' %}

      {% unless _lqip contains 'data:' %}
        {% assign _lqip_alt = 'lqip="' | append: _path_prefix %}
        {% assign _left = _left | replace: 'lqip="', _lqip_alt %}
      {% endunless %}

      <!-- add image placeholder -->
      {% assign _left = _left | replace: 'src=', 'data-src=' | replace: ' lqip=', ' data-lqip="true" src=' %}

    {% else %}
      {% assign _class = _class | append: ' shimmer' %}
    {% endif %}

    <!-- lazy-load images -->
    {% if _lazyload %}
      {% assign _left = _left | append: ' loading="lazy"' %}
    {% endif %}

    {% if page.layout == 'home' %}
      <!-- create the image wrapper -->
      {% assign _wrapper_start = '<div class="preview-img ' | append: _class | append: '">' %}

      {% assign _img_content = _img_content | append: _wrapper_start %}
      {% assign _right = _right | prepend: '></div' %}

    {% else %}
      <!-- make sure the `<img>` is wrapped by `<a>` -->
      {% assign _parent = _right | slice: 1, 4 %}

      {% if _parent == '</a>' %}
        <!-- add class to exist <a> tag -->
        {% assign _size = _img_content | size | minus: 1 %}
        {% capture _class %}
          class="img-link{% unless _lqip %} shimmer{% endunless %}"
        {% endcapture %}
        {% assign _img_content = _img_content | slice: 0, _size | append: _class | append: '>' %}

      {% else %}
        <!-- create the image wrapper -->
        {% assign _wrapper_start = _final_src
          | default: _src
          | prepend: '<a href="'
          | append: '" class="popup img-link '
          | append: _class
          | append: '">'
        %}

        {% assign _img_content = _img_content | append: _wrapper_start %}
        {% assign _right = '></a' | append: _right %}
      {% endif %}
    {% endif %}

    <!-- combine -->
    {% assign _img_content = _img_content | append: IMG_TAG | append: _left | append: _right %}
  {% endfor %}

  {% if _img_content %}
    {% assign _content = _img_content %}
  {% endif %}
{% endif %}

<!-- Add header for code snippets -->

{% if _content contains '<div class="highlight"><code>' %}
  {% assign _code_spippets = _content | split: '<div class="highlight"><code>' %}
  {% assign _new_content = '' %}

  {% for _snippet in _code_spippets %}
    {% if forloop.last %}
      {% assign _new_content = _new_content | append: _snippet %}

    {% else %}
      {% assign _left = _snippet | split: '><' | last %}

      {% if _left contains 'file="' %}
        {% assign _label_text = _left | split: 'file="' | last | split: '"' | first %}
        {% assign _label_icon = 'far fa-file-code fa-fw' %}
      {% else %}
        {% assign _lang = _left | split: 'language-' | last | split: ' ' | first %}
        {% capture _label_text %}{% include language-alias.html language=_lang %}{% endcapture %}
        {% assign _label_icon = 'fas fa-code fa-fw small' %}
      {% endif %}

      {% capture _label %}
        <span data-label-text="{{ _label_text | strip }}"><i class="{{ _label_icon }}"></i></span>
      {% endcapture %}

      {% assign _new_content = _new_content
        | append: _snippet
        | append: '<div class="code-header">'
        | append: _label
        | append: '<button aria-label="copy" data-title-succeed="'
        | append: site.data.locales[include.lang].post.button.copy_code.succeed
        | append: '"><i class="far fa-clipboard"></i></button></div>'
        | append: '<div class="highlight"><code>'
      %}
    {% endif %}
  {% endfor %}

  {% assign _content = _new_content %}
{% endif %}

<!-- Create heading anchors -->

{% assign heading_levels = '2,3,4,5' | split: ',' %}
{% assign _heading_content = _content %}

{% for level in heading_levels %}
  {% assign mark_start = '<h' | append: level | append: ' id="' %}
  {% assign mark_end = '</h' | append: level | append: '>' %}

  {% if _heading_content contains mark_start %}
    {% assign _new_content = null %}
    {% assign heading_snippets = _heading_content | split: mark_start %}

    {% for snippet in heading_snippets %}
      {% if forloop.first %}
        {% assign _new_content = snippet %}
        {% continue %}
      {% endif %}

      {% assign id = snippet | split: '"' | first %}
      {% assign anchor = '<a href="#'
        | append: id
        | append: '" class="anchor text-muted"><i class="fas fa-hashtag"></i></a>'
      %}

      {% assign left = snippet | split: mark_end | first %}
      {% assign right = snippet | slice: left.size, snippet.size %}
      {% assign left = left | replace_first: '">', '"><span class="me-2">' | append: '</span>' %}

      {% assign _new_content = _new_content | append: mark_start | append: left | append: anchor | append: right %}
    {% endfor %}

    {% assign _heading_content = _new_content %}
  {% endif %}
{% endfor %}

{% assign _content = _heading_content %}

<!-- return -->
{{ _content }}
```
{% endraw %}
{: file="test.cs"}

</div>
</details>


<!------------------------------------------------------------------------------------------------->
<details>
    <summary><i>수정 코드</i></summary>
<div markdown ="1">

{% raw %}
```html
<!-- Refactor the HTML structure -->

{% assign _content = include.content %}

<!--
  In order to allow a wide table to scroll horizontally,
  we suround the markdown table with `<div class="table-wrapper">` and `</div>`
-->

{% if _content contains '<table' %}
  {% assign _content = _content
    | replace: '<table', '<div class="table-wrapper"><table'
    | replace: '</table>', '</table></div>'
    | replace: '<code><div class="table-wrapper">', '<code>'
    | replace: '</table></div></code>', '</table></code>'
  %}
{% endif %}

<!--
  Fixed kramdown code highlight rendering:
  https://github.com/penibelst/jekyll-compress-html/issues/101
  https://github.com/penibelst/jekyll-compress-html/issues/71#issuecomment-188144901
-->

{% if _content contains '<pre class="highlight">' %}
  {% assign _content = _content
    | replace: '<div class="highlight"><pre class="highlight"><code', '<div class="highlight"><code'
    | replace: '</code></pre></div>', '</code></div>'
  %}
{% endif %}

<!-- Change the icon of checkbox -->

{% if _content contains '<input type="checkbox"' %}
  {% assign _content = _content
    | replace: '<input type="checkbox" class="task-list-item-checkbox" disabled="disabled" checked="checked" />',
      '<i class="fas fa-check-circle fa-fw checked"></i>'
    | replace: '<input type="checkbox" class="task-list-item-checkbox" disabled="disabled" />',
      '<i class="far fa-circle fa-fw"></i>'
  %}
{% endif %}

<!-- Handle images -->

{% assign IMG_TAG = '<img ' %}

{% if _content contains IMG_TAG %}
  {% assign _img_content = null %}
  {% assign _img_snippets = _content | split: IMG_TAG %}

  {% for _img_snippet in _img_snippets %}
    {% if forloop.first %}
      {% assign _img_content = _img_snippet %}
      {% continue %}
    {% endif %}

    {% assign _left = _img_snippet | split: '>' | first %}
    {% assign _right = _img_snippet | remove: _left %}

    {% unless _left contains 'src=' %}
      {% continue %}
    {% endunless %}

    {% assign _left = _left | remove: ' /' | replace: ' w=', ' width=' | replace: ' h=', ' height=' %}
    {% assign _attrs = _left | split: '" ' %}

    {% assign _src = null %}
    {% assign _lqip = null %}
    {% assign _class = null %}

    {% for _attr in _attrs %}
      {% unless _attr contains '=' %}
        {% continue %}
      {% endunless %}

      {% assign _pair = _attr | split: '="' %}
      {% capture _key %}{{ _pair | first }}{% endcapture %}
      {% capture _value %}{{ _pair | last | remove: '"' }}{% endcapture %}

      {% case _key %}
        {% when 'src' %}
          {% assign _src = _value %}
        {% when 'lqip' %}
          {% assign _lqip = _value %}
        {% when 'class' %}
          {% assign _class = _value %}
      {% endcase %}
    {% endfor %}

    <!-- take out classes -->
    {% assign _wrapper_class = '' %}

    {% if _class %}
      {% assign _remain = _class %}
      {% assign _class_array = _class | split: ' ' %}

      {% for c in _class_array %}
        {% assign _pick = false %}

        {% case c %}
          {% when 'preview-img', 'normal', 'left', 'right', 'light', 'dark' %}
            {% assign _pick = true %}
          {% else %}
            {% if c contains '-' %}
              {% assign start = c | split: '-' | first %}
              {% if start == 'w' %}
                {% assign _pick = true %}
              {% endif %}
            {% endif %}
        {% endcase %}

        {% if _pick %}
          {% assign _remain = _remain | remove: c | strip %}
          {% assign _wrapper_class = _wrapper_class | append: ' ' | append: c %}
        {% endif %}
      {% endfor %}

      {% unless _wrapper_class == '' %}
        {% capture _old_class %}class="{{ _class }}"{% endcapture %}
        {% assign _left = _left | remove: _old_class %}
        {% unless _remain == '' %}
          {% capture _new_class %}class="{{ _remain }}"{% endcapture %}
          {% assign _left = _left | append: _new_class %}
        {% endunless %}
      {% endunless %}
    {% endif %}

    {% assign _final_src = null %}
    {% assign _lazyload = true %}

    {%- capture _img_url -%}
      {% include media-url.html src=_src subpath=page.media_subpath %}
    {%- endcapture -%}

    {% assign _path_prefix = _img_url | remove: _src %}

    {% unless _src contains '//' %}
      {% assign _final_src = _path_prefix | append: _src %}
      {% assign _src_alt = 'src="' | append: _path_prefix %}
      {% assign _left = _left | replace: 'src="', _src_alt %}
    {% endunless %}

    {% if _lqip %}
      {% assign _lazyload = false %}
      {% assign _wrapper_class = _wrapper_class | append: ' blur' %}

      {% unless _lqip contains 'data:' %}
        {% assign _lqip_alt = 'lqip="' | append: _path_prefix %}
        {% assign _left = _left | replace: 'lqip="', _lqip_alt %}
      {% endunless %}

      <!-- add image placeholder -->
      {% assign _left = _left | replace: 'src=', 'data-src=' | replace: ' lqip=', ' data-lqip="true" src=' %}

    {% else %}
      {% assign _wrapper_class = _wrapper_class | append: ' shimmer' %}
    {% endif %}

    <!-- lazy-load images -->
    {% if _lazyload %}
      {% assign _left = _left | append: ' loading="lazy"' %}
    {% endif %}

    {% if page.layout == 'home' %}
      <!-- create the image wrapper -->
      {% assign _wrapper_start = '<div class="preview-img ' | append: _class | append: '">' %}

      {% assign _img_content = _img_content | append: _wrapper_start %}
      {% assign _right = _right | prepend: '></div' %}

    {% else %}
      <!-- make sure the `<img>` is wrapped by `<a>` -->
      {% assign _parent = _right | slice: 1, 4 %}

      {% if _parent == '</a>' %}
        <!-- add class to exist <a> tag -->
        {% assign _size = _img_content | size | minus: 1 %}
        {% capture _class %}
          class="img-link{% unless _lqip %} shimmer{% endunless %}"
        {% endcapture %}
        {% assign _img_content = _img_content | slice: 0, _size | append: _class | append: '>' %}

      {% else %}
        <!-- create the image wrapper -->
        {% assign _wrapper_start = _final_src
          | default: _src
          | prepend: '<a href="'
          | append: '" class="popup img-link'
          | append: _wrapper_class
          | append: '">'
        %}

        {% assign _img_content = _img_content | append: _wrapper_start %}
        {% assign _right = '></a' | append: _right %}
      {% endif %}
    {% endif %}

    <!-- combine -->
    {% assign _img_content = _img_content | append: IMG_TAG | append: _left | append: _right %}
  {% endfor %}

  {% if _img_content %}
    {% assign _content = _img_content %}
  {% endif %}
{% endif %}

<!-- Add header for code snippets -->

{% if _content contains '<div class="highlight"><code>' %}
  {% assign _code_spippets = _content | split: '<div class="highlight"><code>' %}
  {% assign _new_content = '' %}

  {% for _snippet in _code_spippets %}
    {% if forloop.last %}
      {% assign _new_content = _new_content | append: _snippet %}

    {% else %}
      {% assign _left = _snippet | split: '><' | last %}

      {% if _left contains 'file="' %}
        {% assign _label_text = _left | split: 'file="' | last | split: '"' | first %}
        {% assign _label_icon = 'far fa-file-code fa-fw' %}
      {% else %}
        {% assign _lang = _left | split: 'language-' | last | split: ' ' | first %}
        {% capture _label_text %}{% include language-alias.html language=_lang %}{% endcapture %}
        {% assign _label_icon = 'fas fa-code fa-fw small' %}
      {% endif %}

      {% capture _label %}
        <span data-label-text="{{ _label_text | strip }}"><i class="{{ _label_icon }}"></i></span>
      {% endcapture %}

      {% assign _new_content = _new_content
        | append: _snippet
        | append: '<div class="code-header">'
        | append: _label
        | append: '<button aria-label="copy" data-title-succeed="'
        | append: site.data.locales[include.lang].post.button.copy_code.succeed
        | append: '"><i class="far fa-clipboard"></i></button></div>'
        | append: '<div class="highlight"><code>'
      %}
    {% endif %}
  {% endfor %}

  {% assign _content = _new_content %}
{% endif %}

<!-- Create heading anchors -->

{% assign heading_levels = '2,3,4,5' | split: ',' %}
{% assign _heading_content = _content %}

{% for level in heading_levels %}
  {% assign mark_start = '<h' | append: level | append: ' id="' %}
  {% assign mark_end = '</h' | append: level | append: '>' %}

  {% if _heading_content contains mark_start %}
    {% assign _new_content = null %}
    {% assign heading_snippets = _heading_content | split: mark_start %}

    {% for snippet in heading_snippets %}
      {% if forloop.first %}
        {% assign _new_content = snippet %}
        {% continue %}
      {% endif %}

      {% assign id = snippet | split: '"' | first %}
      {% assign anchor = '<a href="#'
        | append: id
        | append: '" class="anchor text-muted"><i class="fas fa-hashtag"></i></a>'
      %}

      {% assign left = snippet | split: mark_end | first %}
      {% assign right = snippet | slice: left.size, snippet.size %}
      {% assign left = left | replace_first: '">', '"><span class="me-2">' | append: '</span>' %}

      {% assign _new_content = _new_content | append: mark_start | append: left | append: anchor | append: right %}
    {% endfor %}

    {% assign _heading_content = _new_content %}
  {% endif %}
{% endfor %}

{% assign _content = _heading_content %}

<!-- return -->
{{ _content }}
```
{% endraw %}
{: file="test.cs"}

</div>
</details>

<br>

## _**이미지 그림자 추가**_
---

구글에 검색해보았지만, 이에 대해 언급한 글이 없었고, 이슈가 된 것 같지도 않아서 직접 수정을 진행했다.

`./_sass/base/_base.scss`{: .filepath} 경로에 해당 파일을 수정한다.

`_base.scss` 파일의 최하단에 아래의 내용을 추가한다.

```css
.
.
#tail-wrapper {
  @include bp.xxxl {
    padding-right: 4.5rem !important;
  }

  > :not(script) {
    margin-top: 3rem;
  }
}

/* --- 이미지 그림자 설정, 아래의 코드 추가 --- */
img.shadow {
  @extend %rounded;
  box-shadow: 0 0.4rem 0.8rem var(--img-shadow-color) !important;
}
```
{: file="_base.scss"}

<br>

이후, 라이트 모드와 다크모드 각각의 테마에 따른 색상설정을 위해 각 파일에 변수를 추가한다.

`./_sass/themes/_light.scss`{: .filepath}

```scss
/* Posts */
--toc-highlight: #0550ae;
--toc-popup-border-color: lightgray;
.
.
.
--prompt-danger-icon-color: #df3c30;
--img-shadow-color : rgb(0, 0, 0, 1); // 라이트모드 이미지 그림자 색상 변수 추가
```
{: file="_light.scss"}

<br>

`./_sass/themes/_dark.scss`{: .filepath}

```scss
/* Posts */
--toc-highlight: rgb(205, 92, 92); // rgb(116, 178, 243);
--toc-popup-border-color: #373737;
.
.
.
--prompt-danger-icon-color: #cd0202;
--img-shadow-color : rgba(186, 191, 196, 0.3); // 다크모드 이미지 그림자 색상 변수 추가
```
{: file="_dark.scss"}

<br>

## _**Result**_
---

여기까지 진행 했다면, 아래의 이미지처럼 라운딩 및 각 테마별로 그림자 색상이 바뀌는 것을 확인할 수 있다.

원래 안되던걸 되게 바꾼 것 뿐이라서, 사용방법은 본래의 방식대로 그대로 적용하면 된다.

사진을 클릭하면 원본의 이미지와 같이 각진 모습으로 보여진다.

```markdown
![img](/assets/img/Test Image.jpg){: .shadow .rounded-10 w='700' h='550' }
```

![img](etc/2025-05-18-test image.jpg){: .shadow .rounded-10 w='700' h='550' }
_Test Image.jpg_

<br>

## _**Reference**_
---
* [jekyll-theme-chirpy Repository's Issues - Border Radius for Images is not working as expected #2331](https://github.com/cotes2020/jekyll-theme-chirpy/issues/2331)
