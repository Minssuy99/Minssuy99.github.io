---
title: 13. Improving Circling
description: Game Development
author: Minssuy99
date: 2025-06-01 23:00:00 +0900
categories: [Lecture, Melee Combat System]
tags: [C#, Unity]
pin: false
math: true
permalink : /posts/Improving-Circling
published: true
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

![img](2025-05-31-error.gif){: .shadow .rounded-10 .w-75 w="1200"}
_Problem_

<br>

현재 구조물 혹은 적 오브젝트와 부딪히면, 장애물을 통과하려고 시도한다.

이런 현상이 발생하는 이유는 `transform.RotateAround` 함수를 사용하고 있기 때문이다.

이 함수는 단순히 적을 원형으로 이동시키려고만 할 뿐, 장애물이 있는지 신경쓰지 않는다.

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
        // Problem Code
        transform.RotateAround(enemy.Target.transform.position, Vector3.up,
                                            circlingSpeed *circlingDir * Time.deltaTime);
    }
}
```
{: file="CombatMovementState.cs"}

<br>

## _**Circling 로직 수정**_
---

### _**NavMeshAgent.Move**_

`RotateAround` 대신 `NavMeshAgent.Move()` 를 사용하여 해결할 수 있다.

해당 함수는 장애물로 이동하려고 하진 않지만, 플레이어 주위를 원형으로 회전시키는 방법이 없다.

이를 해결하기 위해 `Move` 함수에 파라미터 `Offset=변화량`로 전달할 벡터값을 직접 작성한다.

<br>

* _**vecToTarget**_ : 타겟을 기준으로 적이 어느 위치에 있는지를 나타냄 `타겟 → 적 방향 벡터`
* _**rotatedPos**_ : 벡터를 `Y` 축 기준으로 회전시켜 새로운 위치 `rotatedPos` 계산
* _**Move(rotatedPos - vecToTarget)**_ : 현재 위치에서 회전된 위치까지의 변화량 `=오프셋` 을 계산하여 직접 이동

```csharp
Vector3 vecToTarget = enemy.transform.position - enemy.Target.transform.position;
Vector3 rotatedPos = Quaternion.Euler(0, circlingSpeed * circlingDir * Time.deltaTime, 0) * vecToTarget;
enemy.NavAgent.Move(rotatedPos - vecToTarget);
```
{: file="CombatMovementState.cs"}

<br>

### _**NavMeshAgent.SetDestination**_

`SetDestination` 함수는 목표 지점을 향해 해당 위치까지 자동으로 경로를 계산해서 이동한다.

이 방식은 캐릭터가 장애물을 피해 자연스럽게 목표 지점까지 도달하도록 도와주는 데 적합하다.

하지만 이 함수는 원형 경로로 이동은 불가능하며, 장애물을 피하며 최적의 경로로 자동 이동한다.

```csharp
NavAgent.SetDestination(Vector3 target);
```

<br>

`SetDestination` 을 사용할 땐 주의할 점이 있는데, 목표 위치가 현재 위치와 너무 가까우면

 `NavMesh` 시스템은 그 거리를 무시하고 **이미 도착했다**고 판단해 아예 이동하지 않는 현상이 있다.

<br>

특히 아래와 같은 상황에서 문제가 발생한다. 

* 프레임마다 매우 짧은 거리만 이동하고 싶을 때
* 목표 위치가 자주 갱신되며, 이동량이 미세한 경우
* 이동 궤적이 직선이 아닌 원형 등 특수한 경우

즉, 이 함수는 큰 이동이나 중간 거리 목적지 설정에는 적합하지만, 세밀한 제어나 원형 이동에는 적합하지 않다.

<br>

## _**애니메이션 분리**_
---

### _**Problem**_


![img](2025-05-31-error.gif){: .shadow .rounded-10 .w-75 w="1200"}
_Problem_

<br>

위의 현상을 다시 보자면, 정지한 적 오브젝트는 옆으로 밀리고 있음에도 앞으로 걷는 애니메이션을 실행하고 있다.

그 이유는, 이 오브젝트가 이동 방향이 아닌 단순한 속도의 크기만을 기준으로 애니메이션을 판단하기 때문이다.

<br>

```csharp
private void Update()
{
    StateMachine.Execute();
    
    // Error Code
    Animator.SetFloat("moveAmount", NavAgent.velocity.magnitude / NavAgent.speed);
}
```
{: file="EnemyController.cs"}

위의 코드를 보면 방향과 상관없이 벡터의 크기를 의미하는 `magnitude` 를 `moveAmount` 에 전달하고 있다.

속도의 방향은 고려되지 않고 오직 크기만 반영되기 때문에, 방향이 어디든지간에 일정 속도 이상이면

걷는 애니메이션이 재생되는 문제가 발생한다.

<br>

이를 해결하기 위해, 실제 전방으로 이동하고 있는 정도만을 추출해 애니메이션에 반영해야 한다.

`velocity` 벡터에서 `forward` 벡터 방향으로의 성분만 분리하여 사용해야 한다.

<br>

쉽게 말하자면, 오브젝트의 전방 벡터와 이동 방향이 일치할수록 앞으로 움직이는 애니메이션을,

오브젝트의 측면 벡터와 이동 방향이 일치할수록 좌우로 움직이는 애니메이션을 실행하도록 구현한다.

<br>

### _**Solve**_

* _**Forward Vector**_ : 캐릭터가 전방을 향해 바라보고 있는 방향벡터
* _**Velocity**_ : 실제 캐릭터가 이동하고 있는 방향과 속도를 포함한  벡터

![img](2025-06-01-vector1.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_Vector_

$$\cos(\theta) = \frac{\text{전방 성분}}{v}$$

$$\sin(\theta) = \frac{\text{측면 성분}}{v}$$

$$ v = |\vec{\text{velocity}}| = \text{velocity.magnitude} $$

<br>

`Foward Vector` 는 밑변, `Velocity` 는 빗변이고, 그 사이 각을 `θ` 로 설정한다.

여기서 삼각함수를 이용하여 속도벡터를 전진 성분과 측면 성분, 두 성분으로 분해할 수 있다.

<br>

![img](2025-06-01-vector2.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_Vector_

$$\text{전방 속도} = v \cdot \cos(\theta)$$

$$\text{측면 속도} = v \cdot \sin(\theta)$$

<br>

## _**적용**_
---

위의 내용을 바탕으로 코드로 구현한다.

<br>

### _**Velocity**_

`NavMeshAgent.velocity` 에서 가져올 수 있지만, 원형 이동 애니메이션에서는 작동하지 않는다.

유니티에서 `NavMeshAgent.velocity` 는 `SetDestination()` 함수로 목적지를 지정해줄 때 자동으로 갱신된다.

이 경우 `Agent` 는 내비게이션 시스템에 의해 자동으로 움직이고, 그 속도 벡터가 `velocity` 로 주어진다.

<br>

하지만 `NavMeshAgent.Move` 로 움직임을 수동으로 설정했기 때문에, `velocity` 값은 갱신이 되지 않거나 `0` 이 된다.

그러므로 속도도 마찬가지로 수동으로 계산해야 한다.

<br>

$$ v\text{ (속도)} = \frac{\text{변위 (위치변화량)}}{\text{시간 변화량}} = \frac{\Delta x}{\Delta t} $$

<br>

시간 변화량은 `Time.deltaTime` 이고, 위치변화량은 적 오브젝트의 `현재위치 - 이전위치` 이다.

이걸 코드로 구현하면 아래와 같다.

```csharp
private Vector3 prevPos; // 이전 위치 변수선언

