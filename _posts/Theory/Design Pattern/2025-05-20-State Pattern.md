---
title: State Pattern
description: Design Pattern for Game Development
author: Minssuy99
date: 2025-05-20 17:20:00 +0900
categories: [Theory, Design Pattern]
tags: [Design Pattern]
pin: false
# published : false
# permalink: /posts/Input_Title_At_Here/
# math: false
# mermaid: false
image:
  path: 'thumbnail.png'
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAFCAYAAAB8ZH1oAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsQAAA7EAZUrDhsAAACxSURBVBhXLY4/C4FRGMV/9xKh9KZkJzEYLJQ/s2QyKt/CavEJ5EtY7Bab0WKhDDJgFhaJ+9zL+/ae4enU6fzOo0azpeMvYwQvnaRaKeCsYb05ENEapZQfo/1jrSXjpcgXiyxWey6PGP1Ojc/H4FzAIdLoDie+Eac4nm/Mp2Oud0HiWRLRLyI2oAbEQP/m8/mi3RvQatap5lO8398wDKf9hoiQ86BQKhPFsN2d0FqFP8IP8dg6d0cGIlEAAAAASUVORK5CYII=
  # alt:
media_subpath: '/assets/img/posts/Theory/DesignPattern'
---

<!-------------------------------------------------------------->

## _**상태 패턴을 배워야하는 이유**_
---

`2D` 횡스크롤 게임에서 이동, 점프, 엎드리기, 엎드려서 기모으기 등의 동작을 만든다고 가정한다.

스페이스바를 누르면 점프가 되도록 구현하되, 이단 점프는 허용하지 않는다면, 아래의 로직처럼 작성할 수 있다.

```csharp
void Update()
{
  if(intput.GetKeyDown(KeyCode.Space))
  {
    StartCoroutine("Jump");
  }
}
```

<br>

하지만 이 코드엔 이단 점프를 막는 로직이 없으니, 플래그 변수를 선언하여 이와 같이 만들 수 있다.

```csharp
void Update()
{
  if(intput.GetKeyDown(KeyCode.Space))
  {
    if(!isJumping) // 점프 중이 아니라면
    {
      isJumping = true;
      StartCoroutine("Jump");
    }
  }
}
```

<br>

여기서 캐릭터가 땅에 있을 때, <KBD>↓</KBD> 방향키를 누르면 엎드리고, 떼면 다시 일어서는 기능을 추가한다.

```csharp
void Update()
 {
    if (Input.GetKeyDown(KeyCode.Space))
    {
        if (!isJumping)
        {
            isJumping = true;
            StartCoroutine("Jump");
        }
    }
    else if (Input.GetKeyDown(KeyCode.DownArrow))
    {
        if (!isJumping) // 점프 중일 땐 엎드리기 X
        {
            StartCoroutine("Down");
        }
    }
    else if (Input.GetKeyUp(KeyCode.DownArrow)) // 아래 방향키를 떼면 일어서기
    {
        StartCoroutine("Stand");
    }
 }
```

<br>

하지만 이 코드에는 버그가 있다.

엎드린 상태에서 점프가 되고, 그 상태에서 <kbd>↓</kbd> 키를 떼면 점프 중인 상태에서 일어서기까지 된다.

이를 위해 상태변경을 위한 플래그 변수를 추가한다.

```csharp
void Update()
 {
    if (Input.GetKeyDown(KeyCode.Space))
    {
        if (!isJumping && !isDucking) // 점프상태 && 엎드린 상태가 아닐 때만 점프가 되도록
        {
            isJumping = true;
            StartCoroutine("Jump");
        }
    }
    else if (Input.GetKeyDown(KeyCode.DownArrow))
    {
        if (!isJumping)
        {
            isDucking = true;
            StartCoroutine("Down");
        }
    }
    else if (Input.GetKeyUp(KeyCode.DownArrow)) // 아래 방향키를 떼면
    {
        if(isDucking) // 엎드리기가 true 일테니
        {
           isDucking = false; // 엎드리기를 다시 false로 전환
           StartCoroutine("Stand");
        }
       
    }
 }
```

