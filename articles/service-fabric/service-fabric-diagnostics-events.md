---
title: Azure Service Fabric 事件 | Microsoft Docs
description: 了解所提供的可直接用来帮助监视 Azure Service Fabric 群集的 Service Fabric 事件。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 6379f463d5c2e9027e1d8d4dd90db61cb72cdd44
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="service-fabric-events"></a>Service Fabric 事件 

Service Fabric 平台会为群集内发生的关键操作活动写入多个结构化的事件。 从群集升级到副本放置决策都属于此类事件。 Service Fabric 公开的每个事件都映射到群集中的下列实体之一：
* 群集
* 应用程序
* 服务
* 分区
* 副本 
* 容器

若要查看平台公开的事件的完整列表，请参阅 [Service Fabric 事件列表](service-fabric-diagnostics-event-generation-operational.md)。

下面是群集中你应当查看其事件的一些重要场景的示例。 
1. 节点生命周期事件：当节点启动、发生故障、被激活/被停用或重新启动时，将会公开事件来指明发生了什么，并帮助查明是计算机本身发生错误还是通过 SF 调用了 API 来修改节点状态。
1. 群集升级：当群集升级时（SF 版本或配置更改），将会看到升级启动、滚动通过每个 UD 以及完成（或回滚）。 
1. 应用程序升级：与群集升级类似，当升级滚动完成时会生成一套完整的事件。 这些事件可用来了解升级是何时计划的、升级的当前状态以及整个事件序列。 这有助于回看哪些升级已成功实施。
1. 应用程序/服务部署/删除：针对创建或删除的每个应用程序、服务和容器都存在对应的事件。
1. 分区移动（重新配置）：每当有状态分区经历重新配置时（副本集发生更改），都会记录一个事件。 如果尝试了解分区副本集的更改频率或者在任意时间点跟踪哪个节点在运行主副本，则这非常有用。
1. Chaos 事件：当使用 Service Fabric 的 [Chaos](service-fabric-controlled-chaos.md) 服务时，每次启动或停止该服务时或者当该服务在系统中导致错误时，都会看到事件。
1. 运行状况事件：每次创建了“警告”或“错误”运行状况报告时，或者当实体恢复为“正常”运行状况状态时，或者当运行状况报告过期时，Service Fabric 都会公开运行状况事件。 这些事件对于跟踪实体的历史运行状况统计信息非常有帮助。 

## <a name="how-to-access-events"></a>如何访问事件

可以通过以下几种不同的方式访问 Service Fabric 事件：
* 通过操作通道。 可以通过 Azure 诊断扩展收集这些事件并将其发送到存储表以便使用或引入到诸如 OMS Log Analytics 的工具中。 为群集启用“诊断”后，Azure 诊断代理便会部署在群集上，并默认配置为通过操作通道读取日志。 详细了解如何配置 [Azure 诊断代理](service-fabric-diagnostics-event-aggregation-wad.md)，以便修改群集的诊断配置，从而获取更多日志或性能计数器。 
* 通过 EventStore 服务的 Rest API，这允许直接查询群集；或者通过 Service Fabric 客户端库。 请参阅[通过 EventStore API 查询群集事件](service-fabric-diagnostics-eventstore-query.md)。

## <a name="next-steps"></a>后续步骤
* 了解有关监视群集的详细信息 - [监视群集和平台](service-fabric-diagnostics-event-generation-infra.md)。
* 了解有关 EventStore 服务的详细信息 - [EventStore 服务概述](service-fabric-diagnostics-eventstore.md)
