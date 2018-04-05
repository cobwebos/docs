---
title: 管理 Azure Service Fabric 状态 | Microsoft Docs
description: 了解如何访问、保存和删除 Service Fabric Reliable Actors 状态。
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: c00805fc5d8515fb743256e35a75be1546483245
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="access-save-and-remove-reliable-actors-state"></a>访问、保存和删除 Reliable Actors 状态
[Reliable Actors](service-fabric-reliable-actors-introduction.md) 是可封装逻辑与状态并稳定维护状态的单线程对象。 每个执行组件实例都有其自身的[状态管理器](service-fabric-reliable-actors-state-management.md)：一种类似于字典的数据结构，能够可靠地存储密钥/值对。 状态管理器是围绕状态提供程序的包装。 无论使用哪个[持久性设置](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication)，都可以使用它来存储数据。

状态管理器密钥必须为字符串。 值是泛型的，可以是任何类型（包括自定义类型）。 存储在状态管理器中的值必须可进行数据约定序列化的，因为根据执行组件的状态持久性设置，它们可能在复制期间通过网络传输到其他节点，并且可能写入磁盘。

状态管理器公开通用字典方法来管理状态，类似于在可靠字典中找到的项目。

有关信息，请参阅[管理执行组件状态的最佳做法](service-fabric-reliable-actors-state-management.md#best-practices)。

## <a name="access-state"></a>访问状态
通过状态管理器根据键来访问状态。 状态管理器方法全都是异步的，因为当执行组件具有持久化状态时，它们可能需要磁盘 I/O。 首次访问时，状态对象将缓存在内存中。 重复访问操作将从内存中直接访问对象并以同步方式返回，而不造成磁盘 I/O 或异步上下文切换的开销。 在以下情况下，，将从缓存中删除状态对象：

* 从状态管理器中检索对象之后，执行组件方法将引发未经处理的异常。
* 执行组件在停用之后或发生故障后将重新激活。
* 状态提供程序将状态分页到磁盘。 此行为取决于状态提供程序实现。 `Persisted` 设置的默认状态提供程序具有此行为。

如果键的条目不存在，可以使用引发 `KeyNotFoundException` (C#) 或 `NoSuchElementException`(Java) 的标准 *Get* 操作来检索状态：

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().getStateAsync("MyState");
    }
}
```

如果键的条目不存在，也可以使用不引发的 *TryGet* 方法来检索状态：

```csharp
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```
```Java
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().<Integer>tryGetStateAsync("MyState").thenApply(result -> {
            if (result.hasValue()) {
                return result.getValue();
            } else {
                return 0;
            });
    }
}
```

## <a name="save-state"></a>保存状态
状态管理器检索方法将返回对本地内存中对象的引用。 只是在本地内存中修改此对象并不会永久存储该对象。 从状态管理器检索和修改对象时，必须将它重新插入状态管理器才能永久保存。

可以使用无条件的 *Set*（相当于 `dictionary["key"] = value` 语法）来插入状态：

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture setCountAsync(int value)
    {
        return this.stateManager().setStateAsync("MyState", value);
    }
}
```

可以使用 *Add* 方法添加状态。 尝试添加已存在的键时，该方法会引发 `InvalidOperationException`(C#) 或 `IllegalStateException`(Java)。

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().addOrUpdateStateAsync("MyState", value, (key, old_value) -> old_value + value);
    }
}
```

还可以使用 *TryAdd* 方法添加状态。 尝试添加已存在的键时，该方法不会引发。

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().tryAddStateAsync("MyState", value).thenApply((result)->{
            if(result)
            {
                // Added successfully!
            }
        });
    }
}
```

在执行组件方法结束时，状态管理器会自动保存通过插入或更新操作添加或修改的任何值。 根据所用的设置，“保存”可能包括持久保存到磁盘和复制。 未修改的值不会持久保存或复制。 如果未修改任何值，保存操作不起作用。 如果保存失败，将丢弃修改的状态并重新加载原始状态。

也可以通过对执行组件基调用 `SaveStateAsync` 方法来手动保存状态：

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```
```Java
interface MyActor {
    CompletableFuture setCountAsync(int count)
    {
        this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value).thenApply();

        this.stateManager().saveStateAsync().thenApply();
    }
}
```

## <a name="remove-state"></a>删除状态
可以通过调用 *Remove* 方法，从执行组件的状态管理器中永久删除状态。 尝试删除不存在的键时，该方法会引发 `KeyNotFoundException`(C#) 或 `NoSuchElementException`(Java)。

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().removeStateAsync("MyState");
    }
}
```

还可以使用 *TryRemove* 方法永久删除状态。 尝试删除不存在的键时，该方法不会引发。

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().tryRemoveStateAsync("MyState").thenApply((result)->{
            if(result)
            {
                // State removed!
            }
        });
    }
}
```

## <a name="next-steps"></a>后续步骤

存储在 Reliable Actors 中的状态必须进行序列化，然后才能将其写入到磁盘并进行复制以实现高可用性。 详细了解[执行组件类型序列化](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)。

接下来，详细了解[执行组件诊断和性能监视](service-fabric-reliable-actors-diagnostics.md)。