<br>

이번엔 점프 중에 <kbd>↓</kbd> 방향키를 눌러, 내려찍기 공격을 하도록 구현한다.

```csharp
void Update()
 {
    if (Input.GetKeyDown(KeyCode.Space))
    {
        if (!isJumping && !isDucking)
        {
            isJumping = true;
            StartCoroutine("Jump");
        }
    }
    else if (Input.GetKeyDown(KeyCode.DownArrow))
    {
        if (!isJumping)
        {
            isDucking = true;
            StartCoroutine("Down");
        }
        else // 점프 중이라면
        {
          isJumping = false; // 점프를 비활성화 시키고
          StartCoroutine("Attack"); // 공격 기능 추가
        }
    }
    else if (Input.GetKeyUp(KeyCode.DownArrow))
    {
        if(isDucking)
        {
           isDucking = false;
           StartCoroutine("Stand");
        }
       
    }
 }
```

하지만 이 로직은 이단 점프는 검사하지만, 내려찍기 공격 중인지는 검사하지 않는다.

이렇게 된다면, 높은 곳에서 떨어질 때 연속으로 내려찍기 공격이 가능해질 것이다.

그럼 이를 체크하기 위해 `isAttacking` 과 같은 플래그 변수가 또 필요할 것이다.

<br>

아직 걷기와 달리기 같은 움직임과 관련된 로직은 하나도 작성되지 않았다.

코드는 짧지만 조금만 건드려도 망가지는 시한폭탄 같은 코드이다.

<br>

## _**이를 해결하기 위한 FSM ?**_
---

이러한 문제를 해결하기 위해서 `FSM` <sub>Finite State Machine</sub> 로 구현한다.

유한 상태 기계라고 하며, 상태를 `enum` 으로 정의하고 조건문으로 상태를 분기한다.

```csharp
enum State // 상태 정의
{
  STAND,
  MOVE,
  ATTACK,
}

private State _state;

void Update()
{
  switch(_state) // 분기
  {
    case State.IDLE:
        Debug.Log("STAND State.");
        break;
    case State.MOVE:
        Debug.Log("Move State.");
        break;
    case State.ATTACK:
        Debug.Log("Attack State.");
        break;
  }
}
```

각각의 행동을 상태로 분리함으로써 아까보단 로직을 안전하게 구현할 수 있게 되었다.

하지만 상태가 점점 늘어남에 따라 `enum` 과 `switch` 문은 점점 길어지기 때문에 확장에 불리하다.

또한 각 행동끼리 변수를 공유하는 등, 조금이라도 엮이면 상태를 관리하기엔 골치 아파진다.

<br>

## _**확장을 고려한 State Pattern**_
---

이처럼 `enum` 과 `switch` 문을 사용하는 `FSM` 은 간단한 상태 전이에는 효과적이다.

하지만 상태가 많아질수록 조건문이 길어지고, 상태마다 필요한 변수가 달라질 경우 코드가 복잡해지며,

상태 간 전이 조건이 많아질수록 관리가 어려워진다.

이를 해결하기 위해 객체지향 설계 원칙을 따르는 상태 패턴<sub><i>State Pattern</i></sub>을 도입한다.

상태 패턴은 상태마다 클래스를 따로 분리하고, 상태 전이와 상태 내부의 동작을 객체 단위로 관리할 수 있게 해준다.

![img](StatePattern_1.png){: .shadow .rounded-10 .w-75 w="1200"}
_State Pattern UML_

<br>

* 각 상태를 클래스로 정의한다.
* 각 상태 클래스는 공통된 `State` 인터페이스 또는 추상 클래스를 상속받아 동일한 구조를 가진다.
* 현재 상태를 담당하는 객체 `CurrentState`가 매 프레임 자신의 동작을 수행한다.
* 상태 전이는 상태 객체 내부에서 또는 외부 `StateMachine` 을 통해 유연하게 처리된다.

