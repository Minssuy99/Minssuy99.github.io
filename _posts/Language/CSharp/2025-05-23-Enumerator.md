---
title: Enumerator
description: C# Programming
author: Minssuy99
date: 2025-05-23 21:00:00 +0900
categories: [Language, C#]
tags: [Language, C#]
pin: false
published : true
# math: false
# mermaid: false
image:
  path: 'thumbnail.png'
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAFCAYAAAB8ZH1oAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsQAAA7EAZUrDhsAAAGiaVRYdFhNTDpjb20uYWRvYmUueG1wAAAAAAA8P3hwYWNrZXQgYmVnaW49Iu+7vyIgaWQ9Ilc1TTBNcENlaGlIenJlU3pOVGN6a2M5ZCI/Pgo8eDp4bXBtZXRhIHhtbG5zOng9ImFkb2JlOm5zOm1ldGEvIiB4OnhtcHRrPSJBZG9iZSBYTVAgQ29yZSA5LjEtYzAwMiA3OS5hNmE2Mzk2LCAyMDI0LzAzLzEyLTA3OjQ4OjIzICAgICAgICAiPgogPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4KICA8cmRmOkRlc2NyaXB0aW9uIHJkZjphYm91dD0iIgogICAgeG1sbnM6eG1wPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvIgogICB4bXA6Q3JlYXRvclRvb2w9IkFkb2JlIEV4cHJlc3MgMS4wLjAiLz4KIDwvcmRmOlJERj4KPC94OnhtcG1ldGE+Cjw/eHBhY2tldCBlbmQ9InIiPz5rUdVqAAAAyklEQVQYVw3F3UrCUADA8f8+znIbW6AQqIHpyhRCBO+6q3epi94pwhfxBaLAC0H7YKsU8aKSM0xPzp363fyMq+u+toXHRm2oH1dpnlQYxyMuzzskH3Mm8RjHNrGDMGC/GOK5LqftBgX5yUHeRsolSfJCt3nGnvAxbu/uddRokUrJ0yym920xf11Qu7kg/UopVwMsDcbDcKe3PyvKhx6ub/E4eP5fEPVq5CrHFBqDX8wsW/H+NmW9VmwVHHVLRJ0K1k4R+hmuk1EQDn+/hkiJCDWxaAAAAABJRU5ErkJggg==
  # alt:
media_subpath: '/assets/img/posts/Language/Csharp'
---

<!------------------------------------------------------------>

## _**Enumerator**_
---

`Enumerator` 는 컬렉션의 요소들을 순차적으로 접근할 수 있게 해주는 반복자 패턴을 구현한 객체이다.

`IEnumerator`와 `IEnumerable` 인터페이스를 통해 지원하며, `Unity` 에서는 코루틴에서도 활용된다.

<br>

### _**Interface**_

`IEnumerable`은 컬렉션이 반복 가능함을 나타내는 인터페이스 이다.

```csharp
// IEnumerable 인터페이스
public interface IEnumerable
{
    IEnumerator GetEnumerator();
}
```

<br>

`IEnumerator`는 실제 반복 작업을 수행하는 인터페이스이다.

```csharp
// IEnumerator 인터페이스
public interface IEnumerator
{
    object Current { get; }  // 현재 요소
    bool MoveNext();         // 다음 요소로 이동
    void Reset();            // 처음으로 리셋
}
```

<br>

컬렉션 클래스는 `IEnumerable`, `GetEnumerator` 가 반환하는 열거자 클래스엔 `IEnumerator` 을 상속받는다.

<br>

## _**응용**_
---

### _**foreach**_

`foreach` 반복문은 내부적으로 `IEnumerator`를 사용한다.

`foreach` 를 사용하기 위해선, 해당 클래스가 내부적으로 `GetEnumerator` 메서드가 구현되어 있어야한다.

또한 해당 클래스 내부엔 `MoveNext()`, `Current`, `Reset()` 가 반드시 정의되어 있어야 한다.

```csharp
List<int> numbers = new List<int> { 1, 2, 3, 4, 5 };

foreach (int number in numbers)
{
    Debug.Log(number);
}
```

<br>

위 코드는 내부적으로 아래와 같이 동작한다.

```csharp
List<int> numbers = new List<int> { 1, 2, 3, 4, 5 };

// List 는 내부적으로 GetEnumerator 가 구현되어 있다.
IEnumerator enumerator = numbers.GetEnumerator();
while (enumerator.MoveNext())
{
    int number = (int)enumerator.Current;
    Debug.Log(number);
}
```

<br>

### _**구현**_

커스텀으로 컬렉션을 만들어 `IEnumerable` 및 `IEnumerator` 를 직접 구현한다.

```csharp
public class Enumerator_Practice : MonoBehaviour
{
    class MyList : IEnumerable // 클래스는 IEnumerable 상속
    {
        class enumerator : IEnumerator // IEnumerator 상속
        {
            private int index = -1;
            private int[] num = { 1, 2, 3, 4, 5 };
            
            public object Current { get => num[index]; } // 구현 필수

            public bool MoveNext() // 구현 필수
            {
                if (index == num.Length - 1)
                    return false;

                index++;
                return true;
            }
            public void Reset() // 구현 필수
            {
                index = -1;
            }
        }
        public IEnumerator GetEnumerator() // IEnumerable 에 의해 반드시 구현해야함.
        {
            enumerator enumerator = new enumerator();
            return enumerator; // 프로퍼티 및 메서드 반환
        }
    }
    
    void Start()
    {
        MyList list = new MyList();

        // foreach 문은 list.GetEnumerator() 호출
        // 반환된 enumerator 객체로 MoveNext(), Current 반복 호출
        foreach (int i in list)
        {
            Debug.Log(i);
        }
    }
}
```
{: file="Enumerator_Practice.cs"}

<br>

## _**yield return**_
---

`yield return` 키워드를 사용하면 더 간단하게 `IEnumerator`를 구현할 수 있다.

`yield` 문이 사용된 메서드를 컴파일러는 `IEnumerable`, `IEnumerator` 코드로 치환하여 내부적으로 구현한다.

따라서 `MoveNext`, `Current`, `Reset` 같은 코드를 직접 작성하지 않아도 된다.

`GetEnumerator()` 안에 반복적으로 `yield return` 을 작성하면, 자동으로 값을 하나씩 반환하는 열거자가 만들어진다.

```csharp
public class Enumerator_Ex : MonoBehaviour
{
    public class MyList
    {
        public IEnumerator GetEnumerator()
        {
            int[] num = { 1, 2, 3, 4, 5 };
            int index = -1;

            while (index < num.Length - 1)
            {
                index++;
                yield return num[index];
            }
        }
    }
    void Start()
    {
        MyList myList = new MyList();
        
        foreach(int i in myList)
        {
            Debug.Log(i);   
        }
    }
}
```
{: file="Enumerator_Ex.cs"}

<br>

`return` 은 메서드를 즉시 종료하고, 호출자에게 값을 반환하며 이후엔 코드실행이 불가하다는 특징이 있다.

반면 `yield return` 은 현재 값을 호출자에게 반환하고, 그 다음 실행 지점에서 대기, 다음 호출 시 이어서 실행된다.

<br>

## Reference
---

* [Microsoft Docs - IEnumerator Interface](https://docs.microsoft.com/en-us/dotnet/api/system.collections.ienumerator)
* [Microsoft Docs - IEnumerable Interface](https://docs.microsoft.com/en-us/dotnet/api/system.collections.ienumerable)