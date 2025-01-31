---
title: Singleton
description: Game Development
author: Minssuy99
date: 2025-01-27 04:00:00 +0900
categories: [Lecture, UnityEngine]
tags: [C#, Unity]
pin: false
# math: false
# mermaid: false
image:
  path: 'thumbnail.jpg'
  lqip: data:image/jpeg;base64,iVBORw0KGgoAAAANSUhEUgAAADIAAAAaCAAAAADQq1A2AAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAACxMAAAsTAQCanBgAAADFSURBVDjLY2AYBUMB8JKqweq/0X99UjTwnf3/lOH9/0McxGtZ/f89kPz5fwaD+xmGQ/5AdmvITjm8Wub9//9/LhD3MAS+ZbiXcen/9OSK/0cOC+7WKLXAoWXRd4a1/xcy/O9nCH7CcDnrP8P/lqxrkjXd/xk6bHBo2bGSQeo/B8O+JQxq/wP+2wO1dBXcL2b4vxi3wzgazpr+17pWzM7A4LLMi6GSodrCUK+S878pHs+k/QeBGFTB+Er8gTbzf99och6iAADgXk8NioFvJgAAAABJRU5ErkJggg==
  # alt:
media_subpath: '/assets/img/posts/Lecture/UnityEngine'
---
<!---------------------------------------Header-------------------------------------->
## _**Singleton Pattern**_
---

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Managers : MonoBehaviour
{
    static Managers s_instance;
    public static Managers Instance{ get { Init(); return s_instance; }}

    private void Start()
    {
        Init();
    }

    static void Init()
    {
        if (s_instance == null)
        {
            GameObject go = GameObject.Find("@Managers");

            if (go == null)
            {
                go = new GameObject(name: "@Managers");
                go.AddComponent<Managers>();
            }
            DontDestroyOnLoad(go);
            s_instance = go.GetComponent<Managers>();
        }
    }
}
```

<br>

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Singleton<T>: MonoBehaviour where T : MonoBehaviour
{
    private static T instance;
    
    public static T Instance;
    {
        get
        {
            if (instance == null)
            {
                instance = (T)FindObjectOfType(typeof(T));

                if (instance == null)
                {
                    GameObject obj = new GameObject(typeof(T).Name, typeof(T));
                    instance = obj.GetComponent<T>();
                }
            }
            return instance;
        }
    }

    private void Awake()
    {
        if (transform.parent != null && transform.root != null)
        {
            DontDestroyOnLoad(this.transform.root.gameObject);
        }
        else
        {        
            DonDestoryOnLoad(this.gameObject);
        }    
    }
}

```


<!--------------------------------------Reference-------------------------------------->

## _**Reference**_
---

* 