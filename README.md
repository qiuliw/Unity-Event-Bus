# Unity C# Event Bus
 
 ![EventBus](https://github.com/adammyhre/Unity-Event-Bus/assets/38876398/1b053da8-4a22-4bef-a052-6bf7f3e24b7d)

## Description
This EventBus system provides a way to create decoupled architectures in Unity projects. It allows communication between different parts of an application without requiring direct references.

## Code Structure
This EventBus system contains several C# classes residing in the Scripts\EventBus directory:

1. `EventBus.cs` - Main EventBus class that provides static functions for registering, deregistering, and triggering custom events.

2. `EventBinding.cs` - IEventBinding interface and class definition for EventBinding, which is used to bind functions to events.

3. `Events.cs` - IEvent interface and sample code, which shows how to define custom events.

4. `PredefinedAssemblyUtil.cs` - Utility class for locating assemblies and finding types within them. See [Unity Documentation](https://docs.unity3d.com/Manual/ScriptCompileOrderFolders.html).

5. `EventBusUtil.cs` - Static initialization methods and additional utilities used for EventBus.


## Example Usage

The usage generally works like:

```csharp 
public struct PlayerEvent : IEvent {
    public int health;
    public int mana;
}

EventBinding<PlayerEvent> playerEventBinding;

void OnEnable() {    
    playerEventBinding = new EventBinding<PlayerEvent>(HandlePlayerEvent);
    EventBus<PlayerEvent>.Register(playerEventBinding);

    // Can Add or Remove Actions to/from the EventBinding
}

void OnDisable() {
    EventBus<PlayerEvent>.Deregister(playerEventBinding);
}

void Start() {
    EventBus<PlayerEvent>.Raise(new PlayerEvent {
        health = healthComponent.GetHealth(),
        mana = manaComponent.GetMana()
    });    
}

void HandlePlayerEvent(PlayerEvent playerEvent) {
    Debug.Log($"Player event received! Health: {playerEvent.health}, Mana: {playerEvent.mana}");
}
```

## YouTube

[**Watch the tutorial video here**](https://youtu.be/4_DTAnigmaQ)

You can also check out my [YouTube channel](https://www.youtube.com/@git-amend?sub_confirmation=1) for more Unity content.

## Installation and Setup
Since this is a Unity-centric project, you will need to have Unity installed on your system. The EventBus codebase is entirely C# and conforms to the .NETFramework v4.7.1 standards.

To use these scripts in your project, please place these scripts in your Scripts or a related directory in the Unity editor.

## Contributions
Contributions are always welcome. You can contribute by improving the EventBus codebase, enhancing its features, or providing suggestions.

## Inspired by

This project takes inspiration from the following open source projects:
- [BasicEventBus](https://github.com/pointcache/BasicEventBus?)
- [UnityEventAggregator](https://github.com/EricFreeman/UnityEventAggregator)
- [EventBus](https://github.com/SaldayOpen/EventBus)
- [GenericEventBus](https://github.com/PeturDarri/GenericEventBus/tree/main)


一般不是一个类型，一个类型信号容器。信号容器可以绑定多个回调吗。他这个有什么不同？

> **就是可以分组，更好更简便地按照对象或组件一次性关闭，并且可以按照组标签追溯监听者，实现优先级等细化管理。**

---

👉 **EventBinding = 监听回调的“分组容器”，通常以“一个组件 / 一个对象”为单位**


### 传统方式

```text
事件类型
 ├── 回调 A（来自组件 1）
 ├── 回调 B（来自组件 1）
 ├── 回调 C（来自组件 2）
 └── 回调 D（来自组件 3）
```

关闭组件 1 时：

```text
❌ 必须记得一个个 -= A 和 B
```

---

### EventBinding 方式

```text
事件类型
 ├── EventBinding（组件 1）
 │     ├── A
 │     └── B
 ├── EventBinding（组件 2）
 │     └── C
 └── EventBinding（组件 3）
       └── D
```

关闭组件 1 时：

```csharp
EventBus<T>.Deregister(binding1);
```

✔ 一行代码
✔ 不漏
✔ 不重复
✔ 不出事故

---

## 为什么这在 Unity 里特别舒服

Unity 的本质是：

```text
GameObject
 └── MonoBehaviour
        ├── OnEnable
        ├── OnDisable
        └── OnDestroy
```

而 `EventBinding` **刚好和组件生命周期 1:1 对齐**：

```csharp
void OnEnable()  => Register(binding);
void OnDisable() => Deregister(binding);
```

这就是为什么它看起来“多一层”，但用起来反而更简单。

---

## 再强调一个容易忽略的点

这种设计不仅是“分组”，还是：

* **原子性注销**（不会漏）
* **可追踪的监听者**
* **天然支持暂停 / 屏蔽 / 优先级**


---

> **EventBinding 不是为“多回调”而生的，而是为“按对象成组管理回调”而生的。**

