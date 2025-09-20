---
title: 2. Object Pooling
description: Game Development
author: Minssuy99
date: 2025-09-10 11:00:00 +0900
categories: [Lecture, Idle Game]
tags: [C#, Unity, Design Pattern]
pin: false
math: true
permalink : /posts/Object-Pooling
published: true
# mermaid: false
image:
  path: 'thumbnail.png'
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAFCAYAAAB8ZH1oAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsAAAA7AAWrWiQkAAAGHaVRYdFhNTDpjb20uYWRvYmUueG1wAAAAAAA8P3hwYWNrZXQgYmVnaW49J++7vycgaWQ9J1c1TTBNcENlaGlIenJlU3pOVGN6a2M5ZCc/Pg0KPHg6eG1wbWV0YSB4bWxuczp4PSJhZG9iZTpuczptZXRhLyI+PHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj48cmRmOkRlc2NyaXB0aW9uIHJkZjphYm91dD0idXVpZDpmYWY1YmRkNS1iYTNkLTExZGEtYWQzMS1kMzNkNzUxODJmMWIiIHhtbG5zOnRpZmY9Imh0dHA6Ly9ucy5hZG9iZS5jb20vdGlmZi8xLjAvIj48dGlmZjpPcmllbnRhdGlvbj4xPC90aWZmOk9yaWVudGF0aW9uPjwvcmRmOkRlc2NyaXB0aW9uPjwvcmRmOlJERj48L3g6eG1wbWV0YT4NCjw/eHBhY2tldCBlbmQ9J3cnPz4slJgLAAAAuklEQVQYVwXBMVLCQBSA4f9tNmZ3BAmCEXREL8DYyNBxBK28KS2FjYUHsHPGhgodZ4U40U3e+n1y//CYJtMZRZGjGqnThNwkju0XIQR+6gaTGYy1R1ibEWNL18FFlXPVbvGxxfdLytOS8fgMc30zo9evuKymbF+eeN6sOQxGfOx2FLHGe4dqh3FZzlAC5ymwWC1RsXzvD4jrEZMiSVA1mM+w5/XtncY5mqJifnuHJPCjAcme8PvXIqL8A2tTQzUc0zi5AAAAAElFTkSuQmCC
  # alt:
media_subpath: '/assets/img/posts/Lecture/A. Idle Game'
---

## _**문제**_
---

현재 `Spawner.cs` 에서 `Instantiate` 함수를 통해 몬스터를 주기적으로 생성한다.

```csharp
var go = Instantiate(monster_Prefab, pos, Quaternion.identity);
```
{: file="Spawner.cs"}

<br>

플레이어블 캐릭터는 주변에 생성된 몬스터를 공격하여 처치할 것이다.

이 과정에서 몬스터 오브젝트는 파괴 및 생성을 반복하게 되는데, 이때 많은 가비지가 발생할 수 있다.

이를 해결하기 위해 몬스터 오브젝트를 디자인 패턴 중 하나인 오브젝트 풀링을 사용하여 해결한다.

<br>

## _**구현**_
---

### _**Base_Mng.cs**_

먼저 각종 매니저들을 총괄할 `Base_Mng.cs` 스크립트를 구현한다.

싱글톤으로 구현하여 어디서든 쉽게 접근할 수 있도록 한다.

```csharp
public class Base_Mng : MonoBehaviour
{
    public static Base_Mng instance = null;

    private void Awake()
    {
        Initialize();
    }

    private void Initialize()
    {
        if (instance == null)
        {
            instance = this;
            DontDestroyOnLoad(this.gameObject);
        }
        else
        {
            Destroy(this.gameObject);
        }
    }
}
```
{: file="Base_Mng.cs"}

<br>

### _**Pool_Mng.cs**_

오브젝트 풀을 담당할 `Pool_Mng.cs` 를 구현한다.

인터페이스를 쓰는 이유는 다른 풀링 클래스를 쓰더라도 같은 규약으로 일관되게 관리하기 위함이다.

```csharp
public interface IPool
{
    // 풀링된 오브젝트들이 부모로 삼을 Transform
    Transform parentTransform { get; set; }

    // 풀링된 오브젝트들을 보관할 큐
    Queue<GameObject> pool { get; set; }

    // 풀에서 오브젝트를 꺼낼 때 사용할 함수
    GameObject Get(Action<GameObject> action = null);

    // 오브젝트를 풀에 반환할 때 사용할 함수
    void Return(GameObject obj, Action<GameObject> action = null);
}
```
{: file="Pool_Mng.cs"}

<br>

인터페이스 작성 후, `Object_Pool` 클래스에 상속받은 후 실제 동작을 구현한다.

```csharp
public class Object_Pool : IPool // 상속
{
    public Queue<GameObject> pool { get; set; } = new Queue<GameObject>();

    public Transform parentTransform { get; set; }

    public GameObject Get(Action<GameObject> action = null)
    {
        GameObject obj = pool.Dequeue(); // 오브젝트를 큐에서 꺼내고
        obj.SetActive(true); // 꺼낸 오브젝트 활성화

        if(action != null)
        {
            action?.Invoke(obj); // 꺼낸 오브젝트에 추가 동작 실행
        }
        return obj; // 꺼낸 오브젝트 주기
    }

    public void Return(GameObject obj, Action<GameObject> action = null)
    {
        pool.Enqueue(obj); // 오브젝트를 큐에 넣고
        obj.transform.parent = parentTransform; // 부모 재설정 (방어로직)
        obj.SetActive(false); // 넣은 오브젝트 비활성화

        if (action != null)
        {
            action?.Invoke(obj); // 넣은 오브젝트에 추가 동작 실행
        }
    }
}
```
{: file="Pool_Mng.cs"}

<br>

`IPool` 을 상속받아 만들어낸 `Object_Pool` 과 같은 클래스들을 관리하는 매니저 클래스를 만든다.

`Pool_Mng` 클래스는 풀링 시스템 전체를 관리하는 매니저로 동작하게 된다.

```csharp
public class Pool_Mng
{
    // 여러 오브젝트 풀을 딕셔너리를 통해 별도로 저장
    public Dictionary<string, IPool> m_pool_Dictionary = new Dictionary<string, IPool>();

    // 풀 오브젝트들의 최상위 부모
    // 하이어라키에서 '##Base_Mng` 라는 이름을 가진 오브젝트의 Transform 을 의미함
    Transform base_Obj = null;

    public void Initialize(Transform T)
    {
        base_Obj = T;
    }

    public IPool Pooling_OBJ(string path) // 특정 풀 가져오는 함수
    {
         // 풀을 가져오려고 했으나 없다면, 풀을 새로 생성
        if(m_pool_Dictionary.ContainsKey(path) == false)
        {
            Add_Pool(path);
        }

        // 가져올 풀에 오브젝트가 없다면 오브젝트 추가
        if(m_pool_Dictionary[path].pool.Count <= 0) Add_Queue(path);

        // 풀 반환
        return m_pool_Dictionary[path];
    }

    private GameObject Add_Pool(string path)
    {
        // 풀의 부모로 설정할 오브젝트 생성
        // ex) Monster##POOL 이라는 이름의 게임오브젝트가 하이어라키에 생성됨
        GameObject obj = new GameObject(path + "##POOL");

        // Monster##POOL 의 부모를 base_Obj(##Base_Mng) 로 설정
        obj.transform.SetParent(base_Obj);

        // 새로운 풀 생성
        Object_Pool T_Component = new Object_Pool();

        // 딕셔너리에 추가
        m_pool_Dictionary.Add(path, T_Component);

        // Monster##POOL 오브젝트를 생성한 풀의 parentTransform 변수로 참조 (아직 부모로 설정 x)
        T_Component.parentTransform = obj.transform;

        // 풀 반환
        return obj;
    }

    private void Add_Queue(string path)
    {
        // Resources 폴더에 있는 GameObject 타입의 path 라는 이름을 가진 오브젝트 생성 및 참조
        var go = Base_Mng.instance.Instatiate_Path(path);

        // 생성된 오브젝트의 부모를 Monster##POOL 오브젝트로 설정함
        go.transform.parent = m_pool_Dictionary[path].parentTransform;

        // Return 함수 실행 (오브젝트를 비활성상태로 큐에 넣는 함수)
        m_pool_Dictionary[path].Return(go);
    }
}
```
{: file="Pool_Mng.cs"}

<br>

`Base_Mng.cs` 에 `Pool_Mng` 클래스의 인스턴스를 생성한다.

그리고 `Base_Mng.cs` 가 컴포넌트로 추가된 게임 오브젝트의 `Transform` 의 정보를 넘겨준다.

```csharp
public class Base_Mng : MonoBehaviour
{
    public static Base_Mng instance = null;

