---
title: Input System
description: A New Input System to Replace the Classical Input System
author: Minssuy99
date: 2025-01-17 11:00:00 +0900
categories: [Wiki, UnityDocs]
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

## Bookmark
---

{% include embed/bookmark.html
url="https://docs.unity3d.com/Packages/com.unity.inputsystem@1.5/manual/index.html"
title="Unity docs - Input System"
description="A new input system which can be used as a more extensible and customizable alternative to Unity’s classic input system in UnityEngine.Input." %}

<br>

## Scheme
---

* ### Requirement

    * _**Required**_
    
        입력장치가 반드시 필요해야한다.
                
        해당 입력장치가 없다면 해당 `Control Scheme` 은 동작하지않고

        다른 `Control Scheme` 으로 전환된다.

    * _**Optional**_

        입력장치가 선택적으로 존재해도 된다.

        입력장치가 존재하든 존재하지 않든 활성화가 되어있을 수 있다.

    * _**Example**_

        예로들면, 키보드는 Required 로 설정하여 키보드만으로 플레이를 할 수 있도록 설정하고,
        
        마우스는 `Optional` 로 해서 마우스를 이용하면 조금 더 편리하게 게임을 할 수 있도록 한다.

        게임패드는 있어도 되고 없어도 되니 `Optional` 로 해놓는다. 연결되면 자동으로 동작한다.

        <br>

        만약 Required 로 선택된 입력 장치가 없을 시엔 다른 `Control Scheme` 으로 대체되기 때문에
        
        `Null` 처리는 `Input System`이 알아서 한다고 볼 수 있다.

<br>

## Action Type
---

* _**Button**_

    단일 이벤트를 처리할 때 사용된다. 누르거나 뗄 때 한번의 이벤트 처리에 적합하다.

    버튼은 눌림(pressed)과 떼어짐(released) 두 상태만을 고려한다.

    즉, 버튼이 눌렸을 때 performed 상태로 간주되며, 버튼이 떼어질 때 canceled 상태로 간주된다.

    ex) 점프, 발사 등 단일성 이벤트

* _**Value**_
    
    지속적으로 값을 변화하고자 할때 쓰는 타입

    값은 입력의 변화를 지속적으로 추적하며, 입력 값을 읽어서 사용한다.
    
    예를 들어 스틱의 움직임에 따라 -1에서 1 사이의 값을 반환한다.

    ex) 이동 조작, 카메라의 회전 및 움직임 등 연속적인 입력

* _**PassThrough**_

    장치 입력이 있는 동안에는 performed 가 호출된다.

    장치가 전환되면 전환하기 전에 장치가 비활성화(Disabled) 되고 canceled 콜백이 호출된다.

<br>

## Interaction
---

Interaction 과 Processor 적용은 Started 이후에 처리된다.

* _**Press**_
    * _**Press Only**_

        누른 순간에만 performed 를 호출한다.

    * _**Release Only**_

        놓은 순간에만 performed 를 호출한다.

    * _**Press And Release**_

        누른 순간과 놓은 순간 모두에서 performed 를 호출한다.

* _**Hold**_

    일정시간 눌렀을 때 performed 가 호출된다.

* _**Tap**_

    일정시간 안에 눌렀다 뗐을 때 performed 가 호출된다.

* _**Slow Tap**_

    일정시간 눌렀을 때 performed 가 호출된다.
    
    * _**Hold 와 Slow Tap 의 차이점**_

        Hold 는 일정시간이 지나면 자동으로 performed 가 호출되는데,
        
        Slow Tap 은 일정시간이 지나고 누르고 있는 **키를 놓을 때** performed 가 호출된다.

* _**Multi Tap**_ 

    특정 횟수 만큼 탭을 해야 performed 가 호출된다.

    * _**Tap Count**_

        필요한 탭의 횟수

    * _**Max Tap Spacing**_

        탭들 사이의 간격 시간

    * _**Max Tap Duration**_

        이 시간 안에 키를 눌렀다 놓아야 탭으로 인정

<br>

## Processors
---

 * 입력한 값을 편집해주는 기능

    * _**Scale**_

        입력한 값의 크기를 변경

    * _**invert**_

        값을 반전 시킴.

<br>

## 입력 처리
---

### 다이렉트 방식

* 헤더에 `using UnityEngine.InputSystem` 을 추가해야 한다.

* Keyboard, Mouse, Gamepad 등 직접 입력 장치를 사용한다.

* 특정 입력 장치가 연결되지 않은 경우 `Null` 체크가 필요하다.

    * 장점 : 키보드, 마우스, 게임패드와 같은 입력 장치에 직접 접근이 가능하다.

    * 단점 : 여러 입력 장치에 대한 코드 중복 발생이 될 수 있다.

```csharp
using UnityEngine.InputSystem

Keyboard keyboard;
Mouse mouse;
Gamepad gamepad;

// Null 처리
if(gamepad == null) { return; }

keyboard = Keyboard.current;

keyboard.aKey.wasPressedThisFrame // 누르는 시점을 bool 로 반환
keyboard.aKey.wasReleasedThisFrame  // 놓는 시점을 bool 로 반환

if (keyboard.aKey.isPressed) // 누르고있는동안 계속 진행
{
    // 'A' 키를 눌렀을 때 실행되는 코드
}
```

<br>

### 임베디드 방식

* 스크립트에서 `public InputAction moveAction` 과 같이 정의한다.

