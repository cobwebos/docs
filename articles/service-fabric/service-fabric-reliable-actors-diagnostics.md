<properties
   pageTitle="Reliable Actors 诊断和监视 | Microsoft Azure"
   description="本文描述了 Service Fabric Reliable Actors 运行时中的诊断和性能监视功能，包括由其发出的事件和性能计数器。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="01/26/2016"
   wacn.date=""/>

# Reliable Actors 的诊断和性能监视
Reliable Actors 运行时发出 [EventSource](https://msdn.microsoft.com/zh-cn/library/system.diagnostics.tracing.eventsource.aspx) 事件和[性能计数器](https://msdn.microsoft.com/zh-cn/library/system.diagnostics.performancecounter.aspx)，可用于深入了解运行时如何操作并帮助进行故障排除和性能监视。

## EventSource 事件
Reliable Actors 运行时的 EventSource 提供程序名称为“Microsoft-ServiceFabric-Actors”。当[在 Visual Studio 中调试](/documentation/articles/service-fabric-debugging-your-application)执行组件应用程序时，来自此事件源的事件在[“诊断事件”](/documentation/articles/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally#view-service-fabric-system-events-in-visual-studio)窗口中显示。

有助于收集和/或查看 EventSource 事件的工具和技术的示例包括 [PerfView](http://www.microsoft.com/download/details.aspx?id=28567)、[Azure 诊断](/documentation/articles/cloud-services-dotnet-diagnostics)、[语义日志记录](https://msdn.microsoft.com/zh-cn/library/dn774980.aspx)和 [Microsoft TraceEvent 库](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)。

### 关键字
属于 Reliable Actors EventSource 的所有事件都与一个或多个关键字相关联。这样能够对收集的事件进行筛选。定义了以下关键字位。

|Bit|说明|
|---|---|
|0x1|汇总 Fabric Actors 运行时的操作的重要事件集。|
|0x2|描述执行组件方法调用的事件集。有关详细信息，请参阅[有关执行组件的简介主题](/documentation/articles/service-fabric-reliable-actors-introduction#actors)。|
|0x4|与执行组件状态相关的事件集。有关详细信息，请参阅有关[有状态执行组件](/documentation/articles/service-fabric-reliable-actors-introduction#stateful-actors)的主题。|
|0x8|与执行组件中基于轮次的并发相关的事件集。有关详细信息，请参阅有关[并发性](/documentation/articles/service-fabric-reliable-actors-introduction#concurrency)的主题。|

## 性能计数器
Reliable Actors 运行时定义以下性能计数器类别。

|类别|说明|
|---|---|
|Service Fabric Actor|特定于 Azure Service Fabric Actor 的计数器，例如保存执行组件状态所用的时间。|
|Service Fabric Actor 方法|特定于由 Service Fabric Actor 实现的方法的计数器，例如调用执行组件方法的频率。|

以上每个类别都有一个或多个计数器。

默认情况下在 Windows 操作系统中提供的 [Windows 性能监视器](https://technet.microsoft.com/zh-cn/library/cc749249.aspx)应用程序可用于收集和查看性能计数器数据。[Azure 诊断](/documentation/articles/cloud-services-dotnet-diagnostics)是用于收集性能计数器数据并将其上载到 Azure 表的另一个选项。

### 性能计数器实例名称
具有大量执行组件服务或执行组件服务分区的群集将具有大量执行组件性能计数器实例。性能计数器实例名称可帮助标识与此性能计数器实例相关联的特定[分区](/documentation/articles/service-fabric-reliable-actors-platform#service-fabric-partition-concepts-for-actors)和执行组件方法（如果适用）。

#### Service Fabric Actor 类别
对于类别 `Service Fabric Actor`，计数器实例名称采用以下格式：

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* 是与性能计数器实例关联的 Service Fabric 分区 ID 的字符串表示。分区 ID 是 GUID，并且其字符串表示是通过使用格式说明符“D”的 [`Guid.ToString`](https://msdn.microsoft.com/zh-cn/library/97af8hh4.aspx) 方法生成的。

*ActorRuntimeInternalID* 是由 Fabric Actors 运行时生成的供其内部使用的 64 位整数的字符串表示。这包括在性能计数器实例名称中，以确保其唯一性并避免与其他性能计数器实例名称发生冲突。用户不应尝试解释此部分的性能计数器实例名称。

下面是属于 `Service Fabric Actor` 类别的计数器的计数器实例名称的示例：

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

在上述示例中，`2740af29-78aa-44bc-a20b-7e60fb783264` 是 Service Fabric 分区 ID 的字符串表示，`635650083799324046` 是为运行时的内部使用而生成的 64 位 ID。

#### Service Fabric 执行组件方法类别
对于类别 `Service Fabric Actor Method`，计数器实例名称采用以下格式：

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* 是与性能计数器实例关联的执行组件方法的名称。方法名称的格式是根据 Fabric Actors 运行时中的一些逻辑来确定的，该逻辑可以平衡名称的可读性和 Windows 上对性能计数器实例名称的最大长度的约束。

*ActorsRuntimeMethodId* 是由 Fabric Actors 运行时生成的供其内部使用的 32 位整数的字符串表示。这包括在性能计数器实例名称中，以确保其唯一性并避免与其他性能计数器实例名称发生冲突。用户不应尝试解释此部分的性能计数器实例名称。

*ServiceFabricPartitionID* 是与性能计数器实例关联的 Service Fabric 分区 ID 的字符串表示。分区 ID 是 GUID，并且其字符串表示是通过使用格式说明符“D”的 [`Guid.ToString`](https://msdn.microsoft.com/zh-cn/library/97af8hh4.aspx) 方法生成的。

*ActorRuntimeInternalID* 是由 Fabric Actors 运行时生成的供其内部使用的 64 位整数的字符串表示。这包括在性能计数器实例名称中，以确保其唯一性并避免与其他性能计数器实例名称发生冲突。用户不应尝试解释此部分的性能计数器实例名称。

下面是属于 `Service Fabric Actor Method` 类别的计数器的计数器实例名称的示例：

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

在上述示例中，`ivoicemailboxactor.leavemessageasync` 是方法名称，`2` 是为运行时的内部使用而生成的 32 位 ID，`89383d32-e57e-4a9b-a6ad-57c6792aa521` 是 Service Fabric 分区 ID 的字符串表示形式，`635650083804480486` 是为运行时的内部使用而生成的 64 位 ID。

## 事件和性能计数器的列表

### 执行组件方法事件和性能计数器
Reliable Actors 运行时发出与 [执行组件方法](/documentation/articles/service-fabric-reliable-actors-introduction#actors)相关的以下事件。

|事件名称|事件 ID|级别|关键字|说明|
|---|---|---|---|---|
|ActorMethodStart|7|详细|0x2|执行组件运行时即将调用执行组件方法。|
|ActorMethodStop|8|详细|0x2|执行组件方法已执行完毕。也就是说，已返回运行时的对执行组件方法的异步调用，此执行组件方法返回的任务已完成。|
|ActorMethodThrewException|9|警告|0x3|在执行执行组件方法时，即在运行时的对执行组件方法的异步调用过程中或在执行执行组件方法返回的任务过程中引发异常。此事件表示执行组件代码中出现某种形式的故障，需要调查。|

Reliable Actors 运行时发布与执行执行组件方法相关的下列性能计数器。

|类别名称|计数器名称|说明|
|---|---|---|
|Service Fabric Actor 方法|调用/秒|每秒调用执行组件服务方法的次数|
|Service Fabric Actor 方法|每次调用的平均毫秒数|以毫秒为单位执行执行组件服务方法所用的时间|
|Service Fabric Actor 方法|引发的异常数/秒|执行组件服务方法每秒引发异常的次数|

### 并发事件和性能计数器
Reliable Actors 运行时发出与[并发](/documentation/articles/service-fabric-reliable-actors-introduction#concurrency)相关的以下事件。

|事件名称|事件 ID|级别|关键字|说明|
|---|---|---|---|---|
|ActorMethodCallsWaitingForLock|12|详细|0x8|在执行组件中每次新的轮次开始时写入此事件。它包含挂起的执行组件调用的数目。这些调用正在等待获取用于强制执行基于轮次的并发的按执行组件的锁。|

Reliable Actors 运行时发布与并发相关的以下性能计数器。

|类别名称|计数器名称|说明|
|---|---|---|
|Service Fabric Actor|等待执行组件锁的执行组件调用次数|等待获取强制执行基于轮次的并发的每执行组件锁的待处理执行组件调用次数。|

### 执行组件状态管理事件和性能计数器
Reliable Actors 运行时发出与 [执行组件状态管理](/documentation/articles/service-fabric-reliable-actors-introduction#actor-state-management)相关的以下事件。

|事件名称|事件 ID|级别|关键字|说明|
|---|---|---|---|---|
|ActorSaveStateStart|10|详细|0x4|执行组件运行时即将保存执行组件状态。|
|ActorSaveStateStop|11|详细|0x4|执行组件运行时已完成保存执行组件状态。|

Reliable Actors 运行时发布与执行组件状态管理相关的以下性能计数器。

|类别名称|计数器名称|说明|
|---|---|---|
|Service Fabric Actor|每个保存状态操作的平均毫秒数|以毫秒为单位保存执行组件状态所用的时间|

### 与无状态 Actor 实例相关的事件
Reliable Actors 运行时发出与[无状态执行组件实例](/documentation/articles/service-fabric-reliable-actors-platform#service-fabric-partition-concepts-for-stateless-actors)相关的以下事件。

|事件名称|事件 ID|级别|关键字|说明|
|---|---|---|---|---|
|ServiceInstanceOpen|3|信息性|0x1|打开无状态执行组件实例。这意味着可以在此实例（并且也可能是其他实例）内创建此分区的执行组件。|
|ServiceInstanceClose|4|信息性|0x1|关闭无状态执行组件实例。这意味着不再在此实例内创建此分区的执行组件。会将任何新请求传送到此实例中已创建的执行组件。正在进行中的任何请求完成后将会销毁执行组件。|

### 与有状态执行组件副本相关的事件
Reliable Actors 运行时发出与[有状态执行组件副本](/documentation/articles/service-fabric-reliable-actors-platform#service-fabric-partition-concepts-for-stateful-actors)相关的以下事件。

|事件名称|事件 ID|级别|关键字|说明|
|---|---|---|---|---|
|ReplicaChangeRoleToPrimary|1|信息性|0x1|有状态执行组件副本将角色更改为“主要”。这意味着在此副本内创建此分区的 Actor。|
|ReplicaChangeRoleFromPrimary|2|信息性|0x1|有状态执行组件副本将角色更改为“非主要”。这意味着不再在此副本内创建此分区的执行组件。不会将任何新请求传送到此副本中已创建的执行组件。正在进行中的任何请求完成后将会销毁执行组件。|

### Actor 激活和停用事件
Reliable Actors 运行时发出与 [执行组件激活和停用](/documentation/articles/service-fabric-reliable-actors-lifecycle)相关的以下事件。

|事件名称|事件 ID|级别|关键字|说明|
|---|---|---|---|---|
|ActorActivated|5|信息性|0x1|执行组件已激活。|
|ActorDeactivated|6|信息性|0x1|执行组件已停用。|

<!---HONumber=Mooncake_0307_2016-->