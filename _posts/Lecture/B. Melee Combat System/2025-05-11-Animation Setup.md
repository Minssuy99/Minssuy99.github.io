---
title: 3. Animation Setup
description: Game Development
author: Minssuy99
date: 2025-05-11 22:30:00 +0900
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

## _**Model**_
---

### _**모델에 애니메이션이 적용되지 않는 현상**_

`Mixamo` 에서 다운로드한 캐릭터와 애니메이션은 기본적으로 해당 캐릭터 전용으로

설정되어 있어서 다른 모델에 적용하면 제대로 작동하지 않는 경우가 많다.

이유는 스켈레톤 `Skeleton` 구조는 같더라도, 본 `Bone`의 이름이나 계층 구조가 다르기 때문이다.

![img](2025-05-11-model.jpg)

<br>

애니메이션은 본의 위치 `Position` 와 회전 `Rotation` 값을 키프레임으로 저장하고

이를 재생하는 방식이기 때문에 이름이 다르면 해당 본을 찾을 수 없어 애니메이션이 작동하지 않게 된다.

![img](2025-05-11-animation prev.jpg)
_Y Bot 모델과 본의 이름이 다르다_

<br>

### _**Humanoid Retargeting**_

`Unity` 는 이 문제를 해결하기 위해 `Humanoid Retargeting` 기능을 제공한다.

`Generic` 타입의 리깅을 `Humanoid` 로 바꾸면, `Unity` 가 해당 모델의 본들을

`Avatar` 라는 표준 휴머노이드 본 구조에 매핑해준다.

![img](2025-05-11-Inspector.jpg)
_Animation Type : Generic → Humanoid_

이 과정을 통해 서로 다른 본 이름을 가진 모델들끼리도 동일한 애니메이션을 공유할 수 있게 되고

`Avatar` 라는 골격구조 정보를 저장하는 데이터 파일이 생성된다.

<br>

## _**Animation**_
---

### _**인스펙터 변경**_

애니메이션 파일도 마찬가지로, 인스펙터에서 `Animation Type` 을 `Humanoid` 로 변경해준 뒤,

`Avatar Definition` 은 `Copy From Other Avartar` 을 선택하고, 모델을 통해 생성된 아바타 파일을 선택한다.

![img](2025-05-11-Animation Inspector.jpg)

`Copy From Other Avartar` 는 기준이 되는 레퍼런스 아바타가 있을 경우

그 구조에 맞춰 애니메이션을 적용할 수 있도록 해 준다.

<br>

### _**Root Motion 제어**_

`Root Motion` 이 활성화 되면, 이로 인해 캐릭터가 애니메이션만으로 `transform` 이 움직이게 된다.

하지만 이 프로젝트에선 캐릭터 이동을 코드로 제어하기 때문에, `Root Motion` 을 비활성화 한다.

이럴 땐 애니메이션 파일의 인스펙터 설정을 다음과 같이 바꿔준다

* `Root Transform Rotation`
    * `Bake Into Pose` : True
    * `Based Upon` : Original
* `Root Transform Position (Y)`
    * `Bake Into Pose` : True
    * `Based Upon` : Original
* `Root Transform Position (XZ)`
    * `Bake Into Pose` : True
    * `Based Upon` : Original


이렇게 하면 루트 모션이 제거되어, 애니메이션은 재생되지만 실제 위치 이동은 발생하지 않는다.

![img](2025-05-11-before.gif){: .w-50}
_Before_

![img](2025-05-11-after.gif){: .w-50}
_After_

<br>

### _**Blend Tree**_

`Blend Tree` 는 여러 애니메이션 클립을 하나의 파라미터 값을 기준으로

자연스럽게 전환 `Blend` 시켜주는 `Unity` 의 애니메이션 시스템 기능이다.

![img](2025-05-11-blend animation.gif)
_Example_

<br>

또한 각 애니메이션 클립의 임계값을 정하여 조금 더 자연스러운 움직임 연출이 가능하다.

우측의 인스펙터 창에서 `Automate Thresholds` 체크박스를 `false` 로 바꿔주면 사용자임의로 설정이 가능하다.

![img](2025-05-11-Blend Tree.jpg)
_Inspector_



<br>

이전 [코드(클릭)](https://minssuy99.github.io/posts/Character-Movement/) 에서 움직임에 대한 입력값을 `float` 형태의 `moveAmount` 변수로 받고 있었다.

`CharacterController.cs` 에 아래와 같은 로직을 추가해준다.

```csharp
public class PlayerController : MonoBehaviour
{
    Animator animator;

    private void Awake()
    {
        animator = GetComponent<Animator>();
    }

    void Update()
    {   
        .
        .
        // Mathf.Clamp01 값을 0과 1 사이로 제한하는 함수
        float moveAmount = Mathf.Clamp01((Mathf.Abs(h) + Mathf.Abs(v)));
        .
        .
        .
        // 애니메이터 파라미터 이름, Value, 지연시간(DampTime), DeltaTime
        animator.SetFloat("moveAmount", moveAmount, 0.2f, Time.deltaTime);
    }
}

```
{: file="PlayerController.cs"}

<details>
    <summary><i>전체 코드</i></summary>
<div markdown ="1">

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using Unity.Mathematics;
using UnityEngine;

public class PlayerController : MonoBehaviour
{
    [SerializeField] float moveSpeed = 5f;
    [SerializeField] float rotationSpeed = 500f;
    
    Quaternion targetRotation;
    
    CameraController cameraController;
    Animator animator;

    private void Awake()
    {
        cameraController = Camera.main.GetComponent<CameraController>();
        animator = GetComponent<Animator>();
    }

    void Update()
    {
        float h = Input.GetAxis("Horizontal");
        float v = Input.GetAxis("Vertical");

        float moveAmount = Mathf.Clamp01((Mathf.Abs(h) + Mathf.Abs(v)));
        
        var moveInput = (new Vector3(h, 0, v)).normalized;

        var moveDir = cameraController.PlanarRotation * moveInput;

        if (moveAmount > 0)
        {
            transform.position += moveDir * (moveSpeed * Time.deltaTime);
            targetRotation = Quaternion.LookRotation(moveDir);
        }
        
        transform.rotation = Quaternion.RotateTowards(transform.rotation, targetRotation, 
            rotationSpeed * Time.deltaTime);
        
        animator.SetFloat("moveAmount", moveAmount, 0.2f, Time.deltaTime);
    }
}
```
{: file="PlayerController.cs"}

</div>
</details>


<br>

## _**Result**_
---

![img](2025-05-11-Result.gif)

