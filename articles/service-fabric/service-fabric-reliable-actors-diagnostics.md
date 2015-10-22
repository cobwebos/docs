<properties
   pageTitle="Reliable Actors 诊断和性能监视"
   description="本文说明了 Reliable Actors 运行时中的诊断和性能监视功能，包括由其发出的事件和性能计数器。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/05/2015"
   wacn.date=""/>

# Reliable Actors 的诊断和性能监视
Reliable Actors 运行时发出 [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 事件和[性能计数器](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx)，可用于深入了解运行时如何操作并帮助进行故障排除和性能监视。

## EventSource 事件
Reliable Actors 运行时的 EventSource 名称是“Microsoft-ServiceFabric-Actors”。当[在 Visual Studio 中调试](/documentation/articles/service-fabric-debugging-your-application) Actor 应用程序时，来自此事件源的事件在[“诊断事件”](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio)窗口中显示。

Service Fabric 还提供将这些事件定向到 [Application Insights](http://azure.microsoft.com/services/application-insights/) 的选项。有关这方面的详细信息，请参阅有关[适用于 Service Fabric 的 Application Insights 设置](/documentation/articles/service-fabric-diagnostics-application-insights-setup)的文章。

有助于收集和/或查看 EventSource 事件的工具和技术的其他示例包括 [PerfView](http://www.microsoft.com/download/details.aspx?id=28567)、[Azure 诊断](../cloud-services-dotnet-diagnostics.md)、[语义式日志记录](https://msdn.microsoft.com/library/dn774980.aspx)和 [Microsoft TraceEvent 库](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)。

### 关键字
属于 Reliable Actors EventSource 的所有事件都与一个或多个关键字相关联。这样能够对收集的事件进行筛选。以下关键字位定义：

|Bit|说明|
|---|---|
|0x1|汇总 Fabric Actors 运行时的操作的重要事件集|
|0x2|说明 Actor 方法调用的事件集。有关详细信息，请参阅[有关 Actor 的介绍性主题](/documentation/articles/service-fabric-reliable-actors-introduction#actors)。|
|0x4|与 Actor 状态相关的事件集。有关详细信息，请参阅有关[有状态 Actor](/documentation/articles/service-fabric-reliable-actors-introduction#stateful-actors) 的主题。|
|0x8|与 Actor 中基于轮次的并发相关的事件集。有关详细信息，请参阅有关[并发](/documentation/articles/service-fabric-reliable-actors-introduction#concurrency)的主题。|

## 性能计数器
Reliable Actors 运行时定义以下性能计数器类别。

|类别|说明|
|---|---|
|Service Fabric Actor|特定于 Service Fabric Actor 的计数器，例如保存 Actor 状态所用的时间。|
|Service Fabric Actor 方法|特定于由 Service Fabric Actor 实现的方法的计数器，例如调用 Actor 方法的频率。|

以上每个类别都有一个或多个计数器。

默认情况下在 Windows 操作系统中提供的 [Windows 性能监视器](https://technet.microsoft.com/library/cc749249.aspx)应用程序可用于收集和查看性能计数器数据。[Azure 诊断](/documentation/articles/cloud-services-dotnet-diagnostics)是用于收集性能计数器数据并将其上载到 Azure 表的另一个选项。

### 性能计数器实例名称
具有大量 Actor Service 或 Actor Service 分区的群集将有大量 Actor 性能计数器实例。性能计数器实例名称可帮助确定性能计数器实例相关联的特定[分区](/documentation/articles/service-fabric-reliable-actors-platform#service-fabric-partition-concepts-for-actors)和 Actor 方法（如果适用）。

#### `Service Fabric Actor` 类别
对于类别 `Service Fabric Actor`，计数器实例名称采用以下格式：

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* - 是性能计数器实例与之关联的 Service Fabric 分区 ID 的字符串表示形式。分区 ID 是 GUID，并且其字符串表示形式是通过使用带格式说明符“D”的 [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) 方法而生成。

*ActorRuntimeInternalID* - 是由其内部使用的 Fabric Actors 运行时生成的 64 位整数的字符串表示形式。这包括在性能计数器实例名称中，以确保其唯一性并避免与其他性能计数器实例名称发生冲突。用户不应尝试解释此部分的性能计数器实例名称。

下面是属于“Service Fabric Actor”类别的计数器的计数器实例名称的示例：

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

在上述示例中，`2740af29-78aa-44bc-a20b-7e60fb783264` 是 Service Fabric 分区 ID 的字符串表示形式，`635650083799324046` 是为运行时的内部使用而生成的 64 位 ID。

#### `Service Fabric Actor Method` 类别
对于类别 `Service Fabric Actor Method`，计数器实例名称采用以下格式：

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* - 是性能计数器实例与之关联的 Actor 方法的名称。方法名称的格式是根据 Fabric Actors 运行时中的一些逻辑来确定的，该逻辑可以平衡名称的可读性和 Windows 上对性能计数器实例名称的最大长度的约束。

*ActorsRuntimeMethodId* - 是由其内部使用的 Fabric Actors 运行时生成的 32 位整数的字符串表示形式。这包括在性能计数器实例名称中，以确保其唯一性并避免与其他性能计数器实例名称发生冲突。用户不应尝试解释此部分的性能计数器实例名称。

*ServiceFabricPartitionID* - 是性能计数器实例与之关联的 Service Fabric 分区 ID 的字符串表示形式。分区 ID 是 GUID，并且其字符串表示形式是通过使用带格式说明符“D”的 [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) 方法而生成。

*ActorRuntimeInternalID* - 是由其内部使用的 Fabric Actors 运行时生成的 64 位整数的字符串表示形式。这包括在性能计数器实例名称中，以确保其唯一性并避免与其他性能计数器实例名称发生冲突。用户不应尝试解释此部分的性能计数器实例名称。

下面是属于“Service Fabric Actor Method”类别的计数器的计数器实例名称的示例：

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

在上述示例中，`ivoicemailboxactor.leavemessageasync` 是方法名称，`2` 是为运行时的内部使用而生成的 32 位 ID，`89383d32-e57e-4a9b-a6ad-57c6792aa521` 是 Service Fabric 分区 ID 的字符串表示形式，`635650083804480486` 是为运行时的内部使用而生成的 64 位 ID。

## 事件和性能计数器的列表

### Actor 方法事件和性能计数器
Reliable Actors 运行时发出与 [Actor 方法](/documentation/articles/service-fabric-reliable-actors-introduction#actors)相关的以下事件。

|事件名称|事件 ID|级别|关键字|说明|
|---|---|---|---|---|
|ActorMethodStart|7|详细|0x2|Actors 运行时即将调用 Actor 方法|
|ActorMethodStop|8|详细|0x2|Actor 方法执行完毕，即：运行时对 Actor 方法的异步调用已返回，Actor 方法返回的任务已完成。|
|ActorMethodThrewException|9|警告|0x3|在运行时对 Actor 方法执行异步调用过程中或在执行 Actor 方法返回的任务过程中，执行 Actor 方法期间引发异常。此事件表示 Actor 代码中出现某种形式的故障，需要调查。|

Reliable Actors 运行时发布与执行 Actor 方法相关的下列性能计数器。

|类别名称|计数器名称|说明|
|---|---|---|
|Service Fabric Actor 方法|调用/秒|每秒调用的 Actor Service 方法的次数|
|Service Fabric Actor 方法|每次调用的平均毫秒数|以毫秒为单位执行 Actor Service 方法所用的时间|
|Service Fabric Actor 方法|引发的异常数/秒|Actor Service 方法每秒引发异常的次数|

### 并发事件和性能计数器
Reliable Actors 运行时发出与[并发](/documentation/articles/service-fabric-reliable-actors-introduction#concurrency)相关的以下事件。

|事件名称|事件 ID|级别|关键字|说明|
|---|---|---|---|---|
|ActorMethodCallsWaitingForLock|12|详细|0x8|在 Actor 中每次新的轮次开始时写入此事件。它包含等待获取强制执行基于轮次的并发的每 Actor 锁的待处理 Actor 调用次数。|

Reliable Actors 运行时发布与并发相关的以下性能计数器。

|类别名称|计数器名称|说明|
|---|---|---|
|Service Fabric Actor|等待 Actor 锁的 Actor 调用次数|等待获取强制执行基于轮次的并发的每 Actor 锁的待处理 Actor 调用次数。|

### Actor 状态管理事件和性能计数器
Reliable Actors 运行时发出与 [Actor 状态管理](/documentation/articles/service-fabric-reliable-actors-introduction#actor-state-management)相关的以下事件。

|事件名称|事件 ID|级别|关键字|说明|
|---|---|---|---|---|
|ActorSaveStateStart|10|详细|0x4|Actors 运行时即将保存 Actor 状态。|
|ActorSaveStateStop|11|详细|0x4|Actors 运行时已完成保存 Actor 状态。|

Reliable Actors 运行时发布与 Actor 状态管理相关的以下性能计数器。

|类别名称|计数器名称|说明|
|---|---|---|
|Service Fabric Actor|每个保存状态操作的平均毫秒数|以毫秒为单位保存 Actor 状态所用的时间|

### 与无状态 Actor 实例相关的事件
Reliable Actors 运行时发出与[无状态 Actor 实例](/documentation/articles/service-fabric-reliable-actors-platform#service-fabric-partition-concepts-for-stateless-actors)相关的以下事件。

|事件名称|事件 ID|级别|关键字|说明|
|---|---|---|---|---|
|ServiceInstanceOpen|3|信息性|0x1|打开无状态 Actor 实例。这意味着可以在此实例（并且也可能是其他实例）内创建此分区的 Actor。|
|ServiceInstanceClose|4|信息性|0x1|关闭无状态 Actor 实例。这意味着不再在此实例内创建此分区的 Actor。会将任何新请求传送到此实例中已创建的 Actor。正在进行中的任何请求完成后将会销毁 Actor。|

### 与有状态 Actor 副本相关的事件
Reliable Actors 运行时发出与[有状态 Actor 副本](/documentation/articles/service-fabric-reliable-actors-platform#service-fabric-partition-concepts-for-stateful-actors)相关的以下事件。

|事件名称|事件 ID|级别|关键字|说明|
|---|---|---|---|---|
|ReplicaChangeRoleToPrimary|1|信息性|0x1|有状态 Actor 副本将角色更改为“主要”。这意味着在此副本内创建此分区的 Actor。|
|ReplicaChangeRoleFromPrimary|2|信息性|0x1|有状态 Actor 副本将角色更改为“非主要”。这意味着不再在此副本内创建此分区的 Actor。不会将任何新请求传送到此副本中已创建的 Actor。正在进行中的任何请求完成后将会销毁 Actor。|

### Actor 激活和停用事件
Reliable Actors 运行时发出与 [Actor 激活和停用](/documentation/articles/service-fabric-reliable-actors-lifecycle)相关的以下事件。

|事件名称|事件 ID|级别|关键字|说明|
|---|---|---|---|---|
|ActorActivated|5|信息性|0x1|Actor 已激活。|
|ActorDeactivated|6|信息性|0x1|Actor 已停用。|

<!---HONumber=74-->