---
title: 1. Camera Controller
description: Game Development
author: Minssuy99
date: 2025-05-09 09:00:00 +0900
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
## _**Variable**_
---

|name            |Description                                    |
|----------------|-----------------------------------------------|
|FollowTarget    |카메라가 따라갈 대상의 `Transform`             |
|RotationSpeed   |카메라 회전 속도                               |
|Distance        |대상과 카메라의 거리                           |
|MinVerticalAngle|카메라의 수직 회전 최소 각도                   |
|MaxVerticalAngle|카메라의 수평 회전 최대 각도                   |
|FramingOffset   |대상을 기준으로 카메라의 중심 변경             |
|RotationX       |카메라 수직 회전값                             |
|RotationY       |카메라 수평 회전값                             |
|InvertX         |마우스 `X` 축 입력 반전 여부 (좌우 반전)       |
|InvertY         |마우스 `Y` 축 입력 반전 여부 (상하 반전)       |
|InvertXVal      |`InvertX` 설정에 따라 `X` 축 회전에 곱해지는 값|
|InvertYVal      |`InvertY` 설정에 따라 `Y` 축 회전에 곱해지는 값|

<br>

## _**Cursor Class**_
---

### _**Cursor.visible**_

* _Description_ : 커서가 화면에 보일지 여부를 설정하는 속성이다.
* _Type_ : `Bool`
* _Property_
    * `true` : 커서가 화면에 보임.
    * `false` : 커서가 화면에 보이지 않음.

```csharp
Cursor.visible = false; // 커서 숨김
```
{: file="Example.cs"}

<br>

### _**Cursor.lockState**_

* _Description_ : 커서의 잠금 상태를 설정하는 속성이다.
* _Type_ : `CursorLockMode` `Enum`
* _Property_
    * `None` : 커서가 잠기지 않으며 화면 밖으로 나갈 수 있음. (기본값)
    * `Locked` : 커서가 화면 중앙에 고정되며, 화면 밖으로 나가지 못함.<br>
                 이 상태에서는 `visible` 속성과 관계 없이 커서가 표시되지 않음.
    * `Confined` : 커서가 화면 밖으로 나갈 수 없음.

```csharp
Cursor.lockState = CursorLockMode.Locked; // 커서 숨김
```
{: file="Example.cs"}

<br>

## _**Complete Script**_
---

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class CameraController : MonoBehaviour
{
    [SerializeField] Transform followTarget;

    [SerializeField] private float rotationSpeed = 2f;
    [SerializeField] private float distance = 5f;
    
    [SerializeField] float minVerticalAngle = -45f;
    [SerializeField] float maxVerticalAngle = 45f;

    [SerializeField] private Vector2 framingOffset;

    [SerializeField] private bool invertX;
    [SerializeField] private bool invertY;
    

    private float rotationX;
    private float rotationY;

    private float invertXVal;
    private float invertYVal;

    private void Start()
    {
        Cursor.visible = false; // 커서 숨김
        Cursor.lockState = CursorLockMode.Locked; // 커서 화면중앙 고정
    }

    void Update()
    {
        invertXVal = (invertX) ? -1 : 1; // 좌우반전
        invertYVal = (invertY) ? -1 : 1; // 상하반전
        
        rotationX += Input.GetAxis("Mouse Y") * invertYVal* rotationSpeed;
        rotationX = Mathf.Clamp(rotationX, minVerticalAngle, maxVerticalAngle); // 각도제한

        rotationY += Input.GetAxis("Mouse X") * invertXVal * rotationSpeed;
        
        var targetRotation = Quaternion.Euler(rotationX, rotationY, 0);

        var focusPosition = followTarget.position + new Vector3(framingOffset.x, framingOffset.y);
        
        transform.position = focusPosition - targetRotation * new Vector3(0, 0, distance);
        transform.rotation = targetRotation;
    }
}
```
{: file="CameraController.cs"}

<br>

## _**Result**_
---

![img](2025-05-09-Camera move.gif)

<br>

<!--------------------------------------Reference-------------------------------------->


## _**Reference**_
---

* [Unity Documentation - Cursor](https://docs.unity3d.com/2022.3/Documentation/ScriptReference/Cursor.html)