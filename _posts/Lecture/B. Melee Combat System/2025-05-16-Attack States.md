---
title: 7. Attack States
description: Game Development
author: Minssuy99
date: 2025-05-16 11:30:00 +0900
categories: [Lecture, Melee Combat System]
tags: [C#, Unity]
pin: false
math: true
permalink : /posts/Attack-States
# mermaid: false
image:
  path: 'thumbnail.jpg'
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAFCAYAAAB8ZH1oAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsQAAA7EAZUrDhsAAADFSURBVBhXDcuxTsJAHIDx7393LW3vKgRjCYiDAzFOJuICg4smvohPweRbMTLBapwdkIRAoomNJEgK0rPDt/0+sc555xzdi4w4ianbkiQJ8brBfldw1UtZrTYoAY5Hz+FPY1RAURyqSkSFNJsp80XOd/6LBEHkh8MenVbK69uS0ei5Opd0zzOsjdHGICh0ZOOXE6e5vTlju91hylPGkzU188V09s78Y02nHSIPj/d+cNfGRvCz+aygZpELT/0Gl9cZIgovin+18zpj6AM9igAAAABJRU5ErkJggg==
  # alt:
media_subpath: '/assets/img/posts/Lecture/B. Melee Combat System'
---
<!---------------------------------------Header-------------------------------------->

## _**Trouble**_
---

현재 문제는 플레이어가 장비하고 있는 검이 적 오브젝트에 닿이기만 해도

적 오브젝트는 콜라이더가 호출되며 타격 애니메이션이 나타나게 된다.

![img](2025-05-16-Trouble.gif){: width="1200" height="630"}


이를 해결하기 위해, 공격 애니메이션을 크게 `3` 가지 상태로 구분한다.

* `Wind Up` : 공격을 하기 전 준비 단계
* `Impact` : 실제로 칼을 휘둘러 타격하는 단계
* `Cooldown` : 휘두른 뒤, 플레이어가 다시 자세를 잡는 단계

이런 상태를 구분한 상태에서, 콜라이더를 `Impact` 상태일 때만 활성화 하여 해결한다.

<br>

## _**Code**_
---

위에서 동작을 구분한대로, `enum` 타입을 정의한다.

```csharp
public enum AttackState
{
    IDLE,
    WINDUP,
    IMPACT,
    COOLDOWN,
}
```
{: file="MeleeFighter.cs}

<br>

이 상태에서, 공격 애니메이션이 어느 시간동안 `IMPACT` 를 동작하는지 확인한다.

`Impact` 를 시작한 시점은 대략 `0.33` 초이고, 끝나는 시점은 대략 `0.55` 초 이다.

![img](2025-05-16-animation.gif){: width="600"}
_Start : 0.33 / End : 0.55_

<br>

공격 애니메이션을 실행하기 전의 `enumType` 을 `WINDUP` 으로 지정한다.

애니메이션을 통해 알아낸 시간을 일단 하드코딩으로 변수를 초기화 한다.

```csharp
IEnumerator Attack()
{
    attackState = AttackState.WINDUP;

    float impactStartTime = 0.33f;
    float impactEndTime = 0.55f;
    .
    .
}
```
{: file="MeleeFighter.cs}

<br>

애니메이션의 시간을 체크하기 위한 타이머 변수를 선언하고, 위의 변수로 초기화 한 시간을 넘어가면

`AttackState` 상태를 변경해주는 로직을 작성한다.

```csharp
float timer = 0f; // 타이머 선언

while (timer <= animState.length) // 애니메이션 종료 시 반복문 종료
{
    timer += Time.deltaTime;
    
    // 시간 정규화. 0~1 사이의 값 할당됨.
    float nomalizedTime = timer / animState.length;

    if (attackState == AttackState.WINDUP)
    {
        // 정규화된 시간이 임팩트 시작 시간에 도달하면
        if (nomalizedTime >= impactStartTime)
        {
            // 상태를 임팩트 단계로 변경
            attackState = AttackState.IMPACT;
            
            // 무기 콜라이더 활성화
            swordCollider.enabled = true;
        }
    }
    else if (attackState == AttackState.IMPACT)
    {
        // 정규화된 시간이 임팩트 종료 시간에 도달하면
        if (nomalizedTime >= impactEndTime)
        {
            // 상태를 쿨다운 단계로 변경
            attackState = AttackState.COOLDOWN;
            
            // 무기 콜라이더 비활성화 (데미지 판정 종료)
            swordCollider.enabled = false;
        }
    }
    else if (attackState == AttackState.COOLDOWN)
    {
        // TODO : 콤보 공격 로직 구현 예정
    }
    
    // 다음 프레임까지 대기
    yield return null;
}

// 애니메이션이 완료되면 상태를 IDLE 상태로 변경
attackState = AttackState.IDLE;
```
{: file="MeleeFighter.cs}

<br>

무기 콜라이더의 정보를 받기 위한 변수를 선언한다.

```csharp
[SerializeField] private GameObject sword;
BoxCollider swordCollider;

private void Start()
{
    if (sword != null)
    {
        swordCollider = sword.GetComponent<BoxCollider>(); // 무기 콜라이더 참조
        swordCollider.enabled = false; // 콜라이더 비활성화
    }
}
```
{: file="MeleeFighter.cs}

<details>
    <summary><i>전체 코드</i></summary>
<div markdown ="1">

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public enum AttackState
{
    IDLE,
    WINDUP,
    IMPACT,
    COOLDOWN,
}

public class MeleeFighter : MonoBehaviour
{
    [SerializeField] private GameObject sword;
    
    BoxCollider swordCollider;
    
    private Animator animator;

    private void Awake()
    {
        animator = GetComponent<Animator>();
    }

    private void Start()
    {
        if (sword != null)
        {
            swordCollider = sword.GetComponent<BoxCollider>();
            swordCollider.enabled = false;
        }
    }

    AttackState attackState;

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
        attackState = AttackState.WINDUP;

        float impactStartTime = 0.33f;
        float impactEndTime = 0.55f;
        
        animator.CrossFade("Slash", 0.2f);
        yield return null;

        var animState = animator.GetNextAnimatorStateInfo(1);

        float timer = 0f;

        while (timer <= animState.length)
        {
            timer += Time.deltaTime;
            float nomalizedTime = timer / animState.length;

            if (attackState == AttackState.WINDUP)
            {
                if (nomalizedTime >=impactStartTime)
                {
                    attackState = AttackState.IMPACT;
                    swordCollider.enabled = true;
                }
            }
            else if (attackState == AttackState.IMPACT)
            {
                if (nomalizedTime >= impactEndTime)
                {
                    attackState = AttackState.COOLDOWN;
                    swordCollider.enabled = false;
                }
            }
            else if (attackState == AttackState.COOLDOWN)
            {
                // TODO : Handle combos
            }
            
            yield return null;
        }

        attackState = AttackState.IDLE;
        
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

플레이어 캐릭터의 검이 적 오브젝트에 닿이더라도 타격 애니메이션이 더이상 발생하지 않는다.

`Attack State` 를 `public` 으로 잠시 설정 후, 우측의 인스펙터 창에서 상태의 변화를 확인할 수 있다.

![img](2025-05-16-Result.gif){: width="1200" height="630"}
_Check Attack State in Inspector_


<br>