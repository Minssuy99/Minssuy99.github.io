---
title: "[Windows] Logi Options+ 와 PowerToys를 이용한 작업환경 빠르게 세팅하기"
description: Quick Workspace Setup
author: Minssuy99
date: 2025-08-03 21:00:00 +0900
categories: [Tip, Windows]
tags: [Windows, Logitech, PowerToys]
pin: false
permalink: /posts/Quick-Workspace-Setup/
# math: false
# mermaid: false
image:
  path: 'thumbnail.jpg'
  lqip: data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEASABIAAD/4QAiRXhpZgAATU0AKgAAAAgAAQESAAMAAAABAAEAAAAAAAD/7QAsUGhvdG9zaG9wIDMuMAA4QklNA+0AAAAAABAASAAAAAEAAQBIAAAAAQAB/9sAQwACAQECAQECAgICAgICAgMFAwMDAwMGBAQDBQcGBwcHBgcHCAkLCQgICggHBwoNCgoLDAwMDAcJDg8NDA4LDAwM/9sAQwECAgIDAwMGAwMGDAgHCAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwM/8AAEQgABQAKAwEiAAIRAQMRAf/EAB8AAAEFAQEBAQEBAAAAAAAAAAABAgMEBQYHCAkKC//EALUQAAIBAwMCBAMFBQQEAAABfQECAwAEEQUSITFBBhNRYQcicRQygZGhCCNCscEVUtHwJDNicoIJChYXGBkaJSYnKCkqNDU2Nzg5OkNERUZHSElKU1RVVldYWVpjZGVmZ2hpanN0dXZ3eHl6g4SFhoeIiYqSk5SVlpeYmZqio6Slpqeoqaqys7S1tre4ubrCw8TFxsfIycrS09TV1tfY2drh4uPk5ebn6Onq8fLz9PX29/j5+v/EAB8BAAMBAQEBAQEBAQEAAAAAAAABAgMEBQYHCAkKC//EALURAAIBAgQEAwQHBQQEAAECdwABAgMRBAUhMQYSQVEHYXETIjKBCBRCkaGxwQkjM1LwFWJy0QoWJDThJfEXGBkaJicoKSo1Njc4OTpDREVGR0hJSlNUVVZXWFlaY2RlZmdoaWpzdHV2d3h5eoKDhIWGh4iJipKTlJWWl5iZmqKjpKWmp6ipqrKztLW2t7i5usLDxMXGx8jJytLT1NXW19jZ2uLj5OXm5+jp6vLz9PX29/j5+v/aAAwDAQACEQMRAD8A/LdbxbexuIja28slxsCTuX8y2wwJ2AMF+YfKd6tweMHmmiPI6/pRRRawH//Z
  # alt:
media_subpath: '/assets/img/posts/Tip/Windows'
---
<!--------------------------------------yaml-------------------------------------->
## _**문제**_
---

매번 켜는 `VS Code` 와 크롬창들... 매번 트는 유튜브의 플레이리스트, 그리고 `GPT` 와 `stackoverflow`.

지금까지 이렇게 해왔던 나로서는 딱히 불편함을 느끼진 못했지만, 단순히 이 생각이 들었다.

'_항상 같은 세팅과 같은 배치를 하는데, 이걸...내가 매번 해야할까?_ '

![img](2025-08-03-desktop.png){: .shadow .rounded-10 .w-75 w="1200"}
_Always the same setting..._

이를 해결하기 위해서 본래 내가 사용하는 프로그램들을 사용하여 클릭 한번만으로 작업환경을 전부 세팅되도록 해보았다.

### _**시작하기에 앞서...**_

> 본문은 `Logi Options+` 프로그램을 지원하는 제품 사용자 기준으로 작성되었음을 알립니다.
{: .prompt-warning }

{% include embed/bookmark.html
url="https://support.logi.com/hc/ko/articles/25528092585879-Supported-devices-Logi-Options"
title="Logi Options+ 를 지원하는 제품 확인하기"
description="Logitech 지원 - Logitech 고객 지원에 오신 것을 환영합니다." %}

<br>

## _**PowerToys**_
---

### _**설치**_

`PowerToys` 프로그램이란, 마이크로소프트가 직접 개발한 고급 사용자용 유틸리티 모음이다.

