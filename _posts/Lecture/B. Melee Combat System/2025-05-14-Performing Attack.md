---
title: 5. Performing Attack
description: Game Development
author: Minssuy99
date: 2025-05-14 08:00:00 +0900
categories: [Lecture, Melee Combat System]
tags: [C#, Unity]
pin: false
math: true
# mermaid: false
image:
  path: 'thumbnail.jpg'
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAFCAYAAAB8ZH1oAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsQAAA7EAZUrDhsAAADFSURBVBhXDcuxTsJAHIDx7393LW3vKgRjCYiDAzFOJuICg4smvohPweRbMTLBapwdkIRAoomNJEgK0rPDt/0+sc555xzdi4w4ianbkiQJ8brBfldw1UtZrTYoAY5Hz+FPY1RAURyqSkSFNJsp80XOd/6LBEHkh8MenVbK69uS0ei5Opd0zzOsjdHGICh0ZOOXE6e5vTlju91hylPGkzU188V09s78Y02nHSIPj/d+cNfGRvCz+aygZpELT/0Gl9cZIgovin+18zpj6AM9igAAAABJRU5ErkJggg==
  # alt:
media_subpath: '/assets/img/posts/Lecture/B. Melee Combat System'
---
<!---------------------------------------Header-------------------------------------->

## _**Add Attack Animation**_
---

### _**Inspector**_

다른 애니메이션 에셋과 마찬가지로 설정을 아래와 같이 진행한다.

#### _**Rig**_

* `Animation Type` : `Humanoid`
* `Avatar Definition` : `Copy From Other Avatrar`

#### _**Animation**_

* `Root Transform Rotation`
  * `Bake Into Pose` : True
  * `Based Upon` : Original
* `Root Transform Position (Y)`
  * `Bake Into Pose` : True
  * `Based Upon` : Original
* `Root Transform Position (XZ)`
  * `Bake Into Pose` : False
  * `Based Upon` : Original

### _**Animator**_

`Override Layer` 라는 새로운 레이어를 추가한다.

가중치 `Weight`를 `1` 로 설정하여 기본 레이어를 완전히 덮어쓰도록 한다.

![img](2025-05-14-Layer.jpg)

공격 애니메이션 `Slash` 완료 후, `Empty` 상태로 전환하는 트랜지션을 추가한다.

<br>

## _**Code**_
---

공격 애니메이션 실행을 위한 로직을 작성한다.

공격 애니메이션은 적 오브젝트도 사용할 예정이기 때문에, 모듈식 구성으로 작성한다.

<br>

### _**MeleeFighter.cs**_

실질적으로 공격 애니메이션을 구현하는 스크립트를 작성한다.

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

        // 현재 재생중인 애니메이션의 20% 만큼 블렌딩. 자연스러운 전환.
        animator.CrossFade("Slash", 0.2f);

        yield return null; // 1프레임 대기

        // 다음 프레임에 재생될 애니메이션 정보를 가져오는 함수. 파라미터는 Layer를 의미함.
        var animState = animator.GetNextAnimatorStateInfo(1);
        
        // slash 애니메이션의 재생시간 만큼 기다림.
        yield return new WaitForSeconds(animState.length);
        
        // 공격이 끝났으니 다시 공격 가능하도록 상태 초기화.
        InAction = false;
    }
}
```

<br>

### _**CombatController.cs**_

플레이어가 공격 애니메이션을 수행하기 위한 로직을 작성한다.

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class CombatController : MonoBehaviour
{
    MeleeFighter meleeFighter;

    private void Awake()
    {
        meleeFighter = GetComponent<MeleeFighter>();
    }

    private void Update()
    {
        if (Input.GetButtonDown("Attack"))
        {
            meleeFighter.TryToAttack();
        }
    }
}
```

<br>

이 상태에서 게임을 실행하면 아래와 같이, 움직이면서 공격모션이 나가는 모습이 보여진다.

![img](2025-05-14-Attack1.gif){: width="1200" height="630"}

