---
title: 1. Monster Spawn
description: Game Development
author: Minssuy99
date: 2025-09-09 11:00:00 +0900
categories: [Lecture, Idle Game]
tags: [C#, Unity]
pin: false
math: true
permalink : /posts/Monster-Spawn
published: true
# mermaid: false
image:
  path: 'thumbnail.png'
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAFCAYAAAB8ZH1oAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsAAAA7AAWrWiQkAAAGHaVRYdFhNTDpjb20uYWRvYmUueG1wAAAAAAA8P3hwYWNrZXQgYmVnaW49J++7vycgaWQ9J1c1TTBNcENlaGlIenJlU3pOVGN6a2M5ZCc/Pg0KPHg6eG1wbWV0YSB4bWxuczp4PSJhZG9iZTpuczptZXRhLyI+PHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj48cmRmOkRlc2NyaXB0aW9uIHJkZjphYm91dD0idXVpZDpmYWY1YmRkNS1iYTNkLTExZGEtYWQzMS1kMzNkNzUxODJmMWIiIHhtbG5zOnRpZmY9Imh0dHA6Ly9ucy5hZG9iZS5jb20vdGlmZi8xLjAvIj48dGlmZjpPcmllbnRhdGlvbj4xPC90aWZmOk9yaWVudGF0aW9uPjwvcmRmOkRlc2NyaXB0aW9uPjwvcmRmOlJERj48L3g6eG1wbWV0YT4NCjw/eHBhY2tldCBlbmQ9J3cnPz4slJgLAAAAuklEQVQYVwXBMVLCQBSA4f9tNmZ3BAmCEXREL8DYyNBxBK28KS2FjYUHsHPGhgodZ4U40U3e+n1y//CYJtMZRZGjGqnThNwkju0XIQR+6gaTGYy1R1ibEWNL18FFlXPVbvGxxfdLytOS8fgMc30zo9evuKymbF+eeN6sOQxGfOx2FLHGe4dqh3FZzlAC5ymwWC1RsXzvD4jrEZMiSVA1mM+w5/XtncY5mqJifnuHJPCjAcme8PvXIqL8A2tTQzUc0zi5AAAAAElFTkSuQmCC
  # alt:
media_subpath: '/assets/img/posts/Lecture/A. Idle Game'
---
<!---------------------------------------Header-------------------------------------->

## _**목표**_
---

맵에 플레이어블 캐릭터 주위로 몬스터를 주기적으로 스폰을 할 수 있도록 로직을 작성한다.

<br>

## _**구현**_
---

`Plane` 을 생성하고, 플레이어블 캐릭터는 중앙 `(0, 0, 0)` 에 배치했다.

![img](2025-09-09-Camera.png){: .shadow .rounded-10 .w-75 w="1200"}
_Camera_

프로젝트는 탑다운 시점으로 진행할 것이므로 카메라의 `Projection` 을 `Orthographic` 으로 설정한다.

`Orthographic` 이란 정사 투영이라는 의미로, 3차원 물체를 2차원 평면에 표현하는 투영 방식 중 하나이다.

이 투영 방식을 사용하면 원근법이 적용되지않고 `z` 축 위치와 상관없이 물체의 크기가 일정하게 보인다.

<br>


![img](2025-09-09-Animation.png){: .shadow .rounded-10 .w-75 w="1200"}
_Animation_

플레이어를 공격할 몬스터의 애니메이션을 추가하고 애니메이션을 통제할 두개의 `bool` 타입 파라미터를 생성한다.

<br>

### _**Monster.cs**_

몬스터가 중앙좌표를 향해 바라보며 다가오도록 하고, 동시에 애니메이션 로직도 구현한다.

```csharp
public class Monster : MonoBehaviour
{
    public float m_Speed;
    Animator animator;

    private void Start()
    {
        animator = GetComponent<Animator>();
    }

    private void Update()
    {
        transform.LookAt(Vector3.zero);

        float targetDistance = Vector3.Distance(transform.position, Vector3.zero);

        if (targetDistance <= 0.5f)
        {
            AnimatorChange("isIDLE");
        }
        else
        {
            transform.position = Vector3.MoveTowards(transform.position, Vector3.zero, Time.deltaTime * m_Speed);

            AnimatorChange("isMOVE");
        }
    }

    private void AnimatorChange(string temp)
    {
        animator.SetBool("isIDLE", false);
        animator.SetBool("isMOVE", false);

        animator.SetBool(temp, true);
    }
}
```
{: file="Monster.cs"}

`Vector3.Distance` 함수는 두 벡터 사이의 유클리드 직선거리를 계산해주는 함수이다.

이 함수는 벡터 그 자체를 반환하는 게 아니라, 두 벡터 사이의 길이<sub>스칼라 거리</sub> 를 계산해서 `float` 로 반환한다.

$$
\begin{equation}
  Distance(\mathbf{a}, \mathbf{b}) = \sqrt{(a_x - b_x)^2 + (a_y - b_y)^2 + (a_z - b_z)^2}
  \label{eq:euclidean_distance}
\end{equation}
$$

<br>

### _**Spawner.cs**_

몬스터를 주기적으로 생성하기 위한 스폰 로직을 작성한다.

```csharp
public class Spawner : MonoBehaviour
{
    public GameObject monster_Prefab;
    
    public int m_Count; // 몬스터의 수

    public float m_SpawnTime; // 생성주기

    private void Start()
    {
        StartCoroutine(SpawnCourtine());
    }
    
    IEnumerator SpawnCourtine()
    {
        Vector3 pos;

        for (int i = 0; i < m_Count; i++)
        {
             // 생성되는 반원의 위치값도 더욱 넓어짐
            pos = Vector3.zero + Random.insideUnitSphere * 5.0f;
            pos.y = 0.0f;

             // 중앙점을 기준으로 생성되는 반경을 좀 더 넓거나 좁게 만들 수 있음.
            while(Vector3.Distance(pos, Vector3.zero) <= 3.0f)
            {
                pos = Vector3.zero + Random.insideUnitSphere * 5.0f;
                pos.y = 0.0f;
            }

            var go = Instantiate(monster_Prefab, pos, Quaternion.identity);
        }
        yield return new WaitForSeconds(m_SpawnTime);

        StartCoroutine(SpawnCourtine());
    }
}

```
{: file="Spawner.cs"}

`Random.insideUnitSphere` 는 반지름이 1인 구(Sphere) 내부에서 임의의 점 `Vector3` 을 반환하는 프로퍼티이다.

위와 같이 몬스터, 혹은 아이템 등을 특정 위치 중심으로 구 범위 안에 생성할 때 활용할 수 있다.

<br>

## _**문제**_
---

몬스터들이 생성은 정상적으로 이루어지나, 갑자기 생성되는 부분이 어색하게 보인다.

자연스럽게 생성이 되도록 `Monster.cs` 스크립트에 추가적인 로직을 작성한다.

![img](2025-09-09-before.gif){: .shadow .rounded-10 .w-75 w="1200"}
_Problem_

<br>

```csharp
bool isSpawn = false;

private void Start()
{
    animator = GetComponent<Animator>();
    StartCoroutine(Spawn_Start());
}

IEnumerator Spawn_Start()
{
    float current = 0.0f;
    float percent = 0.0f;
    float start = 0.0f;
    float end = transform.localScale.x;
    while (percent < 1)
    {
        current += Time.deltaTime;
        percent = current / 0.2f;
        float LerpPos = Mathf.Lerp(start, end, percent);
        transform.localScale = new Vector3(LerpPos, LerpPos, LerpPos);
        yield return null;
    }
    yield return new WaitForSeconds(0.3f);
    isSpawn = true;
}

private void Update()
{
    transform.LookAt(Vector3.zero);

    if (isSpawn == false)
        return;
    .
    .
}
```
{: file="Monster.cs"}

<br>

## _**Result**_
---

몬스터들이 자연스럽게 생성되고 플레이어에게 접근하는걸 확인할 수 있다.

![img](2025-09-09-after.gif){: .shadow .rounded-10 .w-75 w="1200"}
_Result_