쉽게 말해서, 복잡한 기능이 많지만 편리하고 유용한 기능들을 윈도우 환경에서 쓸 수 있는 프로그램이다.

설치방법은 윈도우에 이미 존재하는 `Microsoft Store` 을 통해서 설치할 수 있다.

![img](2025-08-03-microsoft store.png){: .shadow .rounded-10 .w-75 w="1200"}
_Microsoft Store_

스토어를 실행한 뒤, `Microsoft PowerToys` 를 검색 후 설치하면 된다.

혹시 스토어가 없는 분들이 계신다면, 아래의 링크를 통해 다운로드 받을 수 있다.

{% include embed/bookmark.html
url="https://apps.microsoft.com/detail/xp89dcgq3k6vld?hl=ko-KR&gl=KR"
title="Microsoft Store - Microsoft PowerToys"
description="Microsoft PowerToys is a set of utilities for power users to tune and streamline..." %}

<br>

### _**FancyZones**_

이 프로그램은 많은 기능들을 지원하는데, 내가 애용하는 기능은 `FancyZones`, 단 하나밖에 없다.

`FancyZones` 는 사용자 임의로 레이아웃을 설정하여, 그에 맞게 화면분할을 할 수 있는 기능이다. 

`Shift` 키를 누른 상태에서 창을 드래그하면 설정한 레이아웃의 가이드가 나타나고, 드래그를 놓으면 창의 크기가 맞춰지며 배치된다.

![img](2025-08-03-powertoys example.gif){: .shadow .rounded-10 .w-75 w="1200"}
_PowerToys' FancyZones_

<br>

이 기능을 사용하기 위해선 단순히 `FancyZones` 기능을 활성화만 시켜주면 된다.