private void Update()
{
    StateMachine.Execute();
    
    Vector3 deltaPos = transform.position - prevPos; // 위치 변화량
    Vector3 velocity = deltaPos / Time.deltaTime; // 속도 = 위치변화량 / 시간변화량
    
    prevPos = transform.position; // 현재위치를 prevPos 로 갱신
}
```
{: file="EnemyController.cs"}

<br>

### _**Dot Product**_

앞서 말했듯이, 전방속도는 삼각함수를 이용하여 아래와 같이 전방 속도의 값을 구할 수 있다.

<br>

$$\text{전방 속도} = v \cdot \cos(\theta)$$

<br>

벡터 내적 공식을 이용하면 각도를 직접 구하지 않아도, 내적 계산으로 전방 속도를 구할 수 있다.

쉽게 말하면 벡터의 내적은 두 벡터가 얼마나 같은 방향을 향하고 있는지를 알려주는 값이다.

<br>

전방 속도는 삼각함수로 구할 수 있지만 내적 공식을 하면 두 벡터의 크기와 각도 정보를

한 번에 계산하여 전방 속도의 값을 바로 얻을 수 있다.

<br>

$$
\text{Dot}(\vec{a}, \vec{b}) = |\vec{a}| \cdot |\vec{b}| \cdot \cos(\theta)
$$

<br>

위의 [그림](#공식)에 있는 두 벡터의 값을 대입한다.

`v` 는 `velocity` 이고, `f` 는 `forward vector` 이다.

$$
\vec{v} \cdot \vec{f} = |\vec{v}| |\vec{f}| \cos(\theta)
$$

<br>


`forward vector` 는 방향벡터, 즉 정규화<sub>normalized</sub>되어 있으므로, 길이는 `1` 이 된다.

$$
|\vec{f}| = 1
$$

<br>

식을 전개하면 아래와 같이 된다.

$$
\text{Dot}(\vec{v}, \vec{f}) = |\vec{v}| \cdot |\vec{f}| \cdot \cos(\theta)
= |\vec{v}| \cdot 1 \cdot \cos(\theta)
= |\vec{v}| \cdot \cos(\theta)
$$

<br>

따라서 내적은 다음과 같이 표현할 수 있다.

$$
\text{Dot}(\vec{v}, \vec{f}) = |\vec{v}| \cos(\theta) = \text{Forward Speed}
$$

<br>

### _**전방성분 분리**_

유니티에서는 `Vector3.Dot` 함수를 이용하여 전방 및 측면 성분을 간단히 분리 할 수 있다.

아래는 전방성분을 분리한 코드이다.

```csharp
private Vector3 prevPos; // 이전 위치 변수선언

