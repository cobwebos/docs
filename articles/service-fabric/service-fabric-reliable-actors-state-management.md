---
title: "Reliable Actors 状态管理 | Microsoft 文档"
description: "介绍如何管理、持久保存和复制 Reliable Actors 状态以实现高可用性。"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: fae68c9fb40951e3f7a6fce67d75872cecfc52bd
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2017
---
# <a name="reliable-actors-state-management"></a>Reliable Actors 状态管理
Reliable Actors 是可封装逻辑与状态的单线程对象。 由于执行组件在 Reliable Services 上运行，因此，它们可以使用 Reliable Services 所用的相同持久性和复制机制可靠地维护状态。 这样，执行组件就不会在发生故障之后、在内存回收后重新激活时或者由于资源平衡和升级的原因而在群集中的节点之间移动时丢失其状态。

## <a name="state-persistence-and-replication"></a>状态持久性和复制
之所以认为所有 Reliable Actors *有状态*，是因为每个执行组件实例将映射到唯一的 ID。 这意味着，对同一个执行组件 ID 所做的重复调用将路由到同一个执行组件实例。 与此相反，在无状态系统中，客户端调用不一定每次都路由到同一台服务器。 出于此原因，执行组件服务永远都是有状态服务。

即使执行组件被视为有状态，也并不表示它们必须以可靠的方式存储状态。 执行组件可以根据其数据存储要求来选择状态持久性和复制的级别：

* **持久化状态：**状态持久保存在磁盘中，并复制到 3 个或更多个副本。 这是最持久的状态存储选项，可通过完全群集中断来持久保留状态。
* **易失性状态：**状态被复制到 3 个或更多个副本，且仅保存在内存中。 这可针对节点故障、执行组件故障，以及在升级和资源平衡过程中提供复原能力。 但是，状态不会保留在磁盘中。 因此，如果同时丢失所有副本，状态也会丢失。
* **非持久化状态**：状态不复制，也不写入磁盘。 此级别适用于完全不需要以可靠方式维护状态的执行组件。

每个级别的持久性只是服务的不同*状态提供程序*和*复制*配置。 是否要将状态写入磁盘取决于状态提供程序（可靠服务中存储状态的组件）。 复制取决于要使用多少个副本来部署服务。 如同 Reliable Services，可以轻松地手动设置状态提供程序和副本计数。 执行组件框架提供一个属性，对执行组件使用时，该属性会自动选择默认的状态提供程序，并自动生成副本计数的设置，以实现这三种持久性设置中的一个。 StatePersistence 属性不由派生类继承，每个执行组件类型必须提供其 StatePersistence 级别。

### <a name="persisted-state"></a>持久化状态
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
此设置使用一个状态提供程序，该提供程序可在磁盘上存储数据，并自动将服务副本计数设置为 3。

### <a name="volatile-state"></a>易失性状态
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
此设置使用仅在内存中的状态提供程序，并将副本计数设置为 3。

### <a name="no-persisted-state"></a>非持久化状态
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
此设置使用仅在内存中的状态提供程序，并将副本计数设置为 1。

### <a name="defaults-and-generated-settings"></a>默认值和生成的设置
如果使用 `StatePersistence` 属性，在执行组件服务启动时，系统会在运行时自动选择状态提供程序。 但是，副本计数会在编译时由 Visual Studio 执行组件构建工具设置。 构建工具在 ApplicationManifest.xml 中自动为执行组件服务生成*默认服务*。 参数是针对**副本集大小下限**和**目标副本集大小**创建的。

可手动更改这些参数。 但是，每当 `StatePersistence` 属性更改时，参数将设置为所选 `StatePersistence` 属性的默认副本集大小值，并覆盖所有旧值。 换言之，更改 `StatePersistence` 属性值时，在 ServiceManifest.xml 中设置的值将仅在生成时被覆盖。

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>状态管理器
每个执行组件实例都有其自身的状态管理器：一种类似于字典的数据结构，能够可靠地存储密钥-值对。 状态管理器是围绕状态提供程序的包装。 无论使用哪个持久性设置，都可以使用它来存储数据。 它不保证运行中的执行组件服务可在保留数据的同时，以通过滚动升级从易失性（仅在内存中）状态设置更改保存的状态设置。 但是，针对运行中的服务更改副本计数是可行的。

状态管理器密钥必须为字符串。 值是泛型的，可以是任何类型（包括自定义类型）。 存储在状态管理器中的值必须可进行数据约定序列化的，因为根据执行组件的状态持久性设置，它们可能在复制期间通过网络传输到其他节点，并且可能写入磁盘。

状态管理器公开通用字典方法来管理状态，类似于在可靠字典中找到的项目。

### <a name="accessing-state"></a>访问状态
可以通过状态管理器根据键来访问状态。 状态管理器方法全都是异步的，因为当执行组件具有持久化状态时，它们可能需要磁盘 I/O。 首次访问时，状态对象将缓存在内存中。 重复访问操作将从内存中直接访问对象并以同步方式返回，而不造成磁盘 I/O 或异步上下文切换的开销。 在以下情况下，，将从缓存中删除状态对象：

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

### <a name="saving-state"></a>保存状态
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

### <a name="removing-state"></a>删除状态
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
