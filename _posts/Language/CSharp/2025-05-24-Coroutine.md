---
title: Enumerator
description: C# Programming
author: Minssuy99
date: 2025-05-24 21:00:00 +0900
categories: [Language, C#]
tags: [Language, C#]
pin: false
published : false
# math: false
# mermaid: false
image:
  path: 'thumbnail.png'
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAFCAYAAAB8ZH1oAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsQAAA7EAZUrDhsAAAGiaVRYdFhNTDpjb20uYWRvYmUueG1wAAAAAAA8P3hwYWNrZXQgYmVnaW49Iu+7vyIgaWQ9Ilc1TTBNcENlaGlIenJlU3pOVGN6a2M5ZCI/Pgo8eDp4bXBtZXRhIHhtbG5zOng9ImFkb2JlOm5zOm1ldGEvIiB4OnhtcHRrPSJBZG9iZSBYTVAgQ29yZSA5LjEtYzAwMiA3OS5hNmE2Mzk2LCAyMDI0LzAzLzEyLTA3OjQ4OjIzICAgICAgICAiPgogPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4KICA8cmRmOkRlc2NyaXB0aW9uIHJkZjphYm91dD0iIgogICAgeG1sbnM6eG1wPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvIgogICB4bXA6Q3JlYXRvclRvb2w9IkFkb2JlIEV4cHJlc3MgMS4wLjAiLz4KIDwvcmRmOlJERj4KPC94OnhtcG1ldGE+Cjw/eHBhY2tldCBlbmQ9InIiPz5rUdVqAAAAyklEQVQYVw3F3UrCUADA8f8+znIbW6AQqIHpyhRCBO+6q3epi94pwhfxBaLAC0H7YKsU8aKSM0xPzp363fyMq+u+toXHRm2oH1dpnlQYxyMuzzskH3Mm8RjHNrGDMGC/GOK5LqftBgX5yUHeRsolSfJCt3nGnvAxbu/uddRokUrJ0yym920xf11Qu7kg/UopVwMsDcbDcKe3PyvKhx6ub/E4eP5fEPVq5CrHFBqDX8wsW/H+NmW9VmwVHHVLRJ0K1k4R+hmuk1EQDn+/hkiJCDWxaAAAAABJRU5ErkJggg==
  # alt:
media_subpath: '/assets/img/posts/Language/Csharp'
---

<!------------------------------------------------------------>


## _**Unity 코루틴과의 활용**_
---

Unity에서 코루틴은 `IEnumerator`를 반환하는 메서드로 구현된다.

### _**기본 코루틴**_

```csharp
using System.Collections;
using UnityEngine;

public class CoroutineExample : MonoBehaviour
{
    void Start()
    {
        StartCoroutine(CountDown());
    }
    
    IEnumerator CountDown()
    {
        for (int i = 5; i >= 0; i--)
        {
            Debug.Log($"카운트다운: {i}");
            yield return new WaitForSeconds(1f); // 1초 대기
        }
        
        Debug.Log("발사!");
    }
}
```
{: file="CoroutineExample.cs"}

<br>

### _**복잡한 코루틴 활용**_

```csharp
IEnumerator FadeOut(SpriteRenderer spriteRenderer, float duration)
{
    Color startColor = spriteRenderer.color;
    Color endColor = new Color(startColor.r, startColor.g, startColor.b, 0f);
    
    float elapsed = 0f;
    
    while (elapsed < duration)
    {
        elapsed += Time.deltaTime;
        float progress = elapsed / duration;
        
        spriteRenderer.color = Color.Lerp(startColor, endColor, progress);
        
        yield return null; // 다음 프레임까지 대기
    }
    
    spriteRenderer.color = endColor;
}
```