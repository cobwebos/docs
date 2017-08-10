---
title: "Azure Service Fabric 诊断和监视 | Microsoft Docs"
description: "本文描述了 Service Fabric Reliable ServiceRemoting 运行时的性能监视功能，例如由其发出的性能计数器。"
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: suchiagicha
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: suchiagicha
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: f54e157654fb15d2f7ff48ddc666c6c8803c75a2
ms.contentlocale: zh-cn
ms.lasthandoff: 08/01/2017

---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Reliable Service Remoting 的诊断和性能监视
Reliable ServiceRemoting 运行时发出[性能计数器](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx)。 这些有助于深入了解 ServiceRemoting 的运行状况以及进行故障排除和性能监视。


## <a name="performance-counters"></a>性能计数器
Reliable ServiceRemoting 运行时定义以下性能计数器类别：

| 类别 | 说明 |
| --- | --- |
| Service Fabric 服务 |特定于 Azure Service Fabric Service Remoting 的计数器，例如，处理请求所需的平均时间 |
| Service Fabric 服务方法 |特定于 Service Fabric Remoting Service 所实现方法的计数器，例如，调用服务方法的频率 |

以上每个类别都有一个或多个计数器。

Windows 操作系统中默认可用的 [Windows 性能监视器](https://technet.microsoft.com/library/cc749249.aspx)应用程序可用于收集和查看性能计数器数据。 [Azure 诊断](../cloud-services/cloud-services-dotnet-diagnostics.md)是另一种用于收集性能计数器数据并将其上传到 Azure 表的工具。

### <a name="performance-counter-instance-names"></a>性能计数器实例名称
包含大量 ServiceRemoting 服务或分区的群集具有大量性能计数器实例。 性能计数器实例名称有助于标识与性能计数器实例相关联的特定分区和服务方法（如果适用）。

#### <a name="service-fabric-service-category"></a>Service Fabric 服务类别
对于类别 `Service Fabric Service`，计数器实例名称采用以下格式：

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* 是与性能计数器实例相关联的 Service Fabric 分区 ID 的字符串表示。 分区 ID 是 GUID，并且其字符串表示是通过使用格式说明符“D”的 [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) 方法生成的。

ServiceReplicaOrInstanceId 是与性能计数器实例相关联的 Service Fabric 副本/实例 ID 的字符串表示形式。

ServiceRuntimeInternalID 是由 Fabric 服务运行时生成的供内部使用的 64 位整数的字符串表示形式。 这包括在性能计数器实例名称中，以确保其唯一性并避免与其他性能计数器实例名称发生冲突。 用户不应尝试解释此部分的性能计数器实例名称。

下面的示例展示了属于 `Service Fabric Service` 类别的计数器的计数器实例名称：

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

在前面的示例中，`2740af29-78aa-44bc-a20b-7e60fb783264` 是 Service Fabric 分区 ID 的字符串表示形式，`635650083799324046` 是副本/实例 ID 的字符串表示形式，`5008379932` 是运行时生成的供内部使用的 64 位 ID。

#### <a name="service-fabric-service-method-category"></a>Service Fabric 服务方法类别
对于类别 `Service Fabric Service Method`，计数器实例名称采用以下格式：

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

MethodName 是与性能计数器实例相关联的服务方法的名称。 方法名称的格式是根据 Fabric 服务运行时中的一些逻辑来确定的，该逻辑可以平衡名称的可读性和 Windows 上对性能计数器实例名称的最大长度的约束。

ServiceRuntimeMethodId 是由 Fabric 服务运行时生成的供内部使用的 32 位整数的字符串表示形式。 这包括在性能计数器实例名称中，以确保其唯一性并避免与其他性能计数器实例名称发生冲突。 用户不应尝试解释此部分的性能计数器实例名称。

*ServiceFabricPartitionID* 是与性能计数器实例相关联的 Service Fabric 分区 ID 的字符串表示。 分区 ID 是 GUID，并且其字符串表示是通过使用格式说明符“D”的 [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) 方法生成的。

ServiceReplicaOrInstanceId 是与性能计数器实例相关联的 Service Fabric 副本/实例 ID 的字符串表示形式。

ServiceRuntimeInternalID 是由 Fabric 服务运行时生成的供内部使用的 64 位整数的字符串表示形式。 这包括在性能计数器实例名称中，以确保其唯一性并避免与其他性能计数器实例名称发生冲突。 用户不应尝试解释此部分的性能计数器实例名称。

下面的示例展示了属于 `Service Fabric Service Method` 类别的计数器的计数器实例名称：

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

在前面的示例中，`ivoicemailboxservice.leavemessageasync` 是方法名称，`2` 是运行时生成的供内部使用的 32 位 ID，`89383d32-e57e-4a9b-a6ad-57c6792aa521` 是 Service Fabric 分区 ID 的字符串表示形式，`635650083804480486` 是 Service Fabric 副本/实例 ID 的字符串表示形式，`5008380` 是运行时生成的供内部使用的 64 位 ID。

## <a name="list-of-performance-counters"></a>性能计数器列表
### <a name="service-method-performance-counters"></a>服务方法性能计数器

Reliable Service 运行时发布与执行服务方法相关的下列性能计数器。

| 类别名称 | 计数器名称 | 说明 |
| --- | --- | --- |
| Service Fabric 服务方法 |调用/秒 |每秒调用服务方法的次数 |
| Service Fabric 服务方法 |每次调用的平均毫秒数 |执行服务方法所用的时间（以毫秒为单位） |
| Service Fabric 服务方法 |引发的异常数/秒 |服务方法每秒引发异常的次数 |

### <a name="service-request-processing-performance-counters"></a>服务请求处理的性能计数器
客户端通过服务代理对象调用方法时，会通过网络向远程服务发送请求消息。 该服务处理此请求消息并向客户端返回响应。 Reliable ServiceRemoting 运行时发布以下与服务请求处理相关的性能计数器。

| 类别名称 | 计数器名称 | 说明 |
| --- | --- | --- |
| Service Fabric 服务 |未完成的请求数 |正在服务中处理的请求数 |
| Service Fabric 服务 |每个请求的平均毫秒数 |服务处理请求所用时间（以毫秒为单位） |
| Service Fabric 服务 |反序列化请求的平均毫秒数 |服务收到服务请求消息时，对此请求消息进行反序列化所用的时间（以毫秒为单位） |
| Service Fabric 服务 |序列化响应的平均毫秒数 |将响应发送到客户端之前，在服务中序列化服务响应消息所用的时间（以毫秒为单位） |

## <a name="next-steps"></a>后续步骤
* [代码示例](https://github.com/Azure/servicefabric-samples)
* [PerfView 中的 EventSource 提供程序](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)

