---
title: 15. Retreating after Attack
description: Game Development
author: Minssuy99
date: 2025-06-08 21:00:00 +0900
categories: [Lecture, Melee Combat System]
tags: [C#, Unity]
pin: false
math: true
permalink : /posts/Retreating-after-Attack
published: false
# mermaid: false
image:
  path: 'thumbnail.jpg'
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAFCAYAAAB8ZH1oAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsQAAA7EAZUrDhsAAADFSURBVBhXDcuxTsJAHIDx7393LW3vKgRjCYiDAzFOJuICg4smvohPweRbMTLBapwdkIRAoomNJEgK0rPDt/0+sc555xzdi4w4ianbkiQJ8brBfldw1UtZrTYoAY5Hz+FPY1RAURyqSkSFNJsp80XOd/6LBEHkh8MenVbK69uS0ei5Opd0zzOsjdHGICh0ZOOXE6e5vTlju91hylPGkzU188V09s78Y02nHSIPj/d+cNfGRvCz+aygZpELT/0Gl9cZIgovin+18zpj6AM9igAAAABJRU5ErkJggg==
  # alt:
media_subpath: '/assets/img/posts/Lecture/B. Melee Combat System'
---
<!---------------------------------------Header-------------------------------------->

## _**Problem**_
---

현재는 아래의 사진과 같이, 여러 적이 있음에도 적들 중 한명만 플레이어를 공격하고 있다.

적이 공격을 했다면 뒤로 물러나게 하고, 다른 리스트에 있는 적이 플레이어를 공격하도록 구현한다.

![img](2025-06-06-result.gif){: .shadow .rounded-10 .w-75 w="1200"}
_Problem_

## _**구현**_
---

후퇴 애니메이션의 클립을 아래와 같이 블렌드 트리에 추가한다.

![img](2025-06-08-Animator.gif){: .shadow .rounded-10 .w-75 w="1200"}
_Animator_

<br>

후퇴를 시키기 위해 후퇴 상태 스크립트를 작성한다.

이 스크립트도 마찬가지로 상태 스크립트이기 때문에, `EnemyController` 의 `enum` 및 리스트에 추가해준다.

```csharp
public class RetreatAfterAttackState : State<EnemyController>
{
    [SerializeField] private float backwardWalkSpeed = 1.5f; // 후퇴 속도
    [SerializeField] private float distanceToRetreat = 3f; // 후퇴 완료 거리
    private EnemyController enemy;

    public override void Enter(EnemyController owner)
    {
        enemy = owner; // 적 컨트롤러 참조 저장
    }

    public override void Execute()
    {
        // 플레이어와의 거리가 후퇴 완료 거리에 도달하면 전투 이동 상태로 복귀
        if (Vector3.Distance(enemy.transform.position, enemy.Target.transform.position) >= distanceToRetreat)
        {
            enemy.ChangeState(EnemyStates.CombatMovement);
            return;
        }
        
        // 플레이어를 향한 벡터 계산
        Vector3 vecToTarget = enemy.Target.transform.position - enemy.transform.position;

        // NavAgent.Move를 사용하여 반대 방향으로 이동 (장애물 회피 포함)
        enemy.NavAgent.Move(-vecToTarget.normalized * (backwardWalkSpeed * Time.deltaTime));

        // Y축 회전만 허용 (수평면에서만 회전) 
        vecToTarget.y = 0f;

        // 부드러운 회전으로 항상 플레이어를 바라보도록 설정
        transform.rotation = Quaternion.RotateTowards(transform.rotation, Quaternion.LookRotation(vecToTarget), 500 * Time.deltaTime);
    }
}
```
{: file="RetreatAfterAttackState.cs"}


<br>

추가로 `AttackState.cs` 에 아래의 내용을 추가한다.

```csharp
public override void Exit()
{
    // NavAgent 경로 초기화 - 상태 전환 시 이전 목적지 정보 제거
    enemy.NavAgent.ResetPath();
}
```
{: file="AttackState.cs"}

공격 상태에서 다른 상태로 전환될 때, `NavMeshAgent` 의 이동을 강제로 중단시켜야 한다.

`AttackState` 에서는 `SetDestination` 으로 공격 대상까지의 경로를 미리 설정해두고 있다.

이 경로를 초기화하지 않으면 다음 상태에서도 이전 경로로 이동하려는 문제가 발생한다.

특히 후퇴 상태는 `NavAgent.Move()` 를 이용해 직접 이동을 제어하므로 충돌이 생길 수 있다.

`ResetPath()` 를 호출하면 기존 경로를 제거하고, 다음 상태에서 자유롭게 이동할 수 있게 된다.

<br>

## _**떨림 현상**_
---

의도한대로 적이 공격 후 뒤로 물러나고, 다른 적이 공격하는 모습을 볼 수 있다.

![img](2025-06-08-jitter.gif){: .shadow .rounded-10 .w-75 w="1200"}
_Jitter_

<br>

하지만 적이 공격 후 후퇴상태로 전환될 때, 약간의 떨림이 있는 것을 확인할 수 있다.

`EnemyController.cs` 의 `Update` 문을 확인해보면 아래와 같이 작성되어 있다.

```csharp
private void Update()
{
    StateMachine.Execute();
    
    Vector3 deltaPos = transform.position - prevPos; // Problem Code
    Vector3 velocity = deltaPos / Time.deltaTime;
    
    float forwardSpeed = Vector3.Dot(velocity, transform.forward);
    Animator.SetFloat("forwardSpeed", forwardSpeed / NavAgent.speed, 0.2f, Time.deltaTime);
    
    float angle = Vector3.SignedAngle(transform.forward, velocity, Vector3.up);
    float strafeSpeed = Mathf.Sin(angle * Mathf.Deg2Rad);
    Animator.SetFloat("strafeSpeed", strafeSpeed, 0.2f, Time.deltaTime);
    
    prevPos = transform.position;
}
```
{: file="EnemyController.cs"}

<br>

원인은 `Animator.applyRootMotion` 이 활성화된 상태에서 `transform.position` 변화로 속도를 계산했기 때문이다.

`Root Motion` 이 적용된 경우, 위치 이동은 애니메이션 클립에 의해 제어되므로 `transform.position` 을 기준으로

계산하면 실제 이동 방향 및 속도와 불일치가 발생할 수 있고, 이로 인해 `velocity` 계산 값이 튀는 현상이 생긴다.

<br>

아래처럼 `Animator.applyRootMotion` 이 `true` 일 경우 `velocity` 계산을 건너뛰도록 수정하면

`Root Motion` 에 의한 움직임일 때는 불필요한 `velocity` 계산을 막을 수 있다.

이렇게 하면 `Root Motion` 적용 상태에선 `velocity` 를 `0` 으로 간주하고, 애니메이터가 애니메이션에 따라

자체적으로 움직임을 표현하게 되어, 불일치된 이동 계산으로 인한 시각적 오류, 즉 떨림를 방지할 수 있다.

```csharp
private void Update()
{
    StateMachine.Execute();

    // FIXED
    Vector3 deltaPos = Animator.applyRootMotion? Vector3.zero : transform.position - prevPos;
    Vector3 velocity = deltaPos / Time.deltaTime;
    
    float forwardSpeed = Vector3.Dot(velocity, transform.forward);
    Animator.SetFloat("forwardSpeed", forwardSpeed / NavAgent.speed, 0.2f, Time.deltaTime);
    
    float angle = Vector3.SignedAngle(transform.forward, velocity, Vector3.up);
    float strafeSpeed = Mathf.Sin(angle * Mathf.Deg2Rad);
    Animator.SetFloat("strafeSpeed", strafeSpeed, 0.2f, Time.deltaTime);
    
    prevPos = transform.position;
}
```
{: file="EnemyController.cs"}

<br>

## _**Result**_
---

떨림 없이 애니메이션이 잘 동작하는 모습을 확인할 수 있다.


![img](2025-06-08-result.gif){: .shadow .rounded-10 .w-75 w="1200"}
_Result_