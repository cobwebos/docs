---
title: Azure Service Fabric 有状态 Reliable Services 诊断
description: Azure Service Fabric 中的有状态 Reliable Services 的诊断功能
author: dkkapur
ms.topic: conceptual
ms.date: 8/24/2018
ms.author: dekapur
ms.openlocfilehash: 37162287e130b05dc41453c579b3a628ac878fca
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282258"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>有状态 Reliable Services 的诊断功能
Azure Service Fabri 有状态 Reliable Services StatefulServiceBase 类会发出 [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 事件，这些事件可用于调试服务、提供对运行时运行方式的深入了解，以及帮助进行故障排除。

## <a name="eventsource-events"></a>EventSource 事件
有状态 Reliable Services StatefulServiceBase 类的 EventSource 名称是“Microsoft-ServiceFabric-Services”。 当[在 Visual Studio 中调试](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio)服务时，来自此事件源的事件会显示在“[诊断事件](service-fabric-debugging-your-application.md)”窗口中。

可帮助收集和/或查看 EventSource 事件的工具和技术的示例包括：[PerfView](https://www.microsoft.com/download/details.aspx?id=28567)、[Azure 诊断](../cloud-services/cloud-services-dotnet-diagnostics.md)和 [Microsoft TraceEvent 库](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)。

## <a name="events"></a>事件
| 事件名称 | 事件 ID | 级别 | 事件说明 |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |信息 |在服务 RunAsync 任务启动时发出 |
| StatefulRunAsyncCancellation |2 |信息 |在服务 RunAsync 任务取消时发出 |
| StatefulRunAsyncCompletion |3 |信息 |在服务 RunAsync 任务完成时发出 |
| StatefulRunAsyncSlowCancellation |4 |警告 |在服务 RunAsync 任务完成取消所用时间过长时发出 |
| StatefulRunAsyncFailure |5 |错误 |在服务 RunAsync 任务引发异常时发出 |

## <a name="interpret-events"></a>解释事件
StatefulRunAsyncInvocation、StatefulRunAsyncCompletion 和 StatefulRunAsyncCancellation 事件可用于供服务编写者了解服务的生命周期以及服务启动、取消和完成时的计时。 在调试服务问题或了解服务生命周期时，此信息可能会十分有用。

服务编写者应请密切注意 StatefulRunAsyncSlowCancellation 和 StatefulRunAsyncFailure 事件，因为它们指示与服务有关的问题。

每当服务 RunAsync() 任务引发异常时，都会发出 StatefulRunAsyncFailure。 引发的异常通常指示服务中存在错误或 bug。 此外，该异常会导致服务失败，从而将服务移到另一个节点。 此操作可能是一种高开销操作，并会在移动服务时延迟传入请求。 服务编写者应确定异常的原因并在可能时缓解它。

每当 RunAsync 任务的取消请求花费的时间超过四秒时，都会发出 StatefulRunAsyncSlowCancellation。 当服务花费过长时间完成取消时，它会影响在另一个节点快速重启服务的能力。 这种情况可能会影响服务的总体可用性。

## <a name="performance-counters"></a>性能计数器
Reliable Services 运行时定义以下性能计数器类别：

| 类别 | 说明 |
| --- | --- |
| Service Fabric 事务性复制器 |特定于 Azure Service Fabric 事务性复制器的计数器 |
| Service Fabric TStore |特定于 Azure Service Fabric TStore 的计数器 |

Service Fabric 事务性复制器供[可靠状态管理器](service-fabric-reliable-services-reliable-collections-internals.md)用来在给定的[副本](service-fabric-concepts-replica-lifecycle.md)集内复制事务。

Service Fabric TStore 是[可靠集合](service-fabric-reliable-services-reliable-collections-internals.md)中使用的组件，用于存储和检索键值对。

Windows 操作系统中默认可用的 [Windows 性能监视器](https://technet.microsoft.com/library/cc749249.aspx)应用程序可用于收集和查看性能计数器数据。 [Azure 诊断](../cloud-services/cloud-services-dotnet-diagnostics.md)是另一种用于收集性能计数器数据并将其上传到 Azure 表的工具。

### <a name="performance-counter-instance-names"></a>性能计数器实例名称
具有大量 Reliable Services 或 Reliable Services 分区的群集将具有大量事务复制器性能计数器实例。 对于 TStore 性能计数器也是如此，但还乘以可靠字典和可靠队列使用的数量。 性能计数器实例名称有助于标识与性能计数器实例关联的特定[分区](service-fabric-concepts-partitioning.md)、服务副本和状态提供程序（在 TStore 的情况下）。

#### <a name="service-fabric-transactional-replicator-category"></a>Service Fabric 事务性复制器类别
对于类别 `Service Fabric Transactional Replicator`，计数器实例名称采用以下格式：

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* 是与性能计数器实例相关联的 Service Fabric 分区 ID 的字符串表示形式。 分区 ID 是 GUID，并且其字符串表示形式是通过使用格式说明符“D”的 [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) 生成的。

*ServiceFabricReplicaId* 是一种与给定的 Reliable Service 副本相关联的 ID。 副本 ID 包括在性能计数器实例名称中，以确保其唯一性并避免与同一分区生成的其他性能计数器实例发生冲突。 [此处](service-fabric-concepts-replica-lifecycle.md)提供有关副本及其在 Reliable Services 中的角色的更多详细信息。

以下计数器实例名称通常适用于 `Service Fabric Transactional Replicator` 类别的计数器：

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

在上述示例中，`00d0126d-3e36-4d68-98da-cc4f7195d85e` 是 Service Fabric 分区 ID 的字符串表示形式，`131652217797162571` 是副本 ID。

#### <a name="service-fabric-tstore-category"></a>Service Fabric TStore 类别
对于类别 `Service Fabric TStore`，计数器实例名称采用以下格式：

`ServiceFabricPartitionId:ServiceFabricReplicaId:StateProviderId_PerformanceCounterInstanceDifferentiator_StateProviderName`

*ServiceFabricPartitionId* 是与性能计数器实例相关联的 Service Fabric 分区 ID 的字符串表示形式。 分区 ID 是 GUID，并且其字符串表示形式是通过使用格式说明符“D”的 [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) 生成的。

*ServiceFabricReplicaId* 是一种与给定的 Reliable Service 副本相关联的 ID。 副本 ID 包括在性能计数器实例名称中，以确保其唯一性并避免与同一分区生成的其他性能计数器实例发生冲突。 [此处](service-fabric-concepts-replica-lifecycle.md)提供有关副本及其在 Reliable Services 中的角色的更多详细信息。

*StateProviderId*是与可靠服务中的状态提供程序相关联的 ID。 状态提供程序 ID 包括在性能计数器实例名称中，以便将 TStore 与其他项区分开来。

*PerformanceCounterInstanceDifferentiator* 是与状态提供程序中的性能计数器实例关联的区分 ID。 此区分符 包含在性能计数器实例名称中，以确保其唯一性并避免与其他性能计数器实例（由同一状态提供程序生成）发生冲突。

*StateProviderName*是与可靠服务中的状态提供程序关联的名称。 状态提供程序名称包括在性能计数器实例名称中，用户可以轻松地识别它所提供的状态。

以下计数器实例名称通常适用于 `Service Fabric TStore` 类别的计数器：

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337_urn:MyReliableDictionary/dataStore`

在前面的示例中，`00d0126d-3e36-4d68-98da-cc4f7195d85e` 是 Service Fabric 分区 ID 的字符串表示形式，`131652217797162571` 是副本 ID，`142652217797162571` 是状态提供程序 ID，`1337` 是性能计数器实例区分符。 `urn:MyReliableDictionary/dataStore` 是存储名为 `urn:MyReliableDictionary`的集合的数据的状态提供程序的名称。

### <a name="transactional-replicator-performance-counters"></a>事务复制器性能计数器

Reliable Services 运行时发出的以下事件属于 `Service Fabric Transactional Replicator`类别

 计数器名称 | 说明 |
| --- | --- |
| 启动事务操作数/秒 | 每秒创建的新写入事务数。|
| 事务操作数/秒 | 每秒在 Reliable Collections 上执行的添加/更新/删除操作的数目。|
| 日志刷新字节数/秒 | 事务复制器每秒刷新到磁盘的字节数 |
| 中止的操作数/秒 | 事务复制器出于限制原因每秒拒绝的操作数。 |
| 平均事务毫秒/提交 | 每个事务的提交平均延时（毫秒） |
| 平均刷新延迟时间（毫秒） | 由事务复制器启动的磁盘刷新操作的平均持续时间（毫秒） |

### <a name="tstore-performance-counters"></a>TStore 性能计数器

Reliable Services 运行时发出的以下事件属于 `Service Fabric TStore`类别

 计数器名称 | 说明 |
| --- | --- |
| 项计数 | 存储中的项数。|
| 磁盘大小 | 存储检查点文件的磁盘总大小（以字节为单位）。|
| 检查点文件写入字节数/秒 | 最近检查点文件每秒写入的字节数。|
| 副本磁盘传输字节数/秒 | 在存储副本期间每秒（在主要副本上）读取或（在次要副本上）写入的磁盘字节数。|

## <a name="next-steps"></a>后续步骤
[PerfView 中的 EventSource 提供程序](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
