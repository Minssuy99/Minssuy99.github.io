---
title: Singleton Pattern
description: Design Pattern for Game Development
author: Minssuy99
date: 2025-06-07 23:00:00 +0900
categories: [Theory, Design Pattern]
tags: [Design Pattern]
pin: false
# published : false
# permalink: /posts/Input_Title_At_Here/
# math: false
# mermaid: false
image:
  path: 'thumbnail.png'
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAFCAYAAAB8ZH1oAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsQAAA7EAZUrDhsAAACxSURBVBhXLY4/C4FRGMV/9xKh9KZkJzEYLJQ/s2QyKt/CavEJ5EtY7Bab0WKhDDJgFhaJ+9zL+/ae4enU6fzOo0azpeMvYwQvnaRaKeCsYb05ENEapZQfo/1jrSXjpcgXiyxWey6PGP1Ojc/H4FzAIdLoDie+Eac4nm/Mp2Oud0HiWRLRLyI2oAbEQP/m8/mi3RvQatap5lO8398wDKf9hoiQ86BQKhPFsN2d0FqFP8IP8dg6d0cGIlEAAAAASUVORK5CYII=
  # alt:
media_subpath: '/assets/img/posts/Theory/DesignPattern'
---

<!-------------------------------------------------------------->

## _**정의**_
---

게임을 개발하다 보면, 게임 전체에서 오직 하나만 존재해야 하는 객체들이 있다.

예를 들어, 게임 매니저, 사운드 매니저, 씬 매니저 등이 이에 해당한다.

이런 상황을 위해 전역에서 단 하나의 인스턴스만 존재하도록 보장하는 패턴이 바로 싱글톤 패턴이다.

<br>

## _**Eager Initialization**_
---

번역하면 즉시 초기화 싱글톤이라고 한다.

즉시 초기화 싱글톤은 클래스가 로드될 때 바로 인스턴스를 생성하는 방식이다.

```csharp
public class GameManager
{
    // 클래스 로드 시점에 즉시 인스턴스 생성
    private static readonly GameManager instance = new GameManager();
    
    public int score = 0;
    public int level = 1;
    
    // private 생성자로 외부에서 인스턴스 생성 방지
    private GameManager() { }
    
    // 인스턴스에 접근하는 프로퍼티
    public static GameManager Instance
    {
        get { return instance; }
    }
    
    public void AddScore(int point)
    {
        score += point;
        Debug.Log($"현재 점수: {score}");
    }
}
```
{: file="Eager Initialization.cs"}

<br>

즉시 초기화의 장점은 스레드 안전성이 보장되고 구현이 간단하다는 것이다.

하지만 프로그램 시작 시점에 무조건 생성되므로, 사용하지 않는 경우에도 메모리를 차지한다는 단점이 있다.

<br>

## _**Lazy Initialization**_
---

번역하면 즉시 초기화 싱글톤이라고 한다.

게으른 초기화 싱글톤은 실제로 인스턴스가 필요한 시점에 생성하는 방식이다.

```csharp
public class SoundManager
{
    private static SoundManager instance // 초기에는 null
    
    public float masterVolume = 1.0f;
    public bool isMuted = false;
    
    private SoundManager() 
    {
        Debug.Log("SoundManager 생성됨");
    }
    public static SoundManager Instance
    {
        get
        {
            if (instance == null) // 인스턴스가 없을 때만 생성
            {
                instance = new SoundManager();
            }
            return instance;
        }
    }
    public void PlaySFX(string sfxName)
    {
        if (!isMuted)
        {
            Debug.Log($"{sfxName} 사운드 재생 (볼륨: {masterVolume})");
        }
    }
}
```
{: file="Lazy Initialization.cs"}

<br>

게으른 초기화의 장점은 필요할 때만 인스턴스를 생성하므로 메모리를 절약할 수 있다는 것이다.

하지만 멀티스레드 환경에서는 동시에 여러 인스턴스가 생성될 수 있는 문제가 있다.

<br>

## _**Double-Checked Locking**_
---

영문 그대로 더블 체크 락킹 싱글톤 이라고 한다.

멀티스레드 환경에서 안전한 게으른 초기화를 위해 락<sub>lock</sub>을 사용할 수 있다.

두 번의 `if` 조건을 통해 불필요한 `lock` 진입을 피하면서도, 인스턴스가 오직 한 번만 생성되도록 보장한다.

