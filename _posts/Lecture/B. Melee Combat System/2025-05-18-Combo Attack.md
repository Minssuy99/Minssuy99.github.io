---
title: 8. Combo Attack
description: Game Development
author: Minssuy99
date: 2025-05-18 23:30:00 +0900
categories: [Lecture, Melee Combat System]
tags: [C#, Unity]
pin: false
math: true
permalink : /posts/Combo-Attack
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

이전의 스크립트에서는 특정 애니메이션에 한하여 동작하는 하드코딩으로 되어있었다.

이렇게 되어있다면 다양한 공격을 구현하기 힘들어진다.

```csharp
IEnumerator Attack()
{
    .
    .
    float impactStartTime = 0.33f;
    float impactEndTime = 0.55f;
    
    animator.CrossFade("Slash", 0.2f);
    yield return null;
    .
    .
}
```
{: file="MeleeFighter.cs}

이를 해결하기 위해, 확장성을 위한 `ScriptableObject` 를 작성하고 콤보공격을 구현한다.

<br>

## _**ScriptableObject**_
---

### _**개념**_

`ScriptableObject` 란, 유니티에서 데이터를 에셋으로 저장하고 공유 할 수 있도록 설계된 클래스 타입이다.

코드에서 데이터를 분리하여, 여러 오브젝트가 공통 데이터를 재사용할 수 있는 구조를 만들 수 있다.

뿐만 아니라, 각 스크립트마다 중복적으로 사용하는 데이터를 한 곳에 묶음으로써 메모리 사용량을 줄일 수 있다.

![img](2025-05-18-ScriptableObject.jpg){: .shadow .w-75 .rounded-10 w="1200"}
_Unity Blog - Useful Ways to Use Scriptable Objects_

<br>

쉽게 말하면, 다량의 데이터를 저장하고 관리할 수 있는 데이터 컨테이너 형태의 `Asset` 일 뿐이다.

또한 이렇게 설계하면 게임 디자이너도 친숙하게 데이터를 다룰 수 있게 되어,

프로그래머가 아니더라도 코드 수정 없이 조절이 가능하여 협업이 쉬워지는 구조가 된다.

<br>

### _**작성**_

애니메이션의 이름과, 실제로 공격이 이루어지는 시간과 종료시간의 데이터를 분리한다.

```csharp
[CreateAssetMenu(menuName = "Combat System/Create a new attack")]

public class AttackData : ScriptableObject // Inherits from ScriptableObject class
{
    // Serialized fields
    // Visible in Inspector
    [SerializeField] private string animName;
    [SerializeField] private float impactStartTime;
    [SerializeField] private float impactEndTime;

    // Expression-bodied read-only properties
    // Properties are not shown in Inspector
    public string AnimName => animName;
    public float ImpactStartTime => impactStartTime;
    public float ImpactEndTime => impactEndTime;
}
```
{: file="AttackData.cs}

<br>

위의 코드는 식 본문을 이용하여 구현한 프로퍼티이다.

인스펙터상에서 보이는 `private` 필드들은 실제 값을 저장하는 프로퍼티의 백킹 필드 `Backing field` 라고 한다.

이렇게 작성하면 필드가 `3` 개임에도 `6` 줄로 늘어나기에, 아래와 같이 자동 구현 프로퍼티로 작성한다.

```csharp
public class AttackData : ScriptableObject
{
    [field: SerializeField] public string AnimName { get; private set; }
    [field: SerializeField] public float ImpactStartTime { get; private set; }
    [field: SerializeField] public float ImpactEndTime { get; private set; }
}
```
{: file="AttackData.cs}

앞에 `[field: SerializeField]` 을 작성하면 인스펙터 상에서도 프로퍼티를 볼 수 있게 된다.

`[field: SerializeField]` 는 `C# 7.3` 이후부터 사용 가능한 대상 속성 `Attribute target` 문법이며,

프로퍼티의 백킹 필드에 `SerializeField` 특성을 직접 적용하는 역할을 한다.

<br>

일반적으로 `SerializeField` 는 필드에만 적용이 가능하다.

자동 구현 프로퍼티는 내부에 컴파일러가 생성한 백킹 필드가 있는데, 이 필드는 코드에 직접 보이지 않는다.

따라서, 자동 프로퍼티를 인스펙터에 노출하려면 `SerializeField` 를 백킹 필드에 적용해야 하는데,

`C# 7.3` 부터는 `[field: SerializeField]` 를 사용하여 속성 대상을 `field` 로 지정할 수 있다.

<br>

### _**생성**_

프로젝트 폴더에서 우클릭 → `Combat System` → `Create a new attack` 을 통해 인스턴스 생성이 가능하다.

![img](2025-05-18-menu.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_AssetMenu_

<br>

생성된 인스턴스에 공격 애니메이션의 이름 및 공격 시작시간, 종료시간을 기입한다.

콤보공격에 이용할 다른 공격 애니메이션들에 대해서도 이와 마찬가지로 생성한다.

![img](2025-05-18-so.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_Inspector_

<br>

## _**Combo Attack**_
---

기존에 작성된 `MeleeFighter.cs` 스크립트를 수정한다.

```csharp
public class MeleeFighter : MonoBehaviour
{
    [SerializeField] private List<AttackData> attacks; // 리스트 추가
    .
    .
}
```
{: file="MeleeFighter.cs"}

<br>


콤보 공격을 위한 조건을 추가한다.

```csharp
int comboCount = 0;
bool doCombo;

public void TryToAttack()
{
    if (!InAction)
    {
        StartCoroutine(Attack());
    }
    // 입력이 들어왔을 때, 공격상태가 Impact 이거나 Cooldown 이라면
    else if (attackState == AttackState.IMPACT || attackState == AttackState.COOLDOWN)
    {
        doCombo = true; // 사용자의 추가 입력을 받아 콤보 이어지도록 플래그 설정
    }
}
```
{: file="MeleeFighter.cs"}


<br>

코루틴 내에 하드코딩이 되어있는 부분을 수정한다.

공격 애니메이션을 리스트 형태로 받아서, 입력에 따라 인덱스를 돌면서 콤보공격을 진행한다.

```csharp
IEnumerator Attack()
{
    InAction = true;
    attackState = AttackState.WINDUP;
    
    animator.CrossFade(attacks[comboCount].AnimName, 0.2f); // Modify
    yield return null;

    var animState = animator.GetNextAnimatorStateInfo(1);

    float timer = 0f;

    while (timer <= animState.length)
    {
        timer += Time.deltaTime;
        float nomalizedTime = timer / animState.length;

        if (attackState == AttackState.WINDUP)
        {
            if (nomalizedTime >= attacks[comboCount].ImpactStartTime)  // Modify
            {
                attackState = AttackState.IMPACT;
                swordCollider.enabled = true;
            }
        }
        else if (attackState == AttackState.IMPACT)
        {
            if (nomalizedTime >= attacks[comboCount].ImpactEndTime)  // Modify
            {
                attackState = AttackState.COOLDOWN;
                swordCollider.enabled = false;
            }
        }
        else if (attackState == AttackState.COOLDOWN) // 공격상태가 Cooldown 이라면
        {
            if (doCombo) // 해당 타이밍에 입력이 들어왔다면 (상단의 TryToAttack 메서드)
            {
                doCombo = false; // 다시 공격할 수 있도록 false 로 초기화
                comboCount = (comboCount + 1) % attacks.Count; // 리스트 인덱스 계산

                StartCoroutine(Attack()); // 다음 인덱스의 공격 코루틴 시작 (콤보공격)
                yield break; // 현재 코루틴은 종료
            }
        }
        
        yield return null;
    }

    attackState = AttackState.IDLE;
    comboCount = 0; // 공격이 전부 끝났다면, 인덱스를 0으로 초기화
    InAction = false;
}
```
{: file="MeleeFighter.cs"}

<br>

인스펙터 상에서 `Attacks` 의 리스트를 추가하고, 인스턴스 한 `ScriptableObject` 를 추가해준다.

![img](2025-05-18-attacks.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_Inspector_

<br>

### _**중간결과**_

아래의 피격 애니메이션을 보면, 첫번째 공격엔 반응했으나 두번째 공격엔 반응을 하지않는다.

![img](2025-05-18-bug-attaked.gif){: .shadow .rounded-10 .w-75 w="1200"}
_Test_

<br>

세번째 공격인 발차기에 반응을 한 이유는, 검의 콜라이더가 닿아서 애니메이션이 실행된 것이다.

피격 애니메이션이 전부 끝날 때 까지 코루틴이 기다리고 있기 때문에, 피격 애니메이션이 즉시 실행이 안된다.

<br>

이를 해결하기 위해 피격 애니메이션이 끝날 때 까지 기다릴 시간을 아래와 같이 줄인다.

```csharp
IEnumerator PlayerHitReaction()
{
    InAction = true;
    animator.CrossFade("SwordImpact", 0.2f);
    yield return null;

    var animState = animator.GetNextAnimatorStateInfo(1);
    
    yield return new WaitForSeconds(animState.length * 0.8f); // 전체시간의 80% 만 기다림
    
    InAction = false;
}
```
{: file="MeleeFighter.cs}

<br>

## _**Result**_
---

피격 애니메이션이 제대로 실행되는 것을 확인할 수 있다.

발차기에 대한 피격은 발차기 공격에 대한 콜라이더가 적용되어있지 않아서, 현재의 모습이 정상이다.

![img](2025-05-18-result.gif){: .shadow .rounded-10 .w-75 w="1200"}
_Result_

<br>

## _**Reference**_
---
[Unity Blog - 6 Useful Ways to Use Scriptable Objects for Collaboration and Coding](https://unity.com/kr/blog/engine-platform/6-ways-scriptableobjects-can-benefit-your-team-and-your-code)
