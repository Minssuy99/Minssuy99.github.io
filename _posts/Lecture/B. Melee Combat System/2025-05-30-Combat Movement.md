---
title: 12. Combat Movement
description: Game Development
author: Minssuy99
date: 2025-05-31 16:00:00 +0900
categories: [Lecture, Melee Combat System]
tags: [C#, Unity]
pin: false
math: true
permalink : /posts/Combat-Movement
published: true
# mermaid: false
image:
  path: 'thumbnail.jpg'
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAFCAYAAAB8ZH1oAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsQAAA7EAZUrDhsAAADFSURBVBhXDcuxTsJAHIDx7393LW3vKgRjCYiDAzFOJuICg4smvohPweRbMTLBapwdkIRAoomNJEgK0rPDt/0+sc555xzdi4w4ianbkiQJ8brBfldw1UtZrTYoAY5Hz+FPY1RAURyqSkSFNJsp80XOd/6LBEHkh8MenVbK69uS0ei5Opd0zzOsjdHGICh0ZOOXE6e5vTlju91hylPGkzU188V09s78Y02nHSIPj/d+cNfGRvCz+aygZpELT/0Gl9cZIgovin+18zpj6AM9igAAAABJRU5ErkJggg==
  # alt:
media_subpath: '/assets/img/posts/Lecture/B. Melee Combat System'
---
<!---------------------------------------Header-------------------------------------->

## _**Circling**_
---

플레이어가 적의 범위에 들어왔을 때 전투태세를 갖추는 것 뿐만 아니라,

적 들이 플레이어의 주변을 빙글빙글 돌도록 구현한다.


## _**Code**_
---

`Idle` 에서 `Circling` 행동으로 전환하기 위한 타이머 변수를 선언한다.

그리고 이 타이머 변수에 `Time.deltaTime` 만큼 매 프레임 감소시킨다.

```csharp
private float timer = 0f;

public override void Execute()
{
    if (timer > 0)
    {
        timer -= Time.deltaTime;
    }
}
```
{: file="CombatMovementState.cs"}

<br>

타이머가 `0` 이 되면 `Idle` 에서 `Circling` 으로 전환하는 로직을 작성한다.

랜덤하게 주위를 돌도록 `Random.Range` 를 사용하여 `50%` 확률로 움직이도록 한다.

```csharp
public override void Execute()
{
    if (state == AICombatStates.IDLE)
    {
        if (timer <= 0)
        {
            if (Random.Range(0, 2) == 0) // 50% 확률로 Idle
            {
                StartIdle();
            }
            else // 50% 확률로 플레이어 주위 회전
            {
                StartCircling();
            }
        }
    }
}
```
{: file="CombatMovementState.cs"}

<br>

`StartCircling` 함수 로직을 작성한다.

```csharp
[SerializeField] Vector2 circlingTimeRange = new Vector2(3, 6);

void StartCircling()
{
    state = AICombatStates.Circling; // 상태 전환
    timer = Random.Range(circlingTimeRange.x, circlingTimeRange.y); // 회전시간 랜덤으로 지정

    circlingDir = Random.Range(0, 2) == 0 ? 1 : -1; // 좌, 또는 우 회전 방향 랜덤으로 지정
    
    enemy.Animator.SetBool("circling", true);
    enemy.Animator.SetFloat("circlingDir", circlingDir);
}
```
{: file="CombatMovementState.cs"}

<br>

더 다양한 상황을 만들기 위해 `Idle` 지속 시간도 랜덤으로 지정한다.

```csharp
[SerializeField] Vector2 idleTimeRange = new Vector2(2, 5);

void StartIdle()
{
    state = AICombatStates.IDLE;
    timer = Random.Range(idleTimeRange.x, idleTimeRange.y); // 랜덤 로직 추가
    
    enemy.Animator.SetBool("combatMode", true);
    enemy.Animator.SetBool("circling", false);
}
```
{: file="CombatMovementState.cs"}

<br>

실제로 행동을 수행할 `Execute` 함수 내부에 실행로직을 작성한다.

```csharp
public override void Execute()
{
    .
    .
    else if (state == AICombatStates.Circling)
    {
        if (timer <= 0)
        {
            StartIdle();
            return;
        }
        // Vector3.up → (0, 1 ,0)  Y 축 방향을 나타내는 단위벡터
        transform.RotateAround(enemy.Target.transform.position, Vector3.up,
                                            circlingSpeed *circlingDir * Time.deltaTime);
    }
}
```
{: file="CombatMovementState.cs"}

<br>

### _**transform.RotateAround**_

#### _**함수 시그니처**_

월드 좌표계의 지점을 통과하는 축 `axis` 을 기준으로, 지정한 각도 `degrees` 만큼 `Transform` 을 회전시킨다.

이 함수는 `Transform`의 위치 `position` 와 회전 `rotation` 을 모두 변경한다.

```csharp
RotateAround(Vector3 point, Vector3 axis, float angle);
```

<br>

#### _**예시**_

```csharp
void Update()
{
    // target 을 중심으로 초당 20도 각도로 주위를 회전 
    transform.RotateAround(target.transform.position, Vector3.up, 20 * Time.deltaTime);
}
```

<br>

## _**Animator**_
---

작성한 로직을 바탕으로 애니메이션이 실행되도록 설정한다.

`Circling` 을 블렌드 트리로 정의하고, 회전을 결정할 매개변수를 `float` 로 선언한다.

`circlingDir` 이 `-1` 이라면 적 오브젝트는 오른쪽으로 회전한다.

![img](2025-05-31-blendtree.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_Animator_

<br>

애니메이터와 관련된 로직은 아래의 함수에 선언되어 있다.

```csharp
[SerializeField] Vector2 circlingTimeRange = new Vector2(3, 6);

void StartCircling()
{
    state = AICombatStates.Circling;
    timer = Random.Range(circlingTimeRange.x, circlingTimeRange.y);

    circlingDir = Random.Range(0, 2) == 0 ? 1 : -1; // 이 값을 애니메이터의 파라미터로 넘겨준다 
    
    enemy.Animator.SetBool("circling", true); // Circling 블렌드 트리로 이동
    enemy.Animator.SetFloat("circlingDir", circlingDir); // 회전 애니메이터 실행
}
```
{: file="CombatMovementState.cs"}

<br>

## _**Result**_
---

적 오브젝트가 각각 랜덤한 시간으로 대기 및 이동을 하는 모습을 확인할 수 있다.

![img](2025-05-31-result.gif){: .shadow .rounded-10 .w-75 w="1200"}
_Result_

<br>

## _**Error**_
---

적 오브젝트 간 충돌이 발생했을 땐 원활한 처리가 이루어지지 않는다.

![img](2025-05-31-error.gif){: .shadow .rounded-10 .w-75 w="1200"}
_Error_