* Unity 에디터에서 컴포넌트를 통해 키 바인딩을 설정할 수 있다.

* `OnEnable` 과 `OnDisable` 에서 설정해야 한다.

    * 장점 : Inspecter 창에서 입력 설정을 쉽게 변경 할 수 있다.

    * 단점 : 성능 오버헤드 때문에 `OnEnable` 및 `OnDisable` 에서 설정 및 해제 작업이 필요하다.


```csharp
public InputAction moveAction;

private void OnEnable()
{
    moveAction.Enable();
    moveAction.performed += OnMove; // OnMove 메서드 구독
}

private void OnDisable()
{
    moveAction.Disable();
    moveAction.performed -= OnMove; // OnMove 메서드 구독해제
}

private void OnMove(InputAction.CallbackContext context)
{
    Vector2 movement = context.ReadValue<Vector2>();
    // 이동 처리 코드
}
```

<br>

### 액션에셋 방식

* `InputActionAsset` 파일을 생성하고 이를 스크립트에서 사용한다.

* Inspecter 창에서 `ActionAsset` 에 부착하여 사용한다.

* 다양한 입력 액션과 맵을 정의하여 사용한다.

    * 장점 : `InputActionAsset` 파일을 통해 여러 스크립트에서 동일한 입력 설정이 가능하다.

    * 단점 : `InputActionAsset` 과 `InputActionMap` 을 이해하고 사용해야 한다.

```csharp
public InputActionAsset inputAsset;
private InputActionMap basic;
private InputAction move;
private InputAction jump;

private void OnEnable()
{
    basic = inputAsset.FindActionMap("Basic");
    move = basic.FindAction("Move");
    jump = basic.FindAction("Jump");

    move.performed += PlayerMove;
    jump.started += Jump;

    basic.Enable();
}

private void PlayerMove(InputAction.CallbackContext context)
{
    Vector2 movement = context.ReadValue<Vector2>();
    // 이동 처리 코드
}

private void Jump(InputAction.CallbackContext context)
{
    // 점프 처리 코드
}
```

<br>

### C# 제너레이트 방식

* `InputActionAsset` 파일의 Inspecter 창에서 `Generate C# Class` 옵션을 사용한다.

* 생성된 클래스를 스크립트에서 사용한다.

    * 장점 : 입력 설정 파일에서 C# 클래스를 자동으로 생성하여 사용할 수 있다.

    * 단점 : 자동 생성된 코드에 의존하게 되므로 변경 시 유연성이 떨어질 수 있다.

```csharp
public class PlayerInput : MonoBehaviour {

    // InputActionAsset 의 파일명이 PlayerInputActions 이라면
    // 아래와 같이 클래스로 선언 할 수 있다.
    private PlayerInputActions input;

    private void OnEnable()
    {
        input = new PlayerInputActions();
        input.Basic.Move.performed += PlayerMove;
        input.Basic.Jump.started += Jump;
        input.Enable();
    }

    private void OnDisable()
    {
        input.Disable();
    }

    private void PlayerMove(InputAction.CallbackContext context)
    {
        Vector2 movement = context.ReadValue<Vector2>();
        // 이동 처리 코드
    }

    private void Jump(InputAction.CallbackContext context)
    {
        // 점프 처리 코드
    }
}
```

<br>

## 이벤트 처리 방식
---

### Send Message

* 입력이 발생하면 메시지를 오브젝트에 보낸다.

* `InputValue` 타입의 매개변수를 갖는 함수를 호출한다.

* 메시지를 받는 함수의 이름이 고정되어 있다.

```csharp
void OnMove(InputValue value)
{
    Vector2 movement = value.Get<Vector2>();    // Get 으로 값을 받아옴.
    // 이동 처리 코드
}
```

<br>

### Broadcast Message

* `Send Message` 와 동일하지만, 해당 오브젝트 뿐만이 아니라 자식 오브젝트에게도 호출할 수 있다.

<br>

### Invoke Unity Events

* `UnityEvent` 를 사용하여 이벤트를 처리한다.

* `InputAction.CallbackContext` 구조체 타입의 매개변수를 사용한다.

* 입력 액션 타입에 따라 이벤트가 발생한다.

    * started : 입력이 막 시작된 상태

    * performed : 입력이 인정되었다는 상태

    * canceled: 입력이 취소되었을때의 상태

* 이벤트를 Inspecter 창에서 설정할 수 있다.

* `Send Message` 방식과 다르게 `ReadValue` 로 값을 불러온다.

```csharp
[SerializeField] private InputAction moveAction;

private void OnEnable()
{
    moveAction.performed += OnMovePerformed;
    moveAction.Enable();
}

private void OnDisable()
{
    moveAction.performed -= OnMovePerformed;
    moveAction.Disable();
}

private void OnMovePerformed(InputAction.CallbackContext context)
{
    Vector2 movement = context.ReadValue<Vector2>();    //ReadValue 로 값을 받아옴.
    // 이동 처리 코드
}
```

<br>

### Invoke C# Events

* `Invoke Unity Events` 와 비슷하지만, 코드로 이벤트를 직접 구독하고 처리한다.

* 코드 내에서 이벤트를 설정하여 통합관리가 가능하지만 코드로 작성되다 보니 복잡해질 수 있다.

## Reference
[nekojara - Input Action의 3종류의 콜백 거동](https://nekojara.city/unity-input-system-action-callback#Interactions%E3%81%8CMulti%20Tap%E3%81%AE%E5%A0%B4%E5%90%88)