```csharp
public abstract class State // State 정의
{
    public abstract void Enter(); // 상태 진입 시 
    public abstract void Execute(); // 상태가 유지되는 동안
    public abstract void Exit(); // 상태 종료 시
}

/********************************************************************************/

// State 를 상속받은 상태(행동) 클래스들
public class StandState : State { ... }
public class MoveState : State { ... }
public class AttackState : State { ... }

/********************************************************************************/

public class StateMachine // 상태를 관리하고, 상태 전이를 담당하는 상태머신 클래스
{
    private State _current; // 현재 상태를 저장하는 변수
    public void ChangeState(State newState) // 상태를 변경하는 메서드
    {
        _current?.Exit(); // 현재 상태 종료
        _current = newState; // 새로운 상태로 전환
        _current.Enter(); // 새로운 상태 진입
    }

    public void Update() // 현재 상태의 Execute 를 매 프레임 실행
    {
        _current?.Execute();
    }
}
```

<br>

### _**예시**_
---

위의 코드를 스크립트 별로 분리한다.

#### _**State.cs**_

이 스크립트는 인터페이스로 구현되어 있으나, 추상클래스로 구현해도 된다.

이 인터페이스를 상속받은 클래스는 `Action` 메서드를 구현해야한다.

```csharp
public interface State
{
    void Action();
};
```
{: file="State.cs"}

<br>

#### _**Move.cs**_

이동 상태를 정의한 클래스이고, `State` 를 상속받아 `Action()` 을 구현한다.

```csharp
public class Move : State
{
    public void Action()
    {
        Debug.Log("Move");
    }
}
```
{: file="Move.cs"}

<br>

#### _**Attack.cs**_

이동 상태와 마찬가지로, 공격에 대한 상태를 클래스로 정의한다.

```csharp
public class Attack : State
{
    public void Action()
    {
        Debug.Log("Attack");
    }
}
```
{: file="Attack.cs"}

<br>

#### _**Monster.cs**_

`Monster` 는 현재 상태를 보관하고, 그 상태에 맞는 행동을 `Action()` 으로 실행한다.

상태에 따라 행동을 외부에서 위임해서 실행하는 구조이다.

즉, 몬스터 자신이 직접 행동하는 것이 아닌, 현재 상태가 행동을 결정한다.

```csharp
public class Monster
{
    private State state;

    // Constructor
    public Monster(State state) // 생성자. 인스턴스 생성 시 초기 상태 설정.
    {
        this.state = state;
    }

    public void setState(State state) // 게임이 진행되면서 상태를 변경하는 용도.
    {
        this.state = state;
    }

    public void act()  // 현재 설정된 상태의 행동을 실행.
    {
        state.Action();
    }
};
```
{: file="Monster.cs"}

<br>

#### _**Test.cs**_

이 스크립트는 몬스터의 상태를 변경하고 행동이 바뀌는 것을 테스트하는 실행 예제이다.

```csharp
public class Test : MonoBehaviour {

	void Start () {
        Monster monster = new Monster(new Move()); // 생성자 초기상태 초기화
        monster.act();

        monster.setState(new Attack()); // 상태 변경
        monster.act(); // 공격 실행

        monster.setState(new Move());  // 상태 변경
        monster.act(); // 이동 실행
    }
}
```
{: file="StateManager.cs"}

<br>

## _**Reference**_
---

* [dodobug's Tistory - [Unity] FSM - 유한 상태 기계](https://dodobug.tistory.com/16)
* [dofactory - C# State Design Pattern](https://www.dofactory.com/net/state-design-pattern)
* [공부하는 식빵맘 - Chapter 11. 상태패턴(State Pattern)](https://ansohxxn.github.io/design%20pattern/chapter11/#%EC%83%81%ED%83%9C-%ED%8C%A8%ED%84%B4-%EC%A0%95%EC%9D%98-%EB%B0%8F-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0)