```csharp
public class DataManager
{
    private static DataManager instance = null;
    private static readonly object lockObject = new object(); // 락 객체
    
    public Dictionary<string, int> playerData;
    
    private DataManager()
    {
        playerData = new Dictionary<string, int>();
        Debug.Log("DataManager 생성됨");
    }
    
    public static DataManager Instance
    {
        get
        {
            if (instance == null) // 첫 번째 체크
            {
                lock (lockObject) // 락 획득
                {
                    if (instance == null) // 두 번째 체크 (Double-Check Locking)
                    {
                        instance = new DataManager();
                    }
                }
            }
            return instance;
        }
    }
    
    public void SaveData(string key, int value)
    {
        playerData[key] = value;
        Debug.Log($"데이터 저장: {key} = {value}");
    }
}
```
{: file="Double-CheckedLocking.cs"}

<br>

더 간단한 방법으로는 `Lazy<T>` 클래스를 사용하는 방식이 있다.

`Lazy<T>` 는 `.NET` 에서 제공하는 제네릭 클래스이며, 실제 사용 시까지 객체 생성을 미루는 지연 초기화를 지원한다.

내부적으로 스레드 안전성까지 자동으로 처리되므로, 직접 `lock` 이나 더블 체크 락킹을 구현할 필요가 없다.

결과적으로 간단한 코드만으로도 안전하고 효율적인 싱글톤 패턴을 만들 수 있다.

```csharp
public class DataManager
{
    private static readonly Lazy<DataManager> lazy = new Lazy<DataManager>(() => new DataManager());
    
    public static DataManager Instance => lazy.Value; // 스레드 안전 보장
    
    private DataManager()
    {
        Debug.Log("DataManager 생성됨");
    }
}
```
{: file="Lazy<T>.cs"}

<br>

## _**MonoBehaviour Eager Initialization**_
---

Unity에서 GameObject에 컴포넌트로 추가되어야 하는 매니저들은 `MonoBehaviour`를 상속받아야 한다.

```csharp
public class UIManager : MonoBehaviour
{
    private static UIManager instance;
    
    [SerializeField] private Canvas mainCanvas;
    [SerializeField] private GameObject pausePanel;
    
    void Awake()
    {
        // 인스턴스가 이미 존재하는지 확인
        if (instance != null && instance != this)
        {
            Destroy(gameObject); // 중복 인스턴스 제거
            return;
        }
        
        instance = this;
        DontDestroyOnLoad(gameObject); // 씬 전환 시에도 유지
    }
    
    public static UIManager Instance
    {
        get
        {
            if (instance == null)
            {
                Debug.LogError("UIManager 인스턴스가 씬에 존재하지 않습니다!");
            }
            return instance;
        }
    }
    
    public void ShowPausePanel()
    {
        pausePanel.SetActive(true);
    }
    
    public void HidePausePanel()
    {
        pausePanel.SetActive(false);
    }
}
```
{: file="MonoBehaviour-Eager-Initialization.cs"}

<br>

이 방식은 `Inspector` 에서 직접 설정할 수 있는 변수들을 사용할 수 있다는 장점이 있다.

하지만 씬에 미리 `GameObject` 로 배치되어 있어야 한다는 제약이 있다.

<br>

## _**MonoBehaviour Lazy Initialization**_
---

필요할 때 자동으로 `GameObject` 를 생성하는 방식의 싱글톤이다.

```csharp
public class SceneManager : MonoBehaviour
{
    private static SceneManager instance;
    
    [SerializeField] private float fadeTime = 1.0f;
    
    public static SceneManager Instance
    {
        get
        {
            if (instance == null)
            {
                // GameObject를 동적으로 생성
                GameObject go = new GameObject("SceneManager");
                instance = go.AddComponent<SceneManager>();
                DontDestroyOnLoad(go);
            }
            return instance;
        }
    }
    
    void Awake()
    {
        // 이미 인스턴스가 존재하면 중복 제거
        if (instance != null && instance != this)
        {
            Destroy(gameObject);
            return;
        }
        
        instance = this;
        DontDestroyOnLoad(gameObject);
    }
    
    public void LoadScene(string sceneName)
    {
        StartCoroutine(LoadSceneWithFade(sceneName));
    }
    
    private IEnumerator LoadSceneWithFade(string sceneName)
    {
        // 페이드 아웃
        yield return new WaitForSeconds(fadeTime);
        
        // 씬 로드
        UnityEngine.SceneManagement.SceneManager.LoadScene(sceneName);
    }
}
```
{: file="MonoBehaviour-Lazy-Initialization.cs"}

<br>

## _**Singleton Generic**_
---

여러 싱글톤 클래스에서 공통된 코드를 재사용하기 위해 제네릭을 활용할 수 있다.

### _**Generic Singleton for Regular Classes**_