private void Update()
{
    StateMachine.Execute();
    
    Vector3 deltaPos = transform.position - prevPos; // 위치 변화량
    Vector3 velocity = deltaPos / Time.deltaTime; // 속도 = 위치변화량 / 시간변화량

    float forwardSpeed = Vector3.Dot(velocity, transform.forward); // 전방성분 분리
    
    Animator.SetFloat("moveAmount", forwardSpeed / NavAgent.speed); // forwardSpeed 를 나눈값을 전달
    
    prevPos = transform.position; // 현재위치를 prevPos 로 갱신
}
```
{: file="EnemyController.cs"}

<br>

### _**측면성분 분리**_

측면성분도 마찬가지로, `Vector3.Dot` 및 외적을 구하는 `Vector3.Cross` 를 이용하여 아래와 같이 분리할 수 있다.

`forward vector` 에 수직인 측면 벡터<sub>rightvector</sub>를 만들고, 속도 벡터를 이 벡터에 내적해서 측면속도를 분리할 수 있다.

```csharp
Vector3 rightVector = Vector3.Cross(forward, Vector3.up).normalized;
float sideSpeed = Vector3.Dot(velocity, rightVector);
```

<br>

하지만 이번엔 다른방식인 `Vector3.SignedAngle` 을 사용하는 방식으로 측면성분을 분리해본다.

위의 공식을 다시 보자면, 측면속도를 구하는 공식은 아래와 같다.

$$\text{측면 속도} = v \cdot \sin(\theta)$$

<br>

여기서 `SignedAngle` 함수를 사용하면 `θ` 를 구할 수 있고, 함수의 시그니처는 아래와 같다.

* _**from**_ : 시작 벡터
* _**to**_ : 끝 벡터
* _**axis**_ : 각도를 측정할 평면에 수직인 축. 현재는 `Y` 축에 해당된다.

```csharp
float angle = Vector3.SignedAngle(Vector3 from, Vector3 to, Vector3 axis);
```
{: file="Vector3.SignedAngle"}

<br>

이 `angle` 변수를 `Mathf.Sin` 함수에 넣어서, 주어진 각도의 사인 값을 얻을 수 있다.

![img](2025-06-01-mathfsin.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_Mathf.Sin_

<br>

하지만 위의 그림을 보면, 이 함수는 인자로 라디안 값을 입력해줘야한다고 명시되어있다.

즉, 각도가 도<sub>degree</sub> 단위로 있다면, `Mathf.Deg2Rad` 를 곱해서 라디안으로 변환할 수 있다.

`Mathf.Deg2Rad` 는 유니티의 `Mathf` 클래스에 정의된 `float` 타입의 상수 프로퍼티이다.

`Deg2Rad` 는 `Degrees to Radians` 의 약어이다.

$$\text{Mathf.Deg2Rad} = \frac{\pi}{180} \approx 0.0174533$$

<br>

`angle` 변수는 `degree` 이니, `Deg2Rad` 를 곱하여 라디안으로 변환한다.

```csharp
float sin = Mathf.Sin(angle * Mathf.Deg2Rad);
```
{: file ="Mathf.Sin"}

<br>

여기서, 원래라면 위의 공식에 따라 속도 `v` 를 곱하면 측면성분을 구할 수 있게 된다.

하지만 우리가 구현하고자 하는 것은, 적 오브젝트가 이동방향(좌/우) 를 판단하기 위한 기준을 만드는 것이다.

![img](2025-06-01-blendtree.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_Blend Tree_

현재 애니메이터의 `Blend Tree` 에서 좌측이동은 `-1`, 우측이동은 `1` 로 설정되어 있다.

현재 필요한 것은 **방향** 뿐이므로, 속도의 값은 필요하지 않기에 속도는 곱하지 않아도 된다.

<br>

## _**블렌드 트리 병합**_
---

현재 애니메이터는 아래와 같이 되어있다.

`CombatIdle` 블렌드 트리와 `Circling` 블렌드 트리가 분리되어 있다.

![img](2025-06-01-animator.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_Animator_

<br>

`CombatIdle` 은 `Idle`, `Walk`, `Run` 세개의 동작으로 이루어져 있다.

`Circling` 은 `Left Strafe`, `Rihgt Strafe` 두개의 동작으로 이루어져 있다.

이를 단순화 하기 위해 아래와 같이 2차원 블렌드 트리를 사용한다.

![img](2025-06-01-blendtree2.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_2D Blend Tree_

<br>

오브젝트가 진행하는 방향에 따라 각 애니메이션이 실행되는 모습을 볼 수 있다.

![img](2025-06-01-2dblendtree.gif){: .shadow .rounded-10 .w-75 w="1200"}
_2D Blend Tree_

<br>

## _**중간 결과**_
---

적 오브젝트와 충돌이 생겼을 때, 가만히 있는 적 오브젝트가 앞으로 가는 애니메이션을 실행하지 않는다.

오브젝트의 진행방향에 따라서 회전 애니메이션이 실행되는 모습을 볼 수 있다.

![img](2025-06-01-Shooting.gif){: .shadow .rounded-10 .w-75 w="1200"}
_2D Blend Tree_

<br>

## _**장애물 충돌문제**_
---

적들이 장애물 주변을 어떻게 도는지 테스트 해보기 위해서, 큐브 주변에 적을 배치한다.

여기서 이상한 문제를 볼 수 있는데, 비정상적인 떨림현상과 적이 큐브를 지나서 돌지 않는 것을 볼 수 있다.

![img](2025-06-01-aniError.gif){: .shadow .rounded-10 .w-75 w="1200"}
_Mathf.Sin_

<br>

`NavMeshAgent` 의 `destination` 이 여전히 플레이어의 위치로 설정되어 있기 때문에,

적은 회전을 하려고 하는데, 그렇게 되면 큐브 뒤로 가게 되버리니 플레이어와 거리가 멀어지면서

`NavMeshAgent` 가 플레이어쪽으로 끌어당기는 힘과 충돌하게 된다.

그래서 위와 같은 이상한 떨림현상이 발생하는 것이다.

<br>


### _**Code**_

현재 아래의 코드에서 목적지를 설정하고 있고, 그러므로 적은 플레이어에게 계속 접근을 시도한다.

```csharp
public override void Execute()
{
    .
    .
    else if (state == AICombatStates.CHASE)
    {
        if (Vector3.Distance(enemy.Target.transform.position, enemy.transform.position) <= distanceToStand + 0.03f)
        {
            StartIdle();
            return;
        }
            
        // Problem. 목적지를 정함과 동시에 플레이어에게 접근하는 함수
        enemy.NavAgent.SetDestination(enemy.Target.transform.position);
    }
}
```
{: file="CombatMovementState.cs"}

<br>

이 문제는 `NavMeshAgent.ResetPath()` 함수로 간단하게 처리할 수 있다.

이 함수를 호출하면 `NavMeshAgent` 가 현재 따라가고 있는 경로 `Path` 를 초기화 한다.

`SetDestination()` 으로 설정된 목적지를 제거함으로써, 목적지를 향한 추적도 중지되게 된다.

```csharp
void StartCircling() // 회전 상태 State
{
    state = AICombatStates.Circling;

    enemy.NavAgent.ResetPath(); // 목적지 경로 초기화함으로써 추적 중지
    timer = Random.Range(circlingTimeRange.x, circlingTimeRange.y);

    circlingDir = Random.Range(0, 2) == 0 ? 1 : -1;
}
```
{: file="CombatMovementState.cs"}

<br>

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
* [Unity Docs - Vector3.SignedAngle](https://docs.unity3d.com/2022.3/Documentation/ScriptReference/Vector3.SignedAngle.html)
* [Unity Docs - NavMeshAgent.SetDestination](https://docs.unity3d.com/2022.3/Documentation/ScriptReference/AI.NavMeshAgent.SetDestination.html)
* [Unity Docs - NavMeshAgent.ResetPath](https://docs.unity3d.com/2022.3/Documentation/ScriptReference/AI.NavMeshAgent.ResetPath.html)
* [Unity Docs - Mathf.Deg2Rad](https://docs.unity3d.com/2022.3/Documentation/ScriptReference/Mathf.Deg2Rad.html)
* [Unity Docs - Mathf.Sin](https://docs.unity3d.com/2022.3/Documentation/ScriptReference/Mathf.Sin.html)

