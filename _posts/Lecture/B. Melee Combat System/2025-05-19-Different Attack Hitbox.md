---
title: 9. Different Attack Hitbox
description: Game Development
author: Minssuy99
date: 2025-05-19 14:00:00 +0900
categories: [Lecture, Melee Combat System]
tags: [C#, Unity]
pin: false
math: true
permalink : /posts/Different-Attack-Hitbox
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

현재 발에는 콜라이더가 없어서, 적과 부딪혔을 때 피격 애니메이션이 동작하지 않는다.

![img](2025-05-18-result.gif){: .shadow .rounded-10 .w-75 w="1200"}
_No Collision_

<br>

이를 해결하기 위해 손과 발에 콜라이더를 추가하고, 스크립트를 수정한다.

<br>


## _**Hitbox**_
---

아래와 같이 손과 발에 콜라이더를 추가한 뒤, 태그와 레이어를 검과 똑같이 설정한다.

![img](2025-05-19-addcollider.jpg){: .shadow .rounded-10 .w-75 w="1200"}

<br>

### _**ScriptableObject**_

`ScriptableObject` 스크립트에 아래의 내용을 추가한다.

```csharp
public class AttackData : ScriptableObject
{
    [field : SerializeField] public string AnimName { get; private set; }
    [field : SerializeField] public float ImpactStartTime { get; private set; }
    [field : SerializeField] public float ImpactEndTime { get; private set; }
    [field : SerializeField] public AttackHitbox HitboxToUse { get; private set; } // Add
}
public enum AttackHitbox // Add
{
    LEFTHAND,
    RIGHTHAND,
    LEFTFOOT,
    RIGHTFOOT,
    SWORD,
}
```
{: file="AttackData.cs"}

<br>

추가한 `enum` 타입을 각 인스턴스의 인스펙터에서 지정한다.

![img](2025-05-19-Inspector.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_Hitbox To Use_

<br>

### _**Collider**_

플레이어 캐릭터에 부착된 `animator` 컴포넌트에서 `Avatar` 에 할당된 `Humanoid` 의 원하는 신체부위

즉, 손과 발의 `Transform` 의 정보를 가진 객체의 `Collider` 컴포넌트를 참조한다.

`GetComponent<T>()` 는 특정 `Component`가 붙어 있는 `GameObject` 에서 원하는 타입의 컴포넌트를 가져올 수 있다.

```csharp
SphereCollider leftHandCollider, rightHandCollider, leftFootCollider, rightFootCollider;

private void Start()
{
    if (sword != null)
    {
        swordCollider = sword.GetComponent<BoxCollider>();
        leftHandCollider = animator.GetBoneTransform(HumanBodyBones.LeftHand).GetComponent<SphereCollider>();
        rightHandCollider = animator.GetBoneTransform(HumanBodyBones.RightHand).GetComponent<SphereCollider>();
        leftFootCollider = animator.GetBoneTransform(HumanBodyBones.LeftFoot).GetComponent<SphereCollider>();
        rightFootCollider = animator.GetBoneTransform(HumanBodyBones.RightFoot).GetComponent<SphereCollider>();

        DisableAllHitboxes(); // 기본적으로 모든 콜라이더가 꺼져있도록 함.
    }
}
```
{: file="MeleeFighter.cs"}

<br>

`Start` 함수가 길어지기도 하고, 재사용하기 위해서 메서드로 분리한다.

```csharp
void DisableAllHitboxes()
{
    swordCollider.enabled = false;
    leftHandCollider.enabled = false;
    rightHandCollider.enabled = false;
    leftFootCollider.enabled = false;
    rightFootCollider.enabled = false;
}
```
{: file="MeleeFighter.cs"}

<br>

각 애니메이션에 따른 부위별 콜라이더를 활성화 하기 위해 `switch` 문을 작성한다.

```csharp
void EnableHitbox(AttackData attack)
{
    switch (attack.HitboxToUse)
    {
        case AttackHitbox.LEFTHAND:
            leftHandCollider.enabled = true;
            break;
        case AttackHitbox.RIGHTHAND:
            rightHandCollider.enabled = true;
            break;
        case AttackHitbox.LEFTFOOT:
            leftFootCollider.enabled = true;
            break;
        case AttackHitbox.RIGHTFOOT:
            rightFootCollider.enabled = true;
            break;
        case AttackHitbox.SWORD:
            swordCollider.enabled = true;
            break;
        default:
            break;
    }
}
```
{: file="MeleeFighter.cs"}

<br>

기존의 검의 콜라이더를 직접 켰다 끄는 것이 아닌, 각 애니메이션의 콜라이더가 활성화되도록 수정한다.

```csharp
IEnumerator Attack()
{
  .
  .
  if (attackState == AttackState.WINDUP)
  {
      if (nomalizedTime >= attacks[comboCount].ImpactStartTime)
      {
          attackState = AttackState.IMPACT;
          EnableHitbox(attacks[comboCount]); // 각 애니메이션의 콜라이더 활성화
      }
  }
  else if (attackState == AttackState.IMPACT)
  {
      if (nomalizedTime >= attacks[comboCount].ImpactEndTime)
      {
          attackState = AttackState.COOLDOWN;
          DisableAllHitboxes(); // 모든 콜라이더 비활성화
      }
  }
  .
  .
}
```
{: file="MeleeFighter.cs"}

<details>
    <summary><i>전체코드</i></summary>
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
    [SerializeField] private List<AttackData> attacks;
    [SerializeField] private GameObject sword;
    
    BoxCollider swordCollider;
    SphereCollider leftHandCollider, rightHandCollider, leftFootCollider, rightFootCollider;
    
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
            leftHandCollider = animator.GetBoneTransform(HumanBodyBones.LeftHand).GetComponent<SphereCollider>();
            rightHandCollider = animator.GetBoneTransform(HumanBodyBones.RightHand).GetComponent<SphereCollider>();
            leftFootCollider = animator.GetBoneTransform(HumanBodyBones.LeftFoot).GetComponent<SphereCollider>();
            rightFootCollider = animator.GetBoneTransform(HumanBodyBones.RightFoot).GetComponent<SphereCollider>();

            DisableAllHitboxes();
        }
    }

    AttackState attackState;
    bool doCombo;
    int comboCount = 0;

    public bool InAction { get; private set; } = false;
    
    public void TryToAttack()
    {
        if (!InAction)
        {
            StartCoroutine(Attack());
        }
        else if (attackState == AttackState.IMPACT || attackState == AttackState.COOLDOWN)
        {
            doCombo = true;
        }
    }

    IEnumerator Attack()
    {
        InAction = true;
        attackState = AttackState.WINDUP;
        
        animator.CrossFade(attacks[comboCount].AnimName, 0.2f);
        yield return null;

        var animState = animator.GetNextAnimatorStateInfo(1);

        float timer = 0f;

        while (timer <= animState.length)
        {
            timer += Time.deltaTime;
            float nomalizedTime = timer / animState.length;

            if (attackState == AttackState.WINDUP)
            {
                if (nomalizedTime >= attacks[comboCount].ImpactStartTime)
                {
                    attackState = AttackState.IMPACT;
                    EnableHitbox(attacks[comboCount]);
                }
            }
            else if (attackState == AttackState.IMPACT)
            {
                if (nomalizedTime >= attacks[comboCount].ImpactEndTime)
                {
                    attackState = AttackState.COOLDOWN;
                    DisableAllHitboxes();
                }
            }
            else if (attackState == AttackState.COOLDOWN)
            {
                if (doCombo)
                {
                    doCombo = false;
                    comboCount = (comboCount + 1) % attacks.Count;

                    StartCoroutine(Attack());
                    yield break;
                }
            }
            
            yield return null;
        }

        attackState = AttackState.IDLE;
        comboCount = 0;
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
        
        yield return new WaitForSeconds(animState.length * 0.8f);
        
        InAction = false;
    }

    void EnableHitbox(AttackData attack)
    {
        switch (attack.HitboxToUse)
        {
            case AttackHitbox.LEFTHAND:
                leftHandCollider.enabled = true;
                break;
            case AttackHitbox.RIGHTHAND:
                rightHandCollider.enabled = true;
                break;
            case AttackHitbox.LEFTFOOT:
                leftFootCollider.enabled = true;
                break;
            case AttackHitbox.RIGHTFOOT:
                rightFootCollider.enabled = true;
                break;
            case AttackHitbox.SWORD:
                swordCollider.enabled = true;
                break;
            default:
                break;
        }
    }

    void DisableAllHitboxes()
    {
        swordCollider.enabled = false;
        leftHandCollider.enabled = false;
        rightHandCollider.enabled = false;
        leftFootCollider.enabled = false;
        rightFootCollider.enabled = false;
    }
}
```
{: file="MeleeFighter.cs"}

</div>
</details>

<br>


## _**Enemy**_
---

현재 검 오브젝트에는 콜라이더 및 리지드바디가 부착되어 있다.

유니티에서는 콜라이더끼리 충돌 검사가 일어나기 위해선, 최소한 한쪽에 리지드바디가 있어야한다.

<br>

현재 손과 발에 콜라이더를 추가하였는데, 리지드바디를 전부 추가하지 않고 적 오브젝트에 `CharacterController` 를 추가한다.

`CharacterController` 는 리지드바디가 없더라도 콜라이더와 부딪혔을 때 충돌검사가 가능하다.

![img](2025-05-19-CharacterController.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_CharacterController_


<br>

## _**Result**_
---

발차기 공격을 했을 때, 우측의 인스펙터 창에서 `Sphere Collider` 가 `On/Off` 되는 모습을 확인할 수 있다.

![img](2025-05-19-HitboxResult.gif){: .shadow .rounded-10 .w-75 w="1200"}
_Result_