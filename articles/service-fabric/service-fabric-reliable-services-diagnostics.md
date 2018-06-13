---
title: Azure Service Fabric 有状态 Reliable Services 的诊断功能 | Microsoft Docs
description: Azure Service Fabric 中的有状态 Reliable Services 的诊断功能
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 268ec61515f438fb7f98b6cef7a8ec60ba22e23f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212630"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>有状态 Reliable Services 的诊断功能
Azure Service Fabri 有状态 Reliable Services StatefulServiceBase 类会发出 [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 事件，这些事件可用于调试服务、提供对运行时运行方式的深入了解，以及帮助进行故障排除。

## <a name="eventsource-events"></a>EventSource 事件
有状态 Reliable Services StatefulServiceBase 类的 EventSource 名称是“Microsoft-ServiceFabric-Services”。 当[在 Visual Studio 中调试](service-fabric-debugging-your-application.md)服务时，来自此事件源的事件会显示在“[诊断事件](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio)”窗口中。

可帮助收集和/或查看 EventSource 事件的工具和技术的示例包括：[PerfView](http://www.microsoft.com/download/details.aspx?id=28567)、[Azure 诊断](../cloud-services/cloud-services-dotnet-diagnostics.md)和 [Microsoft TraceEvent 库](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)。

## <a name="events"></a>活动
| 事件名称 | 事件 ID | 级别 | 事件说明 |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |信息性 |在服务 RunAsync 任务启动时发出 |
| StatefulRunAsyncCancellation |2 |信息性 |在服务 RunAsync 任务取消时发出 |
| StatefulRunAsyncCompletion |3 |信息性 |在服务 RunAsync 任务完成时发出 |
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

Service Fabric 事务性复制器供[可靠状态管理器](service-fabric-reliable-services-reliable-collections-internals.md)用来在给定的[副本](service-fabric-concepts-replica-lifecycle.md)集内复制事务。 

Windows 操作系统中默认可用的 [Windows 性能监视器](https://technet.microsoft.com/library/cc749249.aspx)应用程序可用于收集和查看性能计数器数据。 [Azure 诊断](../cloud-services/cloud-services-dotnet-diagnostics.md)是另一种用于收集性能计数器数据并将其上传到 Azure 表的工具。

### <a name="performance-counter-instance-names"></a>性能计数器实例名称
具有大量 Reliable Services 或 Reliable Services 分区的群集将具有大量事务复制器性能计数器实例。 性能计数器实例名称有助于标识与性能计数器实例相关联的特定[分区](service-fabric-concepts-partitioning.md)和服务副本。

#### <a name="service-fabric-transactional-replicator-category"></a>Service Fabric 事务性复制器类别
对于类别 `Service Fabric Transactional Replicator`，计数器实例名称采用以下格式：

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* 是与性能计数器实例相关联的 Service Fabric 分区 ID 的字符串表示形式。 分区 ID 是 GUID，并且其字符串表示形式是通过使用格式说明符“D”的 [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) 生成的。

*ServiceFabricReplicaId* 是一种与给定的 Reliable Service 副本相关联的 ID。 副本 ID 包括在性能计数器实例名称中，以确保其唯一性并避免与其他性能计数器实例（由同一分区生成）发生冲突。 [此处](service-fabric-concepts-replica-lifecycle.md)提供有关副本及其在 Reliable Services 中的角色的更多详细信息。

以下计数器实例名称通常适用于 `Service Fabric Transactional Replicator` 类别的计数器：

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

在上述示例中，`00d0126d-3e36-4d68-98da-cc4f7195d85e` 是 Service Fabric 分区 ID 的字符串表示形式，`131652217797162571` 是副本 ID。

### <a name="transactional-replicator-performance-counters"></a>事务复制器性能计数器

Reliable Services 运行时发出的以下事件属于 `Service Fabric Transactional Replicator`类别

 计数器名称 | 说明 |
| --- | --- |
| 启动事务操作数/秒 | 每秒创建的新写入事务数。|
| 事务操作数/秒 | 每秒在 Reliable Collections 上执行的添加/更新/删除操作的数目。|
| 平均刷新延迟(毫秒) | 事务复制器每秒刷新到磁盘的字节数 |
| 中止的操作数/秒 | 事务复制器出于限制原因每秒拒绝的操作数。 |
| 平均事务用时(毫秒)/提交 | 每个事务的提交平均延时（毫秒） |
| 平均刷新延迟(毫秒) | 由事务复制器启动的磁盘刷新操作的平均持续时间（毫秒） |

## <a name="next-steps"></a>后续步骤
[PerfView 中的 EventSource 提供程序](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
