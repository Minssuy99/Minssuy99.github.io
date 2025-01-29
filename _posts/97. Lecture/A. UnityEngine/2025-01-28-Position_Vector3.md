---
title: Position, Vector
description: Game Development
author: Minssuy99
date: 2025-01-28 00:30:00 +0900
categories: [Lecture, UnityEngine]
tags: [C#, Unity]
pin: false
math: true
# mermaid: false
image:
  path: 'thumbnail.jpg'
  lqip: data:image/jpeg;base64,iVBORw0KGgoAAAANSUhEUgAAADIAAAAaCAAAAADQq1A2AAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAACxMAAAsTAQCanBgAAADFSURBVDjLY2AYBUMB8JKqweq/0X99UjTwnf3/lOH9/0McxGtZ/f89kPz5fwaD+xmGQ/5AdmvITjm8Wub9//9/LhD3MAS+ZbiXcen/9OSK/0cOC+7WKLXAoWXRd4a1/xcy/O9nCH7CcDnrP8P/lqxrkjXd/xk6bHBo2bGSQeo/B8O+JQxq/wP+2wO1dBXcL2b4vxi3wzgazpr+17pWzM7A4LLMi6GSodrCUK+S878pHs+k/QeBGFTB+Er8gTbzf99och6iAADgXk8NioFvJgAAAABJRU5ErkJggg==
  # alt:
media_subpath: '/assets/img/Lecture/UnityEngine'
---
<!---------------------------------------Header-------------------------------------->
## _**Position**_
---

### _**방향**_

유니티에서 오브젝트의 위치를 움직이고 싶을 땐 아래와 같이 작성할 수 있다.

```csharp
void Update()
{
    if (Input.GetKey(KeyCode.W))
        transform.position += new Vector3(0.0f, 0.0f, 1.0f) * Time.deltaTime * _speed;

    if (Input.GetKey(KeyCode.S))
        transform.position -= new Vector3(0.0f, 0.0f, 1.0f) * Time.deltaTime * _speed;

    if (Input.GetKey(KeyCode.A))
        transform.position -= new Vector3(1.0f, 0.0f, 0.0f) * Time.deltaTime * _speed;

    if (Input.GetKey(KeyCode.D))
        transform.position += new Vector3(1.0f, 0.0f, 0.0f) * Time.deltaTime * _speed;
}
```
{: file='PlayerController.cs'}

위 코드를 통해 각 방향별로 `1.0f` 만큼 _`transform.position`_ 에 더하거나 빼서 오브젝트를 움직일 수 있다.

<br>

유니티에서는 이런 방향벡터에 대한 값들을 미리 프로퍼티로 정의해놓았다.

유니티 API 에서 제공하는 _`Vector3.cs`_ 스크립트를 보면 이렇게 정의되어 있다.

`Vector3` 는 구조체로 정의되어 있다.

👉 [Vector3.cs 링크 바로가기](https://github.com/Unity-Technologies/UnityCsReference/blob/master/Runtime/Export/Math/Vector3.cs)

```csharp
public struct Vector3 : IEquatable<Vector3>, IFormattable
{
    private static readonly Vector3 forwardVector = new Vector3(0.0f, 0.0f, 1f);

    public static Vector3 forward
    {
        [MethodImpl(MethodImplOptions.AggressiveInlining)] get => Vector3.forwardVector;
    }
}
```
{: file='Vector3.cs'}

_`forward`_ 뿐만 아니라, _`back`_, _`left`_, _`right`_ 에 대해서도 프로퍼티가 정의되어 있다.

<br>

그래서 기존의 _`new Vector3(0.0f, 0.0f, 1.0f)`_ 을 사용하지 않고, 아래와 같이 간단히 바꿀 수 있다.

```csharp
void Update()
{
    if (Input.GetKey(KeyCode.W))
        transform.position += Vector3.forward * Time.deltaTime * _speed;

    if (Input.GetKey(KeyCode.S))
        transform.position += Vector3.back * Time.deltaTime * _speed;

    if (Input.GetKey(KeyCode.A))
        transform.position += Vector3.left * Time.deltaTime * _speed;

    if (Input.GetKey(KeyCode.D))
        transform.position += Vector3.right * Time.deltaTime * _speed;
}
```
{: file='PlayerController.cs'}

<br>

<!---------------------------------------------------------------------------------->
### _**좌표**_

위의 코드를 실행해보면 아래와 같은 결과물을 얻을 수 있다.

![image](move_using_vector.gif)

오른쪽의 유니티짱은 `y` 축을 기준으로 45° 각도로 회전되어 있음에도 바라보고 있는 방향으로 나아가지 않는다.

그 이유는 _`Vector3.forward`_ 는 월드 좌표계에서의 **앞 방향** 을 나타내는 **단위벡터**이기 때문이다.

이 벡터는 `(0, 0, 1)` 로 정의되어 있어서, **월드 좌표에서 Z축 양의 방향**을 가리킨다.

<br>

캐릭터가 45° 회전한 상태에서 _`Vector3.forward`_ 를 사용하면, 그 방향은 여전히 월드 좌표계에서의 앞 방향`(즉, Vector3(0, 0, 1))`이기 때문에,

캐릭터가 회전했더라도 **회전된 캐릭터의 로컬 좌표를 무시한 채** 월드 좌표 기준으로 이동하게 된다.

이를 해결하기 위해선 여러가지 방법이 있다.

<br>

#### _**1. transform.forward , right**_

이 프로퍼티들은 _`Vector3.cs`_ 가 아닌 _`Transform.cs`_ 에 정의되어 있다.

특이하게도 _`transform`_ 의 방향벡터에는 `up`, `forward`, `right` 만 정의되어 있다.

👉 [Transform.cs 링크 바로가기](https://github.com/Unity-Technologies/UnityCsReference/blob/master/Runtime/Transform/ScriptBindings/Transform.bindings.cs)

```csharp
public class Transform : Component, IEnumerable
{
    public Vector3 forward
    {
    get => this.rotation * Vector3.forward;
    set => this.rotation = Quaternion.LookRotation(value);
    }
}
```
{: file='Transform.cs'}

_`transform.forward`_ 는 현재 캐릭터가 바라보는 앞 방향 <sub>로컬 Z축</sub>  을 **월드 좌표계로 변환한 벡터**이다.

즉, 오브젝트가 회전했을 때, **회전된 방향을 반영**한다.

* _`Vector3.forward`_ : 월드 좌표계에서 고정된 방향을 나타내는 벡터
* _`transform.forward`_ : 게임 오브젝트의 로컬 좌표계에서의 앞 방향을 나타내는 벡터

<br>

이를 이용하여 아래와 같이 코드를 수정할 수 있다.

```csharp
void Update()
{
    if (Input.GetKey(KeyCode.W))
        transform.position += transform.forward * Time.deltaTime * _speed;

    if (Input.GetKey(KeyCode.S))
        transform.position -= transform.forward * Time.deltaTime * _speed; // back

    if (Input.GetKey(KeyCode.A))
        transform.position -= transform.right * Time.deltaTime * _speed; // left

    if (Input.GetKey(KeyCode.D))
        transform.position += transform.right * Time.deltaTime * _speed;
}
```
{: file='PlayerController.cs'}

<br>

#### _**2. TransformDirection**_

_`TransformDirection`_ 은 방향에 대해서만 로컬 좌표에서 월드좌표로 변환하는 메서드이다.

즉, 로컬에서 지정한 벡터가 월드 좌표계에서 어떤 방향에 해당하는지 계산해준다.

예로들어, `Vector3.forward` 는 원래 월드좌표 기준으로 `(0, 0, 1)` 방향을 가진다.

만약 캐릭터가 45° 각도로 회전되어 있을 때 월드 좌표로 변환하면, `(0.7, 0.0, 0.7)` 와 같은 대각선 방향 벡터가 나온다.

<br>

이를 이용하여 아래와 같이 코드를 수정할 수 있다.

```csharp
void Update()
{ 
    if (Input.GetKey(KeyCode.W))
        transform.position += transform.TransformDirection
                        (Vector3.forward * (Time.deltaTime * _speed));
    
    if (Input.GetKey(KeyCode.S))
        transform.position += transform.TransformDirection
                        (Vector3.back * (Time.deltaTime * _speed));
    
    if (Input.GetKey(KeyCode.A))
        transform.position += transform.TransformDirection
                        (Vector3.left * (Time.deltaTime * _speed));
    
    if (Input.GetKey(KeyCode.D))
        transform.position += transform.TransformDirection
                        (Vector3.right * (Time.deltaTime * _speed));
    
}
```
{: file='PlayerController.cs'}

<br>

#### _**3. Translate**_

`Translate` 는 오브젝트의 위치를 이동시키는 메서드이다.

월드 좌표 또는 로컬 좌표에서 상대적인 이동을 지정할 수 있다.

```csharp
public void Translate(Vector3 translation, Transform relativeTo)
```
{: file='Transform.cs'}

`Translate` 의 첫번째 파라미터엔 벡터가 들어가고, 두번째는 좌표계를 받는다.

좌표계에 넣을 수 있는 인자는 두개가 있다.

* _`Space.Self (Default)`_
    * 오브젝트의 로컬 좌표계를 기준으로 적용한다.
    * 쉽게 말해서, 오브젝트가 바라보고 있는 방향으로 이동한다.
* _`Space.World`_
    * 월드좌표 축 방향으로 이동하게 된다.
    * 쉽게 말해서, 위에 첨부한 gif 사진과 똑같이 비정상적으로 움직이게 된다.

<br>

이를 이용하여 아래와 같이 코드를 수정할 수 있다.

```csharp
void Update()
{
    if (Input.GetKey(KeyCode.W))
    {
        transform.Translate(Vector3.forward * (Time.deltaTime * _speed));
    }
    if (Input.GetKTey(KeyCode.S))
    {
        transform.Translate(Vector3.back * (Time.deltaTime * _speed));
    }
    if (Input.GetKey(KeyCode.A))
    {
        transform.Translate(Vector3.left * (Time.deltaTime * _speed));
    }
    if (Input.GetKey(KeyCode.D))
    {
        transform.Translate(Vector3.right * (Time.deltaTime * _speed));
    }
}
```
{: file='PlayerController.cs'}

<br>

<!---------------------------------------------------------------------------->

## _**Vector**_
---

### _**성분 &nbsp;<sub>Component</sub>**_

_`Vector3.cs`_ 스크립트를 참고하여 유니티의 벡터를 간단하게 구현해본다.

```csharp
struct MyVector
{
    public float x;
    public float y;
    public float z;

    public MyVector(float x, float y, float z)
    {
        this.x = x;
        this.y = y;
        this.z = z;
    }
}
```
{: file='MyVector.cs'}

<br>

### _**크기 &nbsp;<sub>Magnitude</sub>**_

여기서 벡터의 크기를 나타내는 값인 _`magnitude`_ 를 구현해본다.

_`magnitude`_ 의 공식은 피타고라스를 이용하면 간단히 구할 수 있다.

<br>

$$
\begin{equation}
  \text{magnitude} = |\mathbf{v}| = \sqrt{x^2 + y^2 + z^2}
  \label{eq:magnitude}
\end{equation}
$$

<br>

_`magnitude`_ 는 `0` 혹은 양수로 표현된다.

_`Vector3.cs`_ 에서도 이 공식을 바탕으로 똑같이 메서드가 정의되어 있다.

```csharp
public static float Magnitude(Vector3 vector)
{
    return (float) Math.Sqrt((double) vector.x * (double) vector.x
                           + (double) vector.y * (double) vector.y
                           + (double) vector.z * (double) vector.z);
}
```
{: file='Vector3.cs'}

<br>

_`magnitude`_ 메서드를 모방하여 간단히 만들어보면 이렇게 만들 수 있다.

```csharp
struct MyVector
{
    public float x;
    public float y;
    public float z;

    public MyVector(float x, float y, float z)
    {
        this.x = x;
        this.y = y;
        this.z = z;
    }

    public float magnitude
    {
        get
        {
            return Mathf.Sqrt(x * x + y * y + z * z);
        }
    }
}
```
{: file='MyVector.cs'}

<br>

### _**정규화 &nbsp;<sub>Normalization</sub>**_

정규화란, 크기가 `1` 인 벡터를 뜻하는 단위벡터를 만드는 과정이다.

단위벡터<sub>Unit Vector</sub> 란, 벡터의 크기를 `1` 로 만든 벡터를 의미한다.

이는 방향을 유지하면서 크기(길이) 를 `1` 로 정규화 하는 과정이다.

<br>

정규화를 하는 과정은 간단한데, 각 성분들을 _`magnitude`_ 로 나눠주면 된다.

<br>

$$
\begin{equation}
  \mathbf{\hat{v}} = \frac{\mathbf{v}}{|\mathbf{v}|}
  \label{eq:normalize_formula}
\end{equation}
$$

<br>

수식으로 표현하면 이해가 힘드니, 예시를 통해 이해한다.

예로들어 벡터 `v = (3, 4, 0)` 를 정규화 해본다.

<br>

#### _**1. 벡터의 크기 구하기**_

<br>

$$
\begin{equation}
  |\mathbf{v}| = \sqrt{3^2 + 4^2 + 0^2} = \sqrt{9 + 16} = \sqrt{25} = 5
  \label{eq:magnitude_example}
\end{equation}
$$

<br>

벡터의 크기<sub><i>magnitude</i></sub> 가 `5` 라는 것을 알아낸다.

<br>

#### _**2. 벡터를 크기로 나누기**_

정규화를 하려면, 벡터의 각 성분을 크기로 나눈다.

<br>

$$
\begin{equation}
  \mathbf{\hat{v}} = \left( \frac{3}{5}, \frac{4}{5}, \frac{0}{5} \right) = (0.6, 0.8, 0.0)
  \label{eq:normalize_example}
\end{equation}
$$

<br>

#### _**3. 단위 벡터의 크기 확인**_

정규화된 벡터의 크기가 `1` 로 조정이 되었는지 다시 확인해본다.

<br>

$$
\begin{equation}
  \mathbf{\hat{v}} = \sqrt{0.6^2 + 0.8^2 + 0.0^2} = \sqrt{0.36 + 0.64 + 0.0} = \sqrt{1.0} = 1.0
  \label{eq:unit_vector_magnitude_complete}
\end{equation}
$$

<br>

이렇게 하여 `(0.6, 0.8, 0.0)` 방향을 가진 크기가 `1` 인 벡터를 만들 수 있다.

<br>

#### _**Normalize ( ) &nbsp; VS &nbsp; normalized**_

_`Vector3.cs`_ 스크립트엔 두가지 종류가 있다.

바로 _`Normalize()`_ 라는 메서드와 _`normalized`_ 라는 프로퍼티이다.

```csharp
public void Normalize()
{
    float num = Vector3.Magnitude(this);
    if ((double) num > 9.999999747378752E-06)
        this = this / num;
    else
        this = Vector3.zero;
}

public Vector3 normalized
{
    [MethodImpl(MethodImplOptions.AggressiveInlining)] get => Vector3.Normalize(this);
}
```
{: file='Vector3.cs'}

이 둘의 차이점은 아주 간단하다.

<br>

#### _**Vector3.normalized 프로퍼티**_

```csharp
Vector3 v = new Vector3(3, 4, 0);
Vector3 unitVector = v.normalized;

Debug.Log(unitVector); // (0.6, 0.8, 0)
Debug.Log(v); // (3, 4, 0) → 원본 값은 그대로 유지됨
```
원본 벡터 `v` 는 변화하지 않고, 새로운 정규화된 벡터를 반환한다.

<br>

읽기전용 프로퍼티 이기 때문에, `setter` 로 사용할 수 없다.

```csharp
/* The property 'UnityEngine.Vector3.normalized' does not have a setter.*/
Vector3 v = new Vector3(3, 4, 0);
v.normalized = new Vector3(3, 4, 0); // Error
```

<br>

#### _**Vector3.Normalize() 메서드**_

```csharp
Vector3 v = new Vector3(3, 4, 0);
v.Normalize();

Debug.Log(v); // (0.6, 0.8, 0) → 원본 값이 바뀜
```

_`Normalize()`_ 는 원본 벡터 자체를 정규화 하는 것, 즉 자기 자신을 바꾼다.

<br>

위의 내용들을 바탕으로 _`normalize`_ 를 모방하여 간단히 만들어본다.

```csharp
struct MyVector
{
    public float x;
    public float y;
    public float z;

    public MyVector(float x, float y, float z)
    {
        this.x = x;
        this.y = y;
        this.z = z;
    }

    public float magnitude
    {
        get
        {
            return Mathf.Sqrt(x * x + y * y + z * z);
        }
    }

    public MyVector normalize
    {
        get
        {
            return new MyVector(x / magnitude, y / magnitude, z / magnitude);
        }
    }
}
```
{: file='MyVector.cs'}

<br>

벡터에 또 다른 벡터 혹은 다른 변수를 사칙연산 하기 위해서 간단하게 연산자 오버로딩을 추가한다.

```csharp
struct MyVector
{
    .
    .
    .
    public static MyVector operator +(MyVector a, MyVector b)
    {
        return new MyVector(a.x + b.x, a.y + b.y, a.z + b.z);
    }
    
    public static MyVector operator -(MyVector a, MyVector b)
    {
        return new MyVector(a.x - b.x, a.y - b.y, a.z - b.z);
    }

    public static MyVector operator *(MyVector a, float b)
    {
        return new MyVector(a.x * b, a.y * b, a.z * b);
    }

    public static MyVector operator /(MyVector a, float b)
    {
        return new MyVector(a.x / b, a.y / b, a.z / b);
    }
}
```
{: file='MyVector.cs'}

<br>

#### _**Complete Code**_

```csharp
struct MyVector
{
    public float x;
    public float y;
    public float z;

    public MyVector(float x, float y, float z)
    {
        this.x = x;
        this.y = y;
        this.z = z;
    }
    
    public float magnitude
    {
        get
        {
            return Mathf.Sqrt(x * x + y * y + z * z);
        }
    }
    
    public MyVector normalize
    {
        get
        {
            return new MyVector(x / magnitude, y / magnitude, z / magnitude);
        }
    }

    public static MyVector operator +(MyVector a, MyVector b)
    {
        return new MyVector(a.x + b.x, a.y + b.y, a.z + b.z);
    }
    
    public static MyVector operator -(MyVector a, MyVector b)
    {
        return new MyVector(a.x - b.x, a.y - b.y, a.z - b.z);
    }

    public static MyVector operator *(MyVector a, float b)
    {
        return new MyVector(a.x * b, a.y * b, a.z * b);
    }

    public static MyVector operator /(MyVector a, float b)
    {
        return new MyVector(a.x / b, a.y / b, a.z / b);
    }
}
```

<br>

<!--------------------------------------Reference-------------------------------------->


## _**Reference**_
---

* [Unity GitHub - Vector3.cs](https://github.com/Unity-Technologies/UnityCsReference/blob/master/Runtime/Export/Math/Vector3.cs)
* [Unity GitHub - Transform.cs](https://github.com/Unity-Technologies/UnityCsReference/blob/master/Runtime/Transform/ScriptBindings/Transform.bindings.cs)