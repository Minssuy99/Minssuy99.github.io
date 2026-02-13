---
title: 6. Reacting to Attacks
description: Game Development
author: Minssuy99
date: 2025-05-15 15:30:00 +0900
categories: [Lecture, Melee Combat System]
tags: [C#, Unity]
pin: false
math: true
permalink : /posts/Reacting-to-Attack
published: false
# mermaid: false
image:
  path: 'thumbnail.jpg'
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAFCAYAAAB8ZH1oAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsQAAA7EAZUrDhsAAADFSURBVBhXDcuxTsJAHIDx7393LW3vKgRjCYiDAzFOJuICg4smvohPweRbMTLBapwdkIRAoomNJEgK0rPDt/0+sc555xzdi4w4ianbkiQJ8brBfldw1UtZrTYoAY5Hz+FPY1RAURyqSkSFNJsp80XOd/6LBEHkh8MenVbK69uS0ei5Opd0zzOsjdHGICh0ZOOXE6e5vTlju91hylPGkzU188V09s78Y02nHSIPj/d+cNfGRvCz+aygZpELT/0Gl9cZIgovin+18zpj6AM9igAAAABJRU5ErkJggg==
  # alt:
media_subpath: '/assets/img/posts/Lecture/B. Melee Combat System'
---
<!---------------------------------------Header-------------------------------------->

## _**Error**_
---

### _**Magenta Error in URP**_

프로젝트가 `URP` 이고 모델을 유니티에 `import` 했을 때, 마젠타 색상을 띌 수도 있다.

이러한 문제는 머티리얼과 셰이더와 관련된 문제이고, 크게 `3` 유형이 있다.

![img](2025-05-15-magenta.jpg){: width="450"}
_AKA. Unity Magenta_

<br>

**기본 셰이더와 `URP` 셰이더의 호환성 문제**

* `Built-in` 렌더 파이프라인에서 사용되던 `Standard Shader` 는 `URP` 에서는 더 이상 지원되지 않는다.
* `URP` 는 `Universal Render Pipeline/Lit` 같은 `URP` 전용 셰이더만 사용 가능하다.

**머티리얼 자동 변환이 누락됨**

* `Unity` 는 렌더 파이프라인을 `URP`로 설정할 때, 기존 머티리얼을 `URP` 용으로 자동 변환하지 않는다.
* 즉, 기존 머티리얼이 여전히 `Standard Shader` 를 사용하고 있다면, 수동으로 바꿔줘야 한다.

**셰이더가 유실되었거나 손상된 경우**

* 모델을 임포트할 때, 셰이더 파일이 함께 따라오지 않았거나, `Unity` 에서 읽지 못하는 포맷이라면<br>`Shader missing` 상태가 되어 마젠타가 표시된다.

<br>

### _**Solution**_

임포트한 모델의 머티리얼을 선택하고, `Shader` 를 다음 중 하나로 수동 변경한다:

* Universal Render Pipeline/Lit
* Universal Render Pipeline/Simple Lit


또는 `Unity` 메뉴에서 자동 변환 기능을 사용할 수도 있다:<br>
`Edit → Render Pipeline → Universal Render Pipeline → Upgrade Project Materials to URP Materials`{: .filepath}

`2022.3.61f1` 버전 기준으로는 아래와 같다.<br>
`Edit → Rendering → Materials → Convert Selected Built-in Materials to URP`{: .filepath}

<br>

## _**Attack**_
---

### _**Sword**_

`Unity Asset Store` 등을 통해 검 모델을 `import` 한다.

캐릭터의 본 `Bone` 중 오른손을 찾아서 오브젝트의 자식으로 설정한다.

![img](2025-05-15-Add Sword.jpg){: width="650"}

<br>

플레이어와 적의 공격 상호작용을 위해서 `Layer` 를 아래와 같이 추가한다.

* `Player` : 플레이어 캐릭터용
* `Enemy` : 적 캐릭터용
* `Player Hitbox` : 플레이어 무기용
* `Enemy Hitbox` : 적 무기용

![img](2025-05-15-Layers.jpg){: width="650"}

<br>

검 오브젝트의 태그와 `Layers` 를 아래의 사진과 같이 설정한다.

![img](2025-05-15-Sword.jpg){: width="650"}

<br>

부착한 상태에서 공격모션을 진행하면 아래와 같은 모습이 된다.

![img](2025-05-15-Attack.gif)
_Equip Sword_

<br>

### _**Enemy**_

`Unity Asset Store` 등을 통해 적 모델을 `import` 한다.

플레이어 캐릭터와 마찬가지로, 캡슐 콜라이더와 전투 구현 스크립트를 컴포넌트로 추가한다.

적 캐릭터의 `Layer` 은 `Enemy` 로 설정한다.

이와 마찬가지로 플레이어 캐릭터도 `Layer` 를 `Player` 로 설정한다.

![img](2025-05-15-Enemy.jpg){: width="650"}

<br>

피격 애니메이션을 `Slash` 와 마찬가지로 트랜지션을 만들어 `Empty` 에 연결한다.

![img](2025-05-15-animator.jpg){: width="650"}

<br>

`Edit - Project Settings - Physics`{: .filepath} 로 들어가서 충돌 매트릭스를 아래와 같이 설정한다.

이렇게 설정함으로써 불피요한 충돌 감지를 방지하여 게임을 최적화 할 수 있다.

![img](2025-05-15-Physics.jpg){: width="450"}

<br>

### _**Code**_

지난 글에 작성한 `IEnumerator Attack()` 코루틴과 마찬가지로, 피격 애니메이션도 아래와 같이 작성한다.

`OnTrigger` 을 호출시키기 위해, 검의 콜라이더에 `isTrigger` 을 활성화시켜줘야 하고

충돌할 물체 중 하나는 `Rigidbody` 를 컴포넌트로 가지고 있어야한다.

```csharp
private void OnTriggerEnter(Collider other)
{
    // 공격모션이 실행 중이 아니며, 히트박스 태그(검) 과 충돌했다면
    if (other.tag == "Hitbox" && !InAction)
    {
        StartCoroutine(PlayerHitReaction()); // 피격 애니메이션 코루틴 실행
    }
}

IEnumerator PlayerHitReaction() // 공격 애니메이션과 동일한 로직
{
    InAction = true;
    animator.CrossFade("SwordImpact", 0.2f);
    yield return null;

    var animState = animator.GetNextAnimatorStateInfo(1);
    
    yield return new WaitForSeconds(animState.length);
    
    InAction = false;
}
```
{: file="MeleeFighter.cs"}

<details>
    <summary><i>전체 코드</i></summary>
<div markdown ="1">

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class MeleeFighter : MonoBehaviour
{
    private Animator animator;

    private void Awake()
    {
        animator = GetComponent<Animator>();
    }

    public bool InAction { get; private set; } = false;
    
    public void TryToAttack()
    {
        if (!InAction)
        {
            StartCoroutine(Attack());
        }
    }

    IEnumerator Attack()
    {
        InAction = true;
        animator.CrossFade("Slash", 0.2f);
        yield return null;

        var animState = animator.GetNextAnimatorStateInfo(1);
        
        yield return new WaitForSeconds(animState.length);
        
        InAction = false;
    }

    private void OnTriggerEnter(Collider other)
    {
        if (other.tag == "Hitbox" && !InAction)
        {
            StartCoroutine(PlayerHitReaction());
        }
    }
    
    IEnumerator PlayerHitReaction()
    {
        InAction = true;
        animator.CrossFade("SwordImpact", 0.2f);
        yield return null;

        var animState = animator.GetNextAnimatorStateInfo(1);
        
        yield return new WaitForSeconds(animState.length);
        
        InAction = false;
    }
}
```
{: file="MeleeFighter.cs"}

</div>
</details>

<br>

## _**Result**_
---

![img](2025-05-15-Result.gif){: width="1200" height="630"}

<br>

## _**Reference**_
---
* [Unity Docs- Introduction to collision](https://docs.unity3d.com/2023.2/Documentation/Manual/CollidersOverview.html?ref=blog.joe-brothers.com)
* [Unity Docs - Interaction between collider types](https://docs.unity3d.com/2023.2/Documentation/Manual/collider-types-interaction.html)