이를 위해서, 공격 애니메이션이 실행되는 동안엔 움직여지지 않도록 `PlayerController.cs` 를 수정한다.

<br>

### _**PlayerController.cs**_

`Update` 문 안에 아래와 같이 추가해준다.

```csharp
void Update()
{
    if (meleeFighter.InAction)
    {
        return;
    }
}
```

<br>

![img](2025-05-14-Attack2.gif){: width="1200" height="630"}

이젠 공격 애니메이션이 실행되는 동안에는 움직여지지 않지만 사소한 문제가 있다.

현재 위의 사진을 보면, 공격 애니메이션이 끝난 뒤에 캐릭터가 즉시 `Run` 애니메이션을 하는 것을 볼 수 있다.

<br>

캐릭터가 이동 중 플레이어가 공격을 하게되면, `moveAmount` 의 값이 `1` 로 되어있기 때문에

`Blend Tree` 의 `1` 에 대응하는 값인 `Run` 상태로 즉시 넘어가게 되는 것이다.

이를 방지하기 위한 코드를 추가해준다.

```csharp
void Update()
{
    if (meleeFighter.InAction)
    {
        animator.SetFloat("moveAmount", 0f); // 공격을 하면 moveAmount 는 0으로 적용.
        return;
    }
}
```

<details>
    <summary><i>전체 코드</i></summary>
<div markdown ="1">

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using Unity.Mathematics;
using Unity.VisualScripting;
using UnityEngine;

public class PlayerController : MonoBehaviour
{
    [SerializeField] float moveSpeed = 5f;
    [SerializeField] float rotationSpeed = 500f;
    
    [Header("Ground Check Settings")]
    [SerializeField] float groundCheckRadius = 0.2f;
    [SerializeField] Vector3 groundCheckOffset;
    [SerializeField] LayerMask groundLayer;
    
    bool isGrounded;

    float ySpeed;
    
    Quaternion targetRotation;
    
    CameraController cameraController;
    Animator animator;
    CharacterController characterController;
    MeleeFighter meleeFighter;

    private void Awake()
    {
        cameraController = Camera.main.GetComponent<CameraController>();
        animator = GetComponent<Animator>();
        characterController = GetComponent<CharacterController>();
        meleeFighter = GetComponent<MeleeFighter>();
    }

    void Update()
    {
        if (meleeFighter.InAction)
        {
            animator.SetFloat("moveAmount", 0f);
            return;
        }
        
        float h = Input.GetAxis("Horizontal");
        float v = Input.GetAxis("Vertical");

        float moveAmount = Mathf.Clamp01((Mathf.Abs(h) + Mathf.Abs(v)));
        
        var moveInput = (new Vector3(h, 0, v)).normalized;

        var moveDir = cameraController.PlanarRotation * moveInput;
        
        GroundCheck();
        if (isGrounded)
        {
            ySpeed = -0.5f;
        }
        else
        {
            ySpeed += Physics.gravity.y * Time.deltaTime;
        }
        
        var velocity = moveDir * moveSpeed;
        velocity.y = ySpeed;
        
        characterController.Move(velocity * Time.deltaTime);

        if (moveAmount > 0)
        {
            targetRotation = Quaternion.LookRotation(moveDir);
        }
        
        transform.rotation = Quaternion.RotateTowards(transform.rotation, targetRotation, 
            rotationSpeed * Time.deltaTime);
        
        animator.SetFloat("moveAmount", moveAmount, 0.2f, Time.deltaTime);
    }

    void GroundCheck()
    {
        isGrounded = Physics.CheckSphere(transform.TransformPoint(groundCheckOffset), groundCheckRadius, groundLayer);
    }

    void OnDrawGizmosSelected()
    {
        Gizmos.color = new Color(0, 1, 0, 0.5f);
        Gizmos.DrawSphere(transform.TransformPoint(groundCheckOffset), groundCheckRadius);
    }
}
```
{: file="PlayerController.cs"}

</div>
</details>

<br>

## _**Result**_
---

![img](2025-05-14-Result.gif){: width="1200" height="630"}