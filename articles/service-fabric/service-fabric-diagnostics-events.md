---
title: Azure Service Fabric 事件 | Microsoft Docs
description: 了解所提供的可直接用来帮助监视 Azure Service Fabric 群集的 Service Fabric 事件。
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: c4ce8e01b1dc819453610f68d044cc268e27eed7
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242745"
---
# <a name="service-fabric-events"></a>Service Fabric 事件 

Service Fabric 平台会为群集内发生的关键操作活动写入多个结构化的事件。 从群集升级到副本放置决策都属于此类事件。 Service Fabric 公开的每个事件都映射到群集中的下列实体之一：
* 群集
* 应用程序
* 服务
* Partition
* 副本 
* 容器

若要查看平台公开的事件的完整列表，请参阅 [Service Fabric 事件列表](service-fabric-diagnostics-event-generation-operational.md)。

下面是群集中你应会看到其事件的一些场景的示例。 
* 节点生命周期事件：当节点启动、发生故障、缩小/扩大、重启、被激活/被停用时，这些事件将会公开以显示发生了什么情况，并帮助查明是计算机本身发生故障还是存在通过 SF 调用 API 来修改节点状态的 API。
* 群集升级：当群集升级时（SF 版本或配置更改），将会看到升级启动、滚动通过每个升级域以及完成（或回滚）。 
* 应用程序升级：与群集升级类似，当升级滚动完成时会生成一套完整的事件。 这些事件可用来了解升级是何时计划的、升级的当前状态以及整个事件序列。 这有助于回看哪些升级已成功实施或是否触发了回滚。
* 应用程序/服务部署/删除：针对创建或删除的每个应用程序、服务和容器都存在对应的事件，并且在缩小或扩大（例如，增加副本的数量）时非常有用
* 分区移动（重新配置）：每当有状态分区经历重新配置时（副本集发生更改），都会记录一个事件。 如果尝试了解分区副本集更改或故障转移的频率，或者在任意时间点跟踪正在运行主副本的节点，则这非常有用。
* Chaos 事件：当使用 Service Fabric 的 [Chaos](service-fabric-controlled-chaos.md) 服务时，每次启动或停止该服务时或者当该服务在系统中导致错误时，都会看到事件。
* 运行状况事件：每次创建了“警告”或“错误”运行状况报告时，或者当实体恢复为“正常”运行状况状态时，或者当运行状况报告过期时，Service Fabric 都会公开运行状况事件。 这些事件对于跟踪实体的历史运行状况统计信息非常有帮助。 

## <a name="how-to-access-events"></a>如何访问事件

可以通过以下几种不同的方式访问 Service Fabric 事件：
* 事件通过标准频道如 ETW/Windows 事件日志记录和可视化，方法是支持这些例如 Azure Monitor 日志的任何监视工具。 默认情况下，在门户中创建的群集已打开的诊断和具有 Windows Azure 诊断代理将事件发送到 Azure 表存储，但仍需要将此集成与 log analytics 资源。 详细了解如何配置[Azure 诊断代理](service-fabric-diagnostics-event-aggregation-wad.md)修改诊断配置的群集以获取更多日志或性能计数器和[Azure Monitor 日志集成](service-fabric-diagnostics-event-analysis-oms.md)
* EventStore 服务的 Rest API 允许直接查询群集或通过 Service Fabric 客户端库进行查询。 请参阅[通过 EventStore API 查询群集事件](service-fabric-diagnostics-eventstore-query.md)。

## <a name="next-steps"></a>后续步骤
* 了解有关监视群集的详细信息 - [监视群集和平台](service-fabric-diagnostics-event-generation-infra.md)。
* 了解有关 EventStore 服务的详细信息 - [EventStore 服务概述](service-fabric-diagnostics-eventstore.md)