![img](2025-08-03-activate.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_Activate_

이 글에선 레이아웃 편집에 대해 자세히 다루지 않는다.

하지만 사용자 레이아웃은 다루기 정말 쉬우니, 조금만 만져보면 금방 알 수 있다.

<br>

### _**작업 영역**_

추가로 `작업 영역` 이라는 기능도 활성화 하고, 이에 대한 설정도 진행한다.

![img](2025-08-03-activate2.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_Activate_

<br>

활성화를 했다면, `작업 영역` 기능 내부에 <kbd>편집기 시작</kbd> 을 클릭하여 설정에 진입한다.

![img](2025-08-03-work area1.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_Work Area_

<br>

그럼 아래와 같은 창이 나타나는데, 우측 상단의<kbd>+ 작업 영역 만들기</kbd> 버튼을 클릭한다.

![img](2025-08-03-work area2.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_Work Area_

<br>

버튼을 클릭하면, 화면의 중앙에 아래와 같은 창이 나타난다.

이 상태에서, <u>본인이 원하는 상태의 작업화면을 세팅을 한 뒤</u> <kbd>캡처</kbd> 버튼을 누른다.

![img](2025-08-03-work area3.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_Work Area_

나 같은 경우엔 아래의 사진처럼 `FancyZones` 를 활용해 작업환경을 내가 원하는 분할로 한 뒤에 캡처를 진행했다.

![img](2025-08-03-work area3-1.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_Work Area_

<br>

<kbd>캡처</kbd> 버튼을 누르면, 현재 화면에서 실행되고 있던 프로그램의 목록들이 나타난다.

여기서, 화면에 띄우고 싶지 않은 프로그램에 대해 우측의 <kbd>제거</kbd> 버튼을 눌러 목록에서 제거한다.

이후 우측 아래의 <kbd>작업 영역 저장</kbd> 을 눌러 마무리한다.

![img](2025-08-03-work area4.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_Work Area_

<br>

그럼 `작업 영역` 기능의 목록에 본인이 추가한 영역이 리스트로 나타나게 된다.

만든 영역에 대해 <kbd>시작</kbd> 버튼을 눌러 본인이 설정한 작업화면으로 배치가 되는지 확인해본다.

![img](2025-08-03-work area5.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_Work Area_

<br>

이와 같이 본인이 쓸 프로그램들이 원하는 화면분할로 나타난다면 `PowerToys` 의 설정은 끝났다.


![img](2025-08-03-work area6.gif){: .shadow .rounded-10 .w-75 w="1200"}
_Work Area_

<br>

## _**Logi Options+**_
---

나는 로지텍의 `Lift` 라는 버티컬 마우스를 사용하고 있어서 이 프로그램을 사용할 수 있다.

이 프로그램은 마우스 버튼의 커스텀 설정 뿐 아니라, `Smart Actions` 이라는 매크로 기능까지 지원한다.

`Smart Actions` 를 사용하기 위해선 `Logi Options+` 프로그램에 로그인이 되어있어야 한다.

<br>

로그인을 했다면, 우측 상단의 아이콘을 클릭하여 `Smart Actions` 기능을 확인할 수 있다.

![img](2025-08-03-smart actions.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_Smart Actions_

해당 기능으로 들어왔다면, 우측 상단의 <kbd>+ 생성</kbd> 버튼을 클릭하여 사용자화 할 수 있다.

![img](2025-08-03-generate.png){: .shadow .rounded-10 .w-75 w="1200"}
_Generate_

<br>

생성버튼을 누르면 아래와 같은 화면이 나타난다.

![img](2025-08-03-explain.png){: .shadow .rounded-10 .w-75 w="1200"}
_If and Then_

`If...` 란은 어떻게 이 매크로를 실행시킬 것인가에 대한 설정이다.

`25년 8월` 기준으로 현재 지원하는 트리거 방식은 `장치` 와 `바로가기` 가 있다.

<kbd>장치</kbd>는 마우스 또는 키보드의 버튼으로 실행시키는 방식이고, <kbd>바로가기</kbd>는 단축키로 실행하는 방식이다.

<br>

`Then...` 은 어떤 기능들을 수행할 것인가에 대한 설정이다.

위의 사진만 봐도 굉장히 직관적으로 설정이 되어있는 것을 확인할 수 있다.

<kbd>동작 추가</kbd> 버튼을 눌러보면 다양한 동작이 나타나게 된다.

<br>

여기서 사용할 것은 `키 입력`, `텍스트`, `지연` 뿐이다.

* `키 입력` : 녹화 시작을 누른 뒤, 마우스 또는 키보드를 입력하여 동작 매크로 추가
* `텍스트` : 어떤 텍스트를 작성할 것인지에 대한 입력
* `지연` : 다음 동작을 실행하기 전 얼마나 기다렸다가 진행할 것인지

여기서 중요한건 `지연` 인데, 이전행동이 제대로 끝나지도 않았는데 다음동작이 이루어지는 경우가 있다.

그래서 비정상적으로 동작하는 경우엔, 동작들 사이에 지연을 추가하면서 수정해보자.

<br>

일단 커스텀화를 진행하기 앞서, 아래 사진과 똑같이 추가 한 뒤에 설정하도록 한다.

![img](2025-08-03-then1.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_Then_

* _**키 입력 : Ctrl + Windows + \`(백틱)**_ `// 키 입력 시 동시에 입력해야함`
  * `작업 영역` 의 리스트 목록을 띄우는 단축키
* _**2초 지연**_
  * `작업 영역` 프로그램이 켜질 때 까지 여유시간 확보
* _**키 입력 : Tab 키 8회, 이후 Enter, 이후 `Alt + F4`**_
  * 설정한 `작업 영역` 의 `실행` 버튼을 누르기 위한 동작
  * Enter 로 실행 한 뒤, 띄워놓았던 `작업 영역` 창을 `Alt + F4` 로 즉시 닫음.
* _**4초 지연**_
  * `작업 영역` 에서 설정했던 프로그램들이 전부 켜질 때 까지 대기

![img](2025-08-03-then3.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_Tab x 8, Enter, Alt + F4 Setting_

<br>

이렇게 추가 했다면, 이후엔 본인이 원하는 방식으로 설정을 진행하면 된다.

나 같은 경우엔 아래의 사진과 같이 설정하였다.

![img](2025-08-03-then2.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_My Setting..._

<br>

## _**Result**_
___

본인이 설정한 트리거 키를 눌러 실행시켜보자.

![img](2025-08-03-result.gif){: .rounded-10 .w-75 .shadow w='1200' h='630' }
_Not double speed, it's macro_