```csharp
public class Singleton<T> where T : class, new()
{
    private static T instance;
    private static readonly object lockObject = new object();
    
    public static T Instance
    {
        get
        {
            if (instance == null)
            {
                lock (lockObject)
                {
                    if (instance == null)
                    {
                        instance = new T();
                    }
                }
            }
            return instance;
        }
    }
}
```

<br>

사용방법은 아래와 같다.

```csharp
public class NetworkManager : Singleton<NetworkManager>
{
    public bool isConnected = false;
    
    public void Connect()
    {
        isConnected = true;
        Debug.Log("네트워크 연결됨");
    }
}

// 사용
NetworkManager.Instance.Connect();
```

<br>

### _**MonoBehaviour-based**_

```csharp
public class MonoSingleton<T> : MonoBehaviour where T : MonoBehaviour
{
    private static T instance;
    
    public static T Instance
    {
        get
        {
            if (instance == null)
            {
                instance = FindObjectOfType<T>();
                
                if (instance == null)
                {
                    GameObject go = new GameObject(typeof(T).Name);
                    instance = go.AddComponent<T>();
                    DontDestroyOnLoad(go);
                }
            }
            return instance;
        }
    }
    
    protected virtual void Awake()
    {
        if (instance != null && instance != this)
        {
            Destroy(gameObject);
            return;
        }
        
        instance = this as T;
        DontDestroyOnLoad(gameObject);
    }
}
```

<br>

사용방법은 아래와 같다.

```csharp
public class AudioManager : MonoSingleton<AudioManager>
{
    [SerializeField] private AudioSource bgmSource;
    
    protected override void Awake()
    {
        base.Awake(); // 부모 클래스의 싱글톤 로직 실행
        
        // AudioManager만의 초기화 로직
        if (bgmSource == null)
        {
            bgmSource = gameObject.AddComponent<AudioSource>();
        }
    }
    
    public void PlayBGM(AudioClip clip)
    {
        bgmSource.clip = clip;
        bgmSource.Play();
    }
}

// 사용
AudioManager.Instance.PlayBGM(backgroundMusic);
```

<br>

## _**싱글톤 패턴의 장단점**_
---

_**장점**_

* 어디서든 `Instance`를 통해 쉽게 접근할 수 있어 코드가 간결해진다.
* 인스턴스가 하나만 존재하므로 메모리 사용량을 줄일 수 있다.
* 모든 곳에서 동일한 인스턴스를 사용하므로 데이터 일관성이 보장된다.
* 인스턴스 생성 시점을 제어할 수 있어 초기화 순서를 관리하기 용이하다.

<br>

_**단점**_

* 다른 클래스들이 싱글톤에 직접 의존하게 되어 결합도가 높아진다.
* 싱글톤은 Mock 객체로 대체하기 어려워 단위 테스트가 복잡해진다.
* 클래스 내부에서 싱글톤을 사용하면 외부에서 의존성을 파악하기 어렵다.
* 싱글톤 클래스는 상속하기 어렵고, 상속받은 클래스도 싱글톤이 되어야 한다.

<br>

## _**싱글톤 사용 시 주의사항**_
---

### _**적절한 사용 범위**_
모든 클래스를 싱글톤으로 만들지 말고, 정말 전역에서 하나만 존재해야 하는 객체에만 적용한다.

```csharp
// 적절한 사용 예시
public class GameManager : Singleton<GameManager> { }
public class InputManager : Singleton<InputManager> { }
public class SaveManager : Singleton<SaveManager> { }

// 부적절한 사용 예시 (일반 클래스로 충분)
public class Enemy : Singleton<Enemy> { } // X
public class Bullet : Singleton<Bullet> { } // X
```

<br>

### _**초기화 순서 고려**_
여러 싱글톤 간에 의존성이 있을 때는 초기화 순서를 신중히 고려해야 한다.

```csharp
public class GameManager : Singleton<GameManager>
{
    public GameManager()
    {
        // SoundManager가 먼저 초기화되어야 함
        SoundManager.Instance.Initialize();
    }
}
```

<br>

## _**대안 - 의존성 주입**_
---

싱글톤의 단점을 보완하기 위해 의존성 주입<sub>Dependency Injection</sub>을 사용할 수 있다.

`DI` 는 필요한 객체를 직접 생성하지 않고 외부에서 주입받음으로써, 결합도를 낮추고 테스트 용이성을 높인다.

<br>

### _**Constructor Injection**_

생성자에서 필요한 의존성을 받아 객체를 초기화한다.

의존성이 명확히 드러나 유지보수와 테스트가 편리하다.

