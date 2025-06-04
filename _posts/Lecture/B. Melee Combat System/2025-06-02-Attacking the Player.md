---
title: 14. Attacking the Player
description: Game Development
author: Minssuy99
date: 2025-06-02 19:00:00 +0900
categories: [Lecture, Melee Combat System]
tags: [C#, Unity]
pin: false
math: true
permalink : /posts/Attacking-the-Player
published: false
# mermaid: false
image:
  path: 'thumbnail.jpg'
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAFCAYAAAB8ZH1oAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsQAAA7EAZUrDhsAAADFSURBVBhXDcuxTsJAHIDx7393LW3vKgRjCYiDAzFOJuICg4smvohPweRbMTLBapwdkIRAoomNJEgK0rPDt/0+sc555xzdi4w4ianbkiQJ8brBfldw1UtZrTYoAY5Hz+FPY1RAURyqSkSFNJsp80XOd/6LBEHkh8MenVbK69uS0ei5Opd0zzOsjdHGICh0ZOOXE6e5vTlju91hylPGkzU188V09s78Y02nHSIPj/d+cNfGRvCz+aygZpELT/0Gl9cZIgovin+18zpj6AM9igAAAABJRU5ErkJggg==
  # alt:
media_subpath: '/assets/img/posts/Lecture/B. Melee Combat System'
---
<!---------------------------------------Header-------------------------------------->

## _**Flow**_
---

현재 적 오브젝트는 플레이어와 거리가 가까우면 회전을 한다.

지금은 엘든링 같은 악랄한 소울류를 만드는 것이 아니기 때문에, 전투는 한번에 한명만 공격하도록 구현한다.

![img](2025-06-02-example.gif){: .shadow .rounded-10 .w-75 w="1200"}
_소울류에서 볼 수 있는 다굴빵 [(출처)](https://gall.dcinside.com/mgallery/board/view/?id=aoegame&no=20426726)_





## _**Result**_
---

장애물과 충돌이 생겼을 때, 회전상태일땐 반드시 회전만을 하고, 회전상태가 끝나면 플레이어와 가까워진다.

또한 진행방향에 따라 애니메이션이 실행되는 모습을 확인할 수 있다.

![img](2025-06-01-result.gif){: .shadow .rounded-10 .w-75 w="1200"}
_Result_

<br>

## _**Reference**_
---

* [Unity Docs - Vector3.Dot](https://docs.unity3d.com/2022.3/Documentation/ScriptReference/Vector3.Dot.html)
* [Unity Docs - Vector3.Cross](https://docs.unity3d.com/2022.3/Documentation/ScriptReference/Vector3.Cross.html)


