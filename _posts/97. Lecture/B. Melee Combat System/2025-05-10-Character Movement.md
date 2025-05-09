---
title: Character Controller
description: Game Development
author: Minssuy99
date: 2025-05-10 02:00:00 +0900
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

## _**Updated CameraController.cs**_
---

```csharp
.
.
        transform.position = focusPosition - targetRotation * new Vector3(0, 0, distance);
        transform.rotation = targetRotation;
    }

    // Added Code
    public Quaternion PlanarRotation => Quaternion.Euler(0, rotationY, 0);

    /* same logic as above*/
    public Quaternion GetPlanarRotation()
    {
        return Quaternion.Euler(0, rotationY, 0);
    }
}
```
{: file="CameraController.cs"}

<br>

## _**Variable**_
---

|name            |Description                                                |
|----------------|-----------------------------------------------------------|
|MoveSpeed       |플레이어가 움직이는 속도                                   |
|RotationSpeed   |플레이어가 회전하는 속도 (초당 회전 각도)                  |
|TargetRotation  |캐릭터가 바라봐야 할 목표 방향                             |
|CameraController|`Main Camera` 에 붙어 있는 `CameraController` 컴포넌트 참조|

<br>

## _**📑 PlayerController Class**_
---

### _**⚙ Awake()**_

```csharp
private void Awake()
{
    cameraController = Camera.main.GetComponent<CameraController>();
}
```
* 게임 시작 시, 메인 카메라 `Camera.main` 에서 `CameraController` 스크립트를 찾아 변수에 저장한다.
* `CameraController` 안에 있는 `PlanarRotation` 값을 사용하기 위해 필요하다.

#### _**PlanarRotation**_

```csharp
public Quaternion planarRotation => Quaternion.Euler(0, rotationY, 0);
```
* 표현식 본문으로 선언된 읽기 전용 프로퍼티.
* 현재 카메라의 `y` 축 회전만 가져온 회전값 `Quaternion` 을 반환한다.
* 카메라가 바라보는 방향을 기준으로 캐릭터의 이동 방향을 정하기 위해 사용한다.

<br>

### _**⚙ Update()**_

```csharp
float h = Input.GetAxis("Horizontal");
float v = Input.GetAxis("Vertical");
```
* 플레이어의 키보드를 입력받는다.
    * `Horizontal` : `A [-1]` ↔`D [+1]`
    * `Vertical` : `S [-1]` ↔ `W [+1]`

<br>

```csharp
float moveAmount = Mathf.Abs(h) + Mathf.Abs(v);
```
* 얼마나 움직였는지 계산한다.

<br>

```csharp
var moveInput = (new Vector3(h, 0, v)).normalized;
```
* 입력 벡터를 `3D` 공간의 방향 벡터로 변환한다.
* `normalized` 를 사용하여 방향만 유지하고 방향벡터의 크기를 `1` 로 만든다.

<br>

```csharp
var moveDir = cameraController.PlanarRotation * moveInput;
```
* 카메라의 방향을 기준으로 회전된 이동 방향을 계산한다.
* 카메라의 `y` 축 회전만 반영한 `PlanarRotation` 을 곱해서, 카메라를 기준으로 움직이게 한다.

<br>

```csharp
if (moveAmount > 0)
{
    transform.position += moveDir * (moveSpeed * Time.deltaTime);
    targetRotation = Quaternion.LookRotation(moveDir);
}
```
* 입력이 있다면, 플레이어를 그 방향으로 움직이고, 바라봐야 할 방향을 `moveDir` 로 설정한다.

<br>

```csharp
transform.rotation = Quaternion.RotateTowards(transform.rotation, targetRotation,
                                                 rotationSpeed * Time.deltaTime);
```
* `Quaternion.RotateTowards` : `transform.rotation` 에서 `targetRotation` 방향으로 부드럽게 회전한다.
* 세번째 인자인 `rotationSpeed * Time.deltaTime` 은 한 프레임에 회전할 최대 각도이다.

<br>

## _**Complete Script**_
---

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

    private void Awake()
    {
        cameraController = Camera.main.GetComponent<CameraController>();
    }

    void Update()
    {
        float h = Input.GetAxis("Horizontal");
        float v = Input.GetAxis("Vertical");

        float moveAmount = Mathf.Abs(h) + Mathf.Abs(v);
        
        var moveInput = (new Vector3(h, 0, v)).normalized;

        var moveDir = cameraController.PlanarRotation * moveInput;

        if (moveAmount > 0)
        {
            transform.position += moveDir * (moveSpeed * Time.deltaTime);
            targetRotation = Quaternion.LookRotation(moveDir);
        }
        transform.rotation = Quaternion.RotateTowards(transform.rotation, targetRotation, 
                                                         rotationSpeed * Time.deltaTime);
    }
}
```
{: file="CharacterController.cs"}

<br>

## _**Result**_
---

![img](2025-05-10-Character move.gif)

<br>

<!--------------------------------------Reference-------------------------------------->


## _**Reference**_
---

* [Unity Docs - Quaternion.RotateTowards](https://docs.unity3d.com/2022.3/Documentation/ScriptReference/Quaternion.RotateTowards.html)
* [Happy's Tistory - 회전에서 Quaternion.Slerp(), Quaternion.RotateTowards()의 차이](https://mj119.tistory.com/195)
* [끄적끄적 코딩공방's Tistory - 헷갈리는 오브젝트 회전 방법 Part 2 (Lerp, Slerp, RotateTowards)](https://coding-shop.tistory.com/248)