하지만 생성자가 복잡해질 수 있으므로 주의가 필요하다.

```csharp
public class Player
{
    private ISoundManager soundManager;
    private IGameManager gameManager;
    
    // 생성자를 통해 의존성 주입
    public Player(ISoundManager soundManager, IGameManager gameManager)
    {
        this.soundManager = soundManager;
        this.gameManager = gameManager;
    }
    
    public void Attack()
    {
        soundManager.PlaySFX("Attack");
        gameManager.AddScore(10);
    }
}
```

<br>

### _**Service Locator 패턴**_

전역에서 서비스 객체를 관리하고 필요한 곳에서 조회해서 사용한다.

`DI` 보다 구현이 간단하지만, 의존성이 숨겨질 수 있어 남용하면 코드가 난해해질 위험이 있다.

적절히 활용하면 빠른 프로토타입 제작에 유리하다.

```csharp
public class ServiceLocator
{
    // 서비스 객체를 타입별로 저장하는 딕셔너리
    private static Dictionary<Type, object> services = new Dictionary<Type, object>();
    
    // 서비스 등록 메서드. 타입에 해당하는 서비스 객체를 저장
    public static void Register<T>(T service)
    {
        services[typeof(T)] = service;
    }
    
    // 서비스 조회 메서드. 등록된 서비스 객체를 타입에 맞게 반환
    public static T Get<T>()
    {
        return (T)services[typeof(T)];
    }
}

// 게임 초기화 시점에 서비스 객체들을 등록하는 클래스
public class GameInitializer : MonoBehaviour
{
    void Start()
    {
        ServiceLocator.Register<ISoundManager>(new SoundManager());
        ServiceLocator.Register<IGameManager>(new GameManager());
    }
}

public class Player : MonoBehaviour
{
    void Attack()
    {
        ServiceLocator.Get<ISoundManager>().PlaySFX("Attack");
        ServiceLocator.Get<IGameManager>().AddScore(10);
    }
}
```

<br>

## _**대안 - Scriptable Object 활용**_

유니티에서 제공하는 데이터 자산으로, 설정값이나 공통 데이터를 에셋으로 관리할 수 있다.

인스펙터에서 쉽게 조작 가능하며, 싱글톤 대신 공유 데이터 저장소로 활용하기 좋다.

게임 설정, 밸런스 조절 등에 특히 유용하다.

```csharp
[CreateAssetMenu(fileName = "GameSettings", menuName = "Settings/Game Settings")]
public class GameSettings : ScriptableObject
{
    [SerializeField] private int maxHealth = 100;
    [SerializeField] private float moveSpeed = 5.0f;
    
    public int MaxHealth => maxHealth;
    public float MoveSpeed => moveSpeed;
}

public class Player : MonoBehaviour
{
    [SerializeField] private GameSettings gameSettings; // Inspector에서 할당
    
    void Start()
    {
        // 싱글톤 대신 ScriptableObject 사용
        GetComponent<Health>().maxHealth = gameSettings.MaxHealth;
        GetComponent<Movement>().moveSpeed = gameSettings.MoveSpeed;
    }
}
```

<br>

## _**결론**_
---

싱글톤 패턴은 게임 개발에서 매우 유용한 패턴이지만, 남용하면 코드의 결합도가 높아지고 테스트하기 어려워진다.

적절한 상황에서만 사용하고, 가능하다면 의존성 주입과 같은 대안도 고려해보는 것이 좋다.

`MonoBehaviour` 를 상속받거나 제네릭을 활용한 싱글톤을 적절히 조합하여 사용하는 것이 효과적이다.

무엇보다 "정말 전역에서 하나만 존재해야 하는 객체인가?" 를 먼저 고민해보고 싱글톤을 적용하는 것이 중요하다.

<br>

## _**Reference**_
---

* [게이머TV's YouTube - 유니티 싱글톤 패턴(Singleton Pattern)](https://www.youtube.com/watch?v=zvOVk2e01go&ab_channel=%EA%B2%8C%EC%9D%B4%EB%A8%B8TV)
* [Jsland's Tistory - 서비스 로케이터 패턴(Service Locator Pattern)](https://jsland-dd.tistory.com/entry/C-%EC%84%9C%EB%B9%84%EC%8A%A4-%EB%A1%9C%EC%BC%80%EC%9D%B4%ED%84%B0-%ED%8C%A8%ED%84%B4-Service-Locator-Pattern)
* [stackoverflow - Constructor Injection in C#/Unity?](https://stackoverflow.com/questions/2015308/constructor-injection-in-c-unity)