---
title: Rigidbody
description: Rigidbody is a Unity component that enables physics-based movement for objects
author: Minssuy99
date: 2025-01-20 14:40:00 +0900
categories: [Unity, Wiki]
tags: [Unity, Input System]
pin: false
# math: false
# mermaid: false
image:
  path: 'thumbnail.jpg'
  lqip: data:image/jpeg;base64,iVBORw0KGgoAAAANSUhEUgAAADIAAAAaCAAAAADQq1A2AAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAACxMAAAsTAQCanBgAAADFSURBVDjLY2AYBUMB8JKqweq/0X99UjTwnf3/lOH9/0McxGtZ/f89kPz5fwaD+xmGQ/5AdmvITjm8Wub9//9/LhD3MAS+ZbiXcen/9OSK/0cOC+7WKLXAoWXRd4a1/xcy/O9nCH7CcDnrP8P/lqxrkjXd/xk6bHBo2bGSQeo/B8O+JQxq/wP+2wO1dBXcL2b4vxi3wzgazpr+17pWzM7A4LLMi6GSodrCUK+S878pHs+k/QeBGFTB+Er8gTbzf99och6iAADgXk8NioFvJgAAAABJRU5ErkJggg==
  # alt:
media_subpath: '/assets/img/Unity'
---
<!------------------------------------------------------------------------>
## Bookmark
---

{% include embed/bookmark.html
url="https://docs.unity3d.com/kr/2022.3/Manual/class-Rigidbody.html"
title="Unity docs - Rigidbody"
description="Rigidbody is a Unity component that enables physics-based movement for objects, applying forces, gravity, and collisions." %}

<br>
<!------------------------------------------------------------------------>

## Rigidbody
---

`Rigidbody`란 **강체**를 뜻하며, 물리학에서 어떠한 힘을 받아도 변형이 일어나지 않는 가상의 물체이다.

아래는 `Unity` 의 `Rigidbody` 컴포넌트에서 설정할 수 있는 주요 속성과 기능에 대한 정리이다.

### Mass

물체의 질량, 단위는 kg 이다.

<br>

### Drag

물체가 받는 공기 저항력을 나타낸다.

<br>

### Angular Drag

오브젝트의 회전 저항력. 이는 공기 저항력이 아닌, 회전 자체에 대한 저항이다.

<br>

### Automatic Center Of Mass

오브젝트의 무게중심(중력을 받는 중심점)을 자동으로 설정할지 결정하는 옵션이다.

- **Center Of Mass [X, Y, Z]** : 무게중심의 좌표를 나타낸다.

<br>

### Automatic Tensor

* 오브젝트가 회전할 때의 물리적 성질을 자동으로 계산할지 설정한다.
* **Inertia Tensor [X, Y, Z]** : 물체가 각 축(X, Y, Z)에서 회전할 때 저항하는 정도를 나타낸다.
    * 물체의 형태와 질량 분포에 따라 값이 달라진다.
    * 예를 들어, 긴 막대는 가로로 회전(X축)할 때 저항이 크고, 세로로 회전(Z축)할 때 저항이 적을 수 있다.
* **Inertia Tensor Rotation [X, Y, Z]** : 관성 텐서가 기준으로 삼는 회전 축의 방향을 조정한다.

<br>

### 관성 텐서(Inertia Tensor)

- 물체가 각 축에서 회전할 때 얼마나 저항하는지를 나타낸다.
- 예: [10, 5, 1]
    - X축(가로): 10 (회전하기 어려움)
    - Y축(세로): 5 (조금 쉬움)
    - Z축(앞뒤): 1 (가장 쉬움)

쉽게 말하자면, 관성 텐서는 물체의 회전에 대한 저항 크기를 숫자로 나타내는 값.

<br>

### Use Gravity

물체가 중력의 영향을 받을지 여부를 결정한다.

<br>

### Is Kinematic

- 활성화되면 물체는 물리엔진으로 제어되지 않고 오직 `Transform` 으로만 조작된다.
- 물리엔진의 힘, 중력, 충돌 등의 영향을 받지 않지만, 충돌은 감지할 수 있다.

<br>

### Interpolate

물체의 움직임을 부드럽게 처리하는 방법을 설정한다.

* **None** : 보간을 사용하지 않는다.
* **Interpolate** : 이전 프레임의 `Transform`에 맞게 움직임을 부드럽게 처리한다.
    * 낮은 프레임에서 플레이어 이동이 끊기거나 튀는 현상을 줄인다.
* **Extrapolate** : 다음 프레임의 `Transform` 을 예측해 움직임을 부드럽게 처리한다.
    * 빠르게 움직이는 물체(예: 총알)에서 유용하다.
    
<br>

### Collision Detection (충돌 감지)

물체 간의 충돌을 감지하는 방식을 설정한다.

* **Discrete** : 기본값으로, 가벼운 연산을 사용해 충돌을 감지한다.
* **Continuous** : 빠르게 움직이는 물체에서도 충돌을 정확히 감지한다.
* **Continuous Dynamic** : 움직이는 오브젝트에 대해 더욱 정밀한 충돌 감지를 수행한다.
* **Continuous Speculative** : 충돌을 예측하여 감지하며, 가장 무거운 연산을 사용한다.

![image](241230-test.gif)

위의 빨간색 큐브는 `Discrete` 로 설정되어 있고, 아래의 빨간색 큐브는 `Continuous` 로 설정되어 있다.

참고로 빨간색 큐브엔 아래와 같이 간단하게 스크립트를 작성하였다.

```csharp
public class Test : MonoBehaviour
{
    Rigidbody rb;
    [SerializeField] float speed = 10.0f;
    void Awake()
    {
        rb = GetComponent<Rigidbody>();
    }

    void FixedUpdate()
    {
        rb.AddForce(new Vector3(1, 0, 0) * speed, ForceMode.Force);
    }
}
```

<br>

### Constraints (제약)

물체의 움직임이나 회전을 특정 축으로 제한한다.
* **Freeze Position**: 각 축으로 움직이지 않도록 고정한다.<br>
물리엔진에 의해 제한되지만, Transform으로는 조작 가능하다.
* ***Freeze Rotation**: 각 축으로 회전하지 않도록 고정한다.

<br>

### Layer Overrides
* 특정 레이어에 속한 오브젝트들 간의 충돌 여부를 설정한다.
* **Include Layers**: 충돌이 허용되는 레이어를 선택한다.
* **Exclude Layers**: 충돌이 무시되는 레이어를 선택한다.

레이어는 **`Edit > Project Settings > Tags and Layers`** 에서 추가할 수 있다.

<br>

## Reference
---

* [Unity Docs - Rigidybody](https://docs.unity3d.com/kr/2022.3/Manual/class-Rigidbody.html)
* [Unity Docs - Physics](https://docs.unity3d.com/kr/current/Manual/class-PhysicsManager.html)