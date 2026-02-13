---
title: 14. Attacking the Player
description: Game Development
author: Minssuy99
date: 2025-06-06 19:00:00 +0900
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

![img](2025-06-06-example.gif){: .shadow .rounded-10 .w-75 w="1200"}
_소울류에서 볼 수 있는 다굴빵 [(출처)](https://gall.dcinside.com/mgallery/board/view/?id=aoegame&no=20426726)_

<br>

## _**Code**_
___


### _**적 리스트 구현**_

`Enemy Manager` 스크립트를 통해 플레이어 범위 내에 있는 모든 적을 관리한다.

그리고 매니저에 등록된 적들 중, 하나의 적을 선택해서 플레이어를 공격하도록 한다.

<br>

`EnemyController` 클래스타입 리스트를 만들고, 이 리스트에 적을 추가하거나 제거하는 함수를 작성한다.

```csharp
public class EnemyManager : MonoBehaviour
{
    private List<EnemyController> enemiesInRange = new List<EnemyController>();

    public void AddEnemyInRange(EnemyController enemy) // Add Func
    {
        // 리스트에 enemy 가 존재하면 true
        if (!enemiesInRange.Contains(enemy)) 
            enemiesInRange.Add(enemy);
    }

    public void RemoveEnemyInRange(EnemyController enemy) // Remove Func
    {
        enemiesInRange.Remove(enemy);
    }
}
```
{: file="EnemyManager.cs"}

<br>

적의 범위에 있을 땐 `AddEnemyInRange` 를 호출하고, 벗어나면 `RemoveEnemyInRange` 를 호출한다.

이 함수들은 현재 플레이어를 감지하기 위한 스크립트인 `Vision Sensor` 에서 호출하면 된다.

```csharp
public class VisionSensor : MonoBehaviour
{
    [SerializeField] private EnemyController enemy;

    private void OnTriggerEnter(Collider other)
    {
        var fighter = other.GetComponent<MeleeFighter>();

        if (fighter != null)
        {
            enemy.TargetsInRange.Add(fighter); // 적 범위에 들어온 대상 추가
            // TODO : 리스트에 적 추가
        }
    }

    private void OnTriggerExit(Collider other)
    {
        var fighter = other.GetComponent<MeleeFighter>();

        if (fighter != null)
        {
            enemy.TargetsInRange.Remove(fighter); // 적 범위에서 벗어난 대상 제거
            // TODO : 리스트에 적 제거
        }
    }
}
```
{: file="VisionSensor.cs"}

<br>

현재 이 스크립트엔 `EnemyManager` 에 대한 참조가 없어서 호출이 불가하다.

`Serialized field` 변수를 만들어서 `Manager` 클래스에 대한 참조를 얻을 수 있다.

하지만 `Enemy Manager` 클래스는 씬에서 한번만 생성되는 매니저이므로, 싱글톤 패턴을 사용한다.

<br>

```csharp
public class EnemyManager : MonoBehaviour
{
    public static EnemyManager i { get; private set; } // 정적변수 선언

    private void Awake()
    {
        i = this; // EnemyManager 스크립트가 붙은 컴포넌트 자신이 i가 된다는 의미
    }

    private List<EnemyController> enemiesInRange = new List<EnemyController>();

    public void AddEnemyInRange(EnemyController enemy)
    {
        // 리스트에 enemy 가 존재하면 true
        if (!enemiesInRange.Contains(enemy)) 
            enemiesInRange.Add(enemy);
    }

    public void RemoveEnemyInRange(EnemyController enemy)
    {
        enemiesInRange.Remove(enemy);
    }
}
```
{: file="EnemyManager.cs"}

<br>

`Enemy Manager`의 `public static` 인스턴스를 만들고, 그 인스턴스를 사용해서 접근할 수 있다.

여러 인스턴스를 만들어야 하는 `EnemyController` 같은 경우에선 이 싱글톤 패턴을 사용할 수 없다.

싱글톤을 구현하였으니, `Vision Sensor` 스크립트에서 함수 호출을 할 수 있게 된다.

```csharp
public class VisionSensor : MonoBehaviour
{
    [SerializeField] private EnemyController enemy;

    private void OnTriggerEnter(Collider other)
    {
        var fighter = other.GetComponent<MeleeFighter>();

        if (fighter != null)
        {
            enemy.TargetsInRange.Add(fighter); // 적 범위에 들어온 대상 추가
            EnemyManager.i.AddEnemyInRange(enemy);  // TODO : 리스트에 적 추가
        }
    }

    private void OnTriggerExit(Collider other)
    {
        var fighter = other.GetComponent<MeleeFighter>();

        if (fighter != null)
        {
            enemy.TargetsInRange.Remove(fighter); // 적 범위에서 벗어난 대상 제거
            EnemyManager.i.RemoveEnemyInRange(enemy);  // TODO : 리스트에 적 제거
        }
    }
}
```
{: file="VisionSensor.cs"}

<br>

잠시 `EnemyManager` 에 있는 `enemiesInRange` 리스트의 접근제어자를 `public` 으로 변경한다.

이후 인스펙터 창에서 플레이어의 범위에 따라 아래와 같은 변화를 확인할 수 있다.

![img](2025-06-06-List.gif){: .shadow .rounded-10 .w-75 w="1200"}
_Enemies List_

<br>

### _**적 공격 구현**_

추적된 적 중 하나를 선택해서 플레이어를 공격하게 만든다.

구현할 전투 시스템에서는, 위의 엘든링 같은 모습이 아닌 적이 차례대로 플레이어를 공격하기를 원한다.

추가로, 플레이어가 반격할 시간도 필요하니 다른 공격을 실행하기 전에 몇 초 정도 텀을 두도록 구현한다.

```csharp
private float notAttackingTimer = 2f;
```
{: file="EnemyManager.cs"}

<br>

이 `notAttackingTimer` 타이머는 어떠한 적도 플레이어를 공격하지 않을 때 업데이트된다.

이 타이머가 특정 임계값에 도달하면 적을 선택해서 플레이어를 공격하게 할 수 있다.

공격이 끝나면 타이머를 다시 업데이트하고, 다시 임계값에 도달하면 다른 적을 선택해서 플레이어를 공격하게 된다.

<br>

그럼, 어떠한 적도 플레이어를 공격하고 있지 않고 있다는 사실을 확인할 수 있는 로직이 있어야 한다.

이 부분은 `Any` 함수를 사용해서 적 중 누군가가 플레이어를 공격하고 있는지 확인할 수 있다.

```csharp
/* 현재 상태가 파라미터로 들어온 행동과 일치하는지 검사 */
public bool IsInState(EnemyStates state)
{
    return StateMachine.CurrentState == stateDict[state];
}
```
{: file="EnemyController.cs"}

```csharp
[SerializeField] private Vector2 timeRangeBetweenAttacks = new Vector2(1, 4);

private void Update()
{
    if (!enemiesInRange.Any(e => e.IsInState(EnemyStates.ATTACK))) // 아무도 공격상태가 아니라면
    {
        if (notAttackingTimer > 0)
            notAttackingTimer -= Time.deltaTime; // 시간이 줄어들고

        if (notAttackingTimer <= 0) // 타이머가 끝이 났다면
        {
            var attackingEnemy = SelectEnemyForAttack(); // 적 중에서 랜덤으로 고른 뒤
            attackingEnemy.ChangeState(EnemyStates.ATTACK); // 공격상태로 전환하고
            notAttackingTimer = Random.Range(timeRangeBetweenAttacks.x, timeRangeBetweenAttacks.y); // 타이머 랜덤설정
        }
    }
}
```
{: file="EnemyManager.cs"}

* _**enemiesInRange**_ : List<EnemyController>
* _**Any**_ : 조건을 만족하는 요소가 하나라도 있는지 검사하는 함수
* _**e => ...**_ : 리스트의 각 요소를 하나씩 `e` 라는 이름으로 받아서 `...` 조건을 확인하는 함수

`e => ...` 는 람다식이고, 여기서 `e` 는 `enemiesInRange` 리스트의 각 요소를 의미하는 매개변수 이름이다.

<br>

이 람다식을 일반적인 `for` 반복문으로 풀면 아래와 같은 모습이 된다.

```csharp
bool anyEnemyAttacking = false;

foreach (EnemyController enemy in enemiesInRange)
{
    if (enemy.IsInState(EnemyStates.ATTACK))
    {
        anyEnemyAttacking = true;
        break;
    }
}

if (!anyEnemyAttacking)
{
    // 타이머 감소 및 적 공격 로직 실행
}
```

<br>

다음은 공격할 적을 선택하는 함수 `SelectEnemyForAttack` 를 작성한다.

무작위로 적을 선택하여 구현하는 것도 좋지만, 전투대기를 오래 하고있는 적 일수록 우선권을 주는 방식으로 구현한다.

함수를 작성하기에 앞서, 전투대기 상태에 얼마나 오래 머물러 있었는지 추적하기 위해 변수를 추가한다.

```csharp
public float CombatMovementTimer { get; set; } = 0f;
```
{: file="EnemyController.cs"}

```csharp
public override void Enter(EnemyController owner)
{
    enemy = owner;
    
    // 추적 멈출 거리 설정
    enemy.NavAgent.stoppingDistance = distanceToStand;
    
    // 전투상태 돌입 시 타이머 0으로 초기화
    enemy.CombatMovementTimer = 0f;
}
public override void Execute()
{
    .
    .
    enemy.CombatMovementTimer += Time.deltaTime; // 공격상태로 들어가지 않으면 계속 증가
}
public override void Exit()
{
    enemy.CombatMovementTimer = 0f; // 전투상태 탈출 시 타이머 0으로 초기화
}
```
{: file="CombatMovementState.cs"}

<br>

`SelectEnemyForAttack` 함수를 작성한다.

아까 선언했던 변수 `CombatMovementTimer` 의 값을 기준으로 목록을 정렬한다.

목록을 정렬할 땐 `OrderBy~` 함수를 사용하면 된다.

* _**OrderBy**_ : 오름차순 정렬
* _**OrderByDescending**_ : 내림차순 정렬

```csharp
EnemyController SelectEnemyForAttack()
{
    return enemiesInRange.OrderByDescending(e => e.CombatMovementTimer).FirstOrDefault();
}
```
{: file="EnemyManager.cs"}

<br>

하지만 이러한 방식은 성능이 좋지 않은데, `Order` 함수는 내부적으로 전체 리스트를 정렬한다.

정렬 알고리즘은 보통 `O(n log n)` 의 시간복잡도를 가진다.

그래서 리스트에 있는 적의 수가 많아질수록 처리시간이 빠르게 늘어난다.

만약 수천마리가 있는 좀비게임 같은걸 만든다고 하면, 이러한 정렬방식은 영 좋지 못하다.

<br>

대신 단순하게 `for` 반복문을 이용하면, 리스트를 한번만 순회하니 시간복잡도는 `O(n)` 이므로 더 효율적이다.

```csharp
EnemyController bestEnemy = null;
float maxTimer = float.MinValue;

foreach (var enemy in enemiesInRange)
{
    if (enemy.CombatMovementTimer > maxTimer)
    {
        maxTimer = enemy.CombatMovementTimer;
        bestEnemy = enemy;
    }
}

return bestEnemy;
```

하지만 이번 프로젝트에서는 적이 많지 않으니, 간단하고 직관적인 `OrderBy` 를 사용한다.

<br>

### _**공격 상태**_

공격상태 클래스인 `AttackState.cs` 스크립트를 작성한다.

우선 이 스크립트를 생성 후, 이 상태도 마찬가지로 `EnemyController.cs` 에 추가한다.

```csharp
public enum EnemyStates
{
    IDLE,
    CombatMovement,
    ATTACK, // ADD
}

private void Start()
{
    NavAgent = GetComponent<NavMeshAgent>();
    Animator = GetComponent<Animator>();
    Fighter = GetComponent<MeleeFighter>();
    
    stateDict = new Dictionary<EnemyStates, State<EnemyController>>();

    stateDict[EnemyStates.IDLE] = GetComponent<IdleState>();
    stateDict[EnemyStates.CombatMovement] = GetComponent<CombatMovementState>();
    stateDict[EnemyStates.ATTACK] = GetComponent<AttackState>(); // ADD
    
    StateMachine = new StateMachine<EnemyController>(this);
    StateMachine.ChangeState(stateDict[EnemyStates.IDLE]);
}
```
{: file="EnemyController.cs"}

<br>

그리고 현재 적은 플레이어와의 거리를 `3`으로 유지하고 있다.

```csharp
[SerializeField] private float distanceToStand = 3f;
```
{: file="CombatMovementState.cs"}

<br>

공격할 적은 플레이어와 가까워져야하니 이에 대한 변수 `attackDistance` 를 추가한다.

```csharp
public class AttackState : State<EnemyController>
{
    [SerializeField] private float attackDistance = 1f;

    public override void Enter(EnemyController owner)
    {
        enemy = owner;

        enemy.NavAgent.stoppingDistance = attackDistance; // NavMesh 거리 재설정
    }
    public override void Execute()
    {
        enemy.NavAgent.SetDestination(enemy.Target.transform.position);
    }
}
```
{: file="AttackState.cs"}

<br>

플레이어와 적의 거리가 공격가능 거리 안으로 들어오면 공격을 실행한다.

```csharp
public override void Execute()
{
    if (isAttacking) return;
    
    enemy.NavAgent.SetDestination(enemy.Target.transform.position);

    if (Vector3.Distance(enemy.Target.transform.position, enemy.transform.position) <= attackDistance + 0.03f)
    {
        StartCoroutine(Attack());
    }
}
IEnumerator Attack()
{
    isAttacking = true;
    enemy.Animator.applyRootMotion = true;
    
     /* 이 함수를 사용하기 위해서 EnemyController 에 MeleeFighter 클래스 참조 추가 */
    enemy.Fighter.TryToAttack();
    yield return new WaitUntil(() => enemy.Fighter.AttackState == AttackStates.IDLE);
    
    enemy.Animator.applyRootMotion = false;
    isAttacking = false;
    
}
```
{: file="AttackState.cs"}

<br>

콜라이더, 태그와 레이어가 설정되어있는 플레이어의 검을 적 오브젝트에도 추가한다.

![img](2025-06-06-sword.jpg){: .shadow .rounded-10 .w-75 w="1200"}
_Sword Prefab_

<br>

## _**Result**_
---

적이 공격하도록 하는 코드는 잘 작동하지만, 지금은 하나의 적이 플레이어를 계속 반복해서 공격한다.

적이 공격 후에 뒤로 물러나야 하고, 다른 적들에게 공격할 기회가 주어져야 한다.

![img](2025-06-06-result.gif){: .shadow .rounded-10 .w-75 w="1200"}
_Result_

<br>

## _**Reference**_
---

* [Microsoft .NET 8 - Enumerable.OrderBy 메서드](https://learn.microsoft.com/ko-kr/dotnet/api/system.linq.enumerable.orderby?view=net-8.0)
* [Microsoft .NET 8 - List.Contains 메서드](https://learn.microsoft.com/ko-kr/dotnet/api/system.collections.generic.list-1.contains?view=net-8.0)