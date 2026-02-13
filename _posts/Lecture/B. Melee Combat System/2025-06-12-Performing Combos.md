---
title: 16. Performing Combos
description: Game Development
author: Minssuy99
date: 2025-06-12 20:00:00 +0900
categories: [Lecture, Melee Combat System]
tags: [C#, Unity]
pin: false
math: true
permalink : /posts/Performing-Combos
published: false
# mermaid: false
image:
  path: 'thumbnail.jpg'
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAFCAYAAAB8ZH1oAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsQAAA7EAZUrDhsAAADFSURBVBhXDcuxTsJAHIDx7393LW3vKgRjCYiDAzFOJuICg4smvohPweRbMTLBapwdkIRAoomNJEgK0rPDt/0+sc555xzdi4w4iambkiQJ8brBfldw1UtZrTYoAY5Hz+FPY1RAURyqSkSFNJsp80XOd/6LBEHkh8MenVbK69uS0ei5Opd0zzOsjdHGICh0ZOOXE6e5vTlju91hylPGkzU188V09s78Y02nHSIPj/d+cNfGRvCz+aygZpELT/0Gl9cZIgovin+18zpj6AM9igAAAABJRU5ErkJggg==
  # alt:
media_subpath: '/assets/img/posts/Lecture/B. Melee Combat System'
---
<!---------------------------------------Header-------------------------------------->

## _**목표**_
---

현재 적은 아래와 같이 플레이어를 향해 단일 공격만 수행하고 있다.

단조로운 전투를 개선하기 위해 적이 단일 공격과 다양한 콤보 공격 사이에서 무작위로 전환할 수 있도록 구현한다.

![img](2025-06-08-result.gif){: .shadow .rounded-10 .w-75 w="1200"}
_Problem_

## _**구현**_
---

콤보 공격을 구현하려면 공격 함수를 여러 번 호출하기만 하면 된다.

`AttackState.cs` 의 `Attack` 코루틴에 `comboCount` 매개변수를 추가한다.

```csharp
IEnumerator Attack(int comboCount = 1) // 콤보 카운트 매개변수 추가 (기본값 1)
{
    isAttacking = true;
    enemy.Animator.applyRootMotion = true;
    
    // 첫 번째 공격 실행
    enemy.Fighter.TryToAttack();

    for (int i = 0; i < comboCount; i++)
    {
        // 이전 공격이 쿨다운 상태가 될 때까지 대기
        yield return new WaitUntil(() => enemy.Fighter.AttackState == AttackStates.COOLDOWN);
        // 다음 공격 실행
        enemy.Fighter.TryToAttack();
    }

    // 모든 공격이 완료될 때까지 대기
    yield return new WaitUntil(() => enemy.Fighter.AttackState == AttackStates.IDLE);
    
    enemy.Animator.applyRootMotion = false;
    isAttacking = false;
    
    enemy.ChangeState(EnemyStates.RetreatAfterAttack);
}
```
{: file="AttackState.cs"}

콤보 공격을 구현하기 위해 `comboCount` 매개변수를 추가하여 실행할 공격 횟수를 지정한다.

각 공격 사이에 `WaitUntil` 을 사용하여 이전 공격이 쿨다운 상태에 도달할 때까지 대기한다.

<br>

`MeleeFighter.cs` 에서 공격 리스트에 접근할 수 있도록 프로퍼티를 추가한다.

외부에서 공격 리스트에 접근할 수 있도록 프로퍼티를 추가하여 적이 가진 공격 수를 확인할 수 있게 한다.

```csharp
public class MeleeFighter : MonoBehaviour
{
    // 공격 리스트를 외부에서 읽을 수 있도록 프로퍼티 추가
    public List<AttackData> Attacks => attacks;
    
    // 기존 코드...
}
```
{: file="MeleeFighter.cs"}

<br>

`AttackState.cs` 의 `Execute` 메서드에서 무작위 콤보 카운트를 생성하여 공격을 실행한다.

```csharp
public override void Execute()
{
    if (isAttacking) return;
    
    enemy.NavAgent.SetDestination(enemy.Target.transform.position);

    if (Vector3.Distance(enemy.Target.transform.position, enemy.transform.position) <= attackDistance + 0.03f)
    {
        // 무작위 콤보 카운트로 공격 실행
        StartCoroutine(Attack(Random.Range(0,enemy.Fighter.Attacks.Count + 1)));
    }
}
```
{: file="AttackState.cs"}

<br>

<details>
    <summary><i>AttackState.cs 전체코드</i></summary>
<div markdown ="1">

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Random = UnityEngine.Random;

public class AttackState : State<EnemyController>
{
    [SerializeField] private float attackDistance = 1f;

    private bool isAttacking;
    
    private EnemyController enemy;
    public override void Enter(EnemyController owner)
    {
        enemy = owner;

        enemy.NavAgent.stoppingDistance = attackDistance;
    }

    public override void Execute()
    {
        if (isAttacking) return;
        
        enemy.NavAgent.SetDestination(enemy.Target.transform.position);

        if (Vector3.Distance(enemy.Target.transform.position, enemy.transform.position) <= attackDistance + 0.03f)
        {
            StartCoroutine(Attack(Random.Range(0,enemy.Fighter.Attacks.Count + 1)));
        }
    }

    IEnumerator Attack(int comboCount = 1)
    {
        isAttacking = true;
        enemy.Animator.applyRootMotion = true;
        
        enemy.Fighter.TryToAttack();

        for (int i = 0; i < comboCount; i++)
        {
            yield return new WaitUntil(() => enemy.Fighter.AttackState == AttackStates.COOLDOWN);
            enemy.Fighter.TryToAttack();
        }
        yield return new WaitUntil(() => enemy.Fighter.AttackState == AttackStates.IDLE);
        
        enemy.Animator.applyRootMotion = false;
        isAttacking = false;
        
        enemy.ChangeState(EnemyStates.RetreatAfterAttack);
    }

    public override void Exit()
    {
        enemy.NavAgent.ResetPath();
    }
}
```
{: file="AttackState.cs"}

</div>
</details>

<details>
    <summary><i>MeleeFighter.cs 전체코드</i></summary>
<div markdown ="1">

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public enum AttackStates
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

    public AttackStates AttackState { get; private set; }
    bool doCombo;
    int comboCount = 0;

    public bool InAction { get; private set; } = false;
    
    public void TryToAttack()
    {
        if (!InAction)
        {
            StartCoroutine(Attack());
        }
        else if (AttackState == AttackStates.IMPACT || AttackState == AttackStates.COOLDOWN)
        {
            doCombo = true;
        }
    }

    IEnumerator Attack()
    {
        InAction = true;
        AttackState = AttackStates.WINDUP;
        
        animator.CrossFade(attacks[comboCount].AnimName, 0.2f);
        yield return null;

        var animState = animator.GetNextAnimatorStateInfo(1);

        float timer = 0f;

        while (timer <= animState.length)
        {
            timer += Time.deltaTime;
            float nomalizedTime = timer / animState.length;

            if (AttackState == AttackStates.WINDUP)
            {
                if (nomalizedTime >= attacks[comboCount].ImpactStartTime)
                {
                    AttackState = AttackStates.IMPACT;
                    EnableHitbox(attacks[comboCount]);
                }
            }
            else if (AttackState == AttackStates.IMPACT)
            {
                if (nomalizedTime >= attacks[comboCount].ImpactEndTime)
                {
                    AttackState = AttackStates.COOLDOWN;
                    DisableAllHitboxes();
                }
            }
            else if (AttackState == AttackStates.COOLDOWN)
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

        AttackState = AttackStates.IDLE;
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
        if(swordCollider != null)
            swordCollider.enabled = false;  
        
        if(leftHandCollider != null)
            leftHandCollider.enabled = false;
        
        if(rightHandCollider != null)
            rightHandCollider.enabled = false;
        
        if(leftFootCollider != null)
            leftFootCollider.enabled = false;
        
        if(rightFootCollider != null)
            rightFootCollider.enabled = false;
    }
    
    public List<AttackData> Attacks => attacks;
}
```
{: file="MeleeFighter.cs"}

</div>
</details>

<br>

## _**Result**_
---

적이 무작위로 단일 공격과 콤보 공격을 전환하여 더 다양한 전투를 볼 수 있게 되었다.

![img](2025-06-12-result.gif){: .shadow .rounded-10 .w-75 w="1200"}
_Result_