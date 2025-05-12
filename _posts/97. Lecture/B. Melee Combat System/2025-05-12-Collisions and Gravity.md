---
title: Collisions and Gravity
description: Game Development
author: Minssuy99
date: 2025-05-12 17:30:00 +0900
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

## _**Collision**_
---

### _**Character Controller**_

충돌 처리 방식에 대해서는 `Rigidbody` 와 `CharacterController` 을 주로 사용한다.

이번 프로젝트에서는 `CharacterController` 을 사용한다.

![img](2025-05-12-Character Controller.jpg)

* `Radius` : `0.2`
* `Height` : 캐릭터 키와 맞춰 `1.7` 정도로 설정한다.
* `Center Y` : 키의 절반 보다 조금 위인 `0.9` 정도로 설정하여 땅에 더 잘닿게 한다.
  * 공식문서에 의하면, `Skin Width` 는 `Radius` 의 `10%` 정도가 좋다고 한다.
  * `Height / 2` + `Skin Width` 의 정도로 설정하면 땅에 잘 닿는다고 한다.
* `Center Z` : 대부분 충돌이 캐릭터 오브젝트의 앞쪽에서 발생하기 때문에 `0.08` 정도로 설정한다.

<br>

## _**Script**_

### _**void GroundCheck()**_

플레이어가 땅에 닿아 있는지 검사하는 기능이다.

* `Physics.CheckSphere()`
  * 가상의 투명한 구체를 만들어 그 안에 충돌 가능한 오브젝트가 있는지 검사한다.
  * `True` 또는 `false` 로 반환한다.
* `transform.TransformPoint(groundCehckOffset)`
  * `groundCheckOffset` 은 로컬좌표 기준으로 바닥의 검사위치를 나타내는 `Vector 3` 타입 변수이다.

```csharp
[SerializeField] Vector3 groundCheckOffset;
[SerializeField] LayerMask groundLayer;

void Update()
{
  GroundCheck();
  if (isGrounded)
  {
      ySpeed = -0.5f;
  }
  else
  {
      ySpeed += Physics.gravity.y * Time.deltaTime;
  }
}

void GroundCheck()
{
    isGrounded = Physics.CheckSphere(transform.TransformPoint(groundCheckOffset),
                                                  groundCheckRadius, groundLayer);
}
```

<br>

### _**void OnDrawGizmosSelected()**_

위의 코드에서 만든 가상의 구체를 기즈모로 에디터 상에서 확인할 수 있게 하는 기능이다.

```csharp
void OnDrawGizmosSelected()
{
    Gizmos.color = new Color(0, 1, 0, 0.5f);
    Gizmos.DrawSphere(transform.TransformPoint(groundCheckOffset), groundCheckRadius);
}
```

![img](2025-05-12-Gizmos.jpg)

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

    private void Awake()
    {
        cameraController = Camera.main.GetComponent<CameraController>();
        animator = GetComponent<Animator>();
        characterController = GetComponent<CharacterController>();
    }

    void Update()
    {
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

![img](2025-05-12-Result.gif)
_충돌할 오브젝트의 Layer 을 Obstacle 로 지정한다_ 

<br>

## _**Reference**_
---

* [Unity Docs - Character Controller Component Reference](https://docs.unity3d.com/2022.3/Documentation/Manual/class-CharacterController.html)