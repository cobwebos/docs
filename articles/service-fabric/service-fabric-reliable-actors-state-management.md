---
title: Reliable Actors 状态管理 | Microsoft 文档
description: 介绍如何管理、持久保存和复制 Reliable Actors 状态以实现高可用性。
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
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: d5d38e7fa80db3484c397d9840bbc6092e4f18bb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="reliable-actors-state-management"></a>Reliable Actors 状态管理
Reliable Actors 是可封装逻辑与状态的单线程对象。 由于执行组件在 Reliable Services 上运行，因此，它们可以使用相同的持久性和复制机制可靠地维护状态。 这样，执行组件就不会在发生故障之后、在内存回收后重新激活时或者由于资源平衡和升级的原因而在群集中的节点之间移动时丢失其状态。

## <a name="state-persistence-and-replication"></a>状态持久性和复制
之所以认为所有 Reliable Actors *有状态*，是因为每个执行组件实例将映射到唯一的 ID。 这意味着，对同一个执行组件 ID 所做的重复调用将路由到同一个执行组件实例。 与此相反，在无状态系统中，客户端调用不一定每次都路由到同一台服务器。 出于此原因，执行组件服务永远都是有状态服务。

即使执行组件被视为有状态，也并不表示它们必须以可靠的方式存储状态。 执行组件可以根据其数据存储要求来选择状态持久性和复制的级别：

* **持久化状态：**状态持久保存到磁盘中，并复制到 3 个或更多个副本。 持久化状态是最持久的状态存储选项，在完全群集中断期间其状态也能持久保留。
* **易失性状态：**状态被复制到 3 个或更多个副本，且仅保存在内存中。 易失性状态可针对节点故障、执行组件故障，以及在升级和资源均衡过程中提供复原能力。 但是，状态不会保留在磁盘中。 因此，如果同时丢失所有副本，状态也会丢失。
* **非持久化状态**：状态不会复制或写入到磁盘，仅用于不需要可靠地维护状态的执行组件。

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

有关管理执行组件状态的示例，请阅读[访问、保存并删除 Reliable Actors 状态](service-fabric-reliable-actors-access-save-remove-state.md)。

## <a name="best-practices"></a>最佳实践
对于管理执行组件状态，下面是一些建议的做法和故障排除技巧。

### <a name="make-the-actor-state-as-granular-as-possible"></a>使执行组件状态尽可能细粒度
这对于应用程序的性能和资源使用情况是至关重要的。 只要对执行组件的“命名状态”有任何写入/更新操作，与该“命名状态”相对应的整个值就会进行序列化，并通过网络发送到次要副本。  次要副本将它写入到本地磁盘并答复主要副本。 当主要副本收到来自次要副本仲裁的确认时，它会将该状态写入到其本地磁盘。 例如，假设该值是一个具有 20 个成员的类，其大小为 1 MB。 即使只修改了其中一个类成员（大小为 1 KB），最终也得支付全部 1 MB 的序列化、网络和磁盘写入的成本。 同样，如果该值为一个集合（如列表、数组或字典），即使修改其中一个成员，也得支付整个集合的成本。 actor 类的 StateManager 接口类似于字典。 你始终应为此字典之上表示执行组件状态的数据结构建模。
 
### <a name="correctly-manage-the-actors-life-cycle"></a>正确管理执行组件的生命周期
你应制定明确的策略来管理每个分区中执行组件服务状态的大小。 执行组件服务应具有固定数目的执行组件，并尽可能多地重复使用它们。 如果不断地创建新的执行组件，则必须在这些执行组件完成其工作后删除它们。 执行组件框架存储有关存在的每个执行组件的一些元数据。 删除某个执行组件的所有状态不会删除有关该执行组件的元数据。 必须删除执行组件（请参阅[删除执行组件及其状态](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)），才能删除系统中存储的有关它的所有信息。 作为额外的检查，应偶尔查询执行组件服务一次（请参阅[枚举执行组件](service-fabric-reliable-actors-platform.md)）以确保执行组件数在预期范围内。
 
如果看到执行组件服务的数据库文件大小不断增加到超出预期大小，请确保你是按照前面的指南进行操作的。 如果你是按照这些指南操作的，但仍存在数据库文件大小问题，应通过产品团队[开具支持票证](service-fabric-support.md)以获取帮助。

## <a name="next-steps"></a>后续步骤

存储在 Reliable Actors 中的状态必须进行序列化，然后才能将其写入到磁盘并进行复制以实现高可用性。 详细了解[执行组件类型序列化](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)。

接下来，详细了解[执行组件诊断和性能监视](service-fabric-reliable-actors-diagnostics.md)。