    // Pool_Mng's Instance
    private static Pool_Mng s_Pool = new Pool_Mng();
    public static Pool_Mng Pool { get { return s_Pool; } }

    private void Awake()
    {
        Initialize();
    }

    private void Initialize()
    {
        if (instance == null)
        {
            instance = this;

            // Pool_Mng 에서 base_Obj(##Base_Mng) 를 최상위 부모로 설정함.
            Pool.Initialize(transform);
            DontDestroyOnLoad(this.gameObject);
        }
        else
        {
            Destroy(this.gameObject);
        }
    }
}
```
{: file="Base_Mng.cs"}

<br>

### _**Spawner.cs**_

기존에는 아래와 같은 방식으로 몬스터를 생성했다.

```csharp
IEnumerator SpawnCourtine()
{
    Vector3 pos;

    for (int i = 0; i < m_Count; i++)
    {
        pos = Vector3.zero + Random.insideUnitSphere * 5.0f;
        pos.y = 0.0f;

        while(Vector3.Distance(pos, Vector3.zero) <= 3.0f)
        {
            pos = Vector3.zero + Random.insideUnitSphere * 5.0f;
            pos.y = 0.0f;
        }

        // 몬스터 생성 로직
        var go = Instantiate(monster_Prefab, pos, Quaternion.identity);
    }
    yield return new WaitForSeconds(m_SpawnTime);

    StartCoroutine(SpawnCourtine());
}
```
{: file="Spawner.cs"}

<br>

위의 방식이 아닌, `Monster` 이라는 이름을 가진 `Pool` 을 생성하고, `Get` 함수로 오브젝트를 큐에서 꺼낸다.

람다식에 적힌 함수들은 `Get` 함수의 `Action` 델리게이트 타입의 파라미터로 넘겨지고, 순차적으로 실행한다.

즉, 풀에서 꺼낸 `Monster` 오브젝트는 `Init` 함수를 실행, 위치를 `pos` 로 설정, `Vector3.zero` 를 바라보게 된다. 

```csharp
IEnumerator SpawnCourtine()
{
    Vector3 pos;

    for (int i = 0; i < m_Count; i++)
    {
        pos = Vector3.zero + Random.insideUnitSphere * 5.0f;
        pos.y = 0.0f;

        while(Vector3.Distance(pos, Vector3.zero) <= 3.0f)
        {
            pos = Vector3.zero + Random.insideUnitSphere * 5.0f;
            pos.y = 0.0f;
        }

        // Monster 오브젝트 풀 생성, Get 함수로 오브젝트를 큐에서 꺼내고, 아래의 함수들을 실행
        var goObj = Base_Mng.Pool.Pooling_OBJ("Monster").Get((value) =>
        {
            value.GetComponent<Monster>().Init();
            value.transform.position = pos;
            value.transform.LookAt(Vector3.zero);
        });

    }
    yield return new WaitForSeconds(m_SpawnTime);

    StartCoroutine(SpawnCourtine());
}
```
{: file="Spawner.cs"}

<br>

생성된 몬스터 오브젝트들을 1초 뒤에 다시 풀에 집어넣는 로직도 작성해본다.

```csharp
    IEnumerator SpawnCourtine()
    {
        Vector3 pos;

        for (int i = 0; i < m_Count; i++)
        {
            pos = Vector3.zero + Random.insideUnitSphere * 5.0f;
            pos.y = 0.0f;

            while(Vector3.Distance(pos, Vector3.zero) <= 3.0f)
            {
                pos = Vector3.zero + Random.insideUnitSphere * 5.0f;
                pos.y = 0.0f;
            }

            var goObj = Base_Mng.Pool.Pooling_OBJ("Monster").Get((value) =>
            {
                value.GetComponent<Monster>().Init();
                value.transform.position = pos;
                value.transform.LookAt(Vector3.zero);
            });

            StartCoroutine(ReturnCoroutine(goObj));
        }
        yield return new WaitForSeconds(m_SpawnTime);

        StartCoroutine(SpawnCourtine());
    }

    // 오브젝트를 풀에 돌려주는 함수
    IEnumerator ReturnCoroutine(GameObject obj)
    {
        yield return new WaitForSeconds(1.0f);
        Base_Mng.Pool.m_pool_Dictionary["Monster"].Return(obj);
    }
```
{: file="Spawner.cs"}

<br>

### _**Monster.cs**_

몬스터 스크립트의 구조를 조금 수정한다.

기존코드는 아래와 같이, 게임을 시작하면 바로 코루틴이 실행되도록 작성되어있다.

```csharp
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

위의 코루틴을 `Init` 함수를 통해 실행할 수 있도록 수정한다.

이를 통해 몬스터가 풀에서 꺼내졌을 때 실행이 되도록 바뀌었다.

```csharp
private void Start()
{
    animator = GetComponent<Animator>();
}

public void Init()
{
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
```
{: file="Monster.cs"}

<br>

## _**Result**_
---

생성된 몬스터들은 `Monster##POOL` 오브젝트의 자식으로 되고, 또한 이는 `##Base_Mng` 의 자식이 된다.

몬스터들이 풀에서 꺼내진 후, 1초 뒤에 `SetActive(false)` 가 되며 풀에 `Return` 되는 것을 확인할 수 있다.

![img](2025-09-20-result.gif){: .shadow .rounded-10 .w-75 w="1200"}
_Result_