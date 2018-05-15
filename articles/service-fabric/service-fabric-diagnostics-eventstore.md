---
title: Azure Service Fabric EventStore | Microsoft Docs
description: 了解 Azure Service Fabric 的 EventStore
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 7e401a310ce9a5f59473353227a9ce36767aac3c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="eventstore-service-overview"></a>事件存储服务概述

>[!NOTE]
>从 Service Fabric 版本 6.2 开始。 EventStore API 当前为预览版，且仅适用于在 Azure 上运行的 Windows 群集。 我们正在将此功能移植到 Linux 以及我们的独立群集。

## <a name="overview"></a>概述

EventStore 服务是在版本 6.2 中引入的，它是 Service Fabric 中的一个监视选项，提供了一种方法来了解群集或工作负荷在任意给定时间点的状态。 EventStore 服务通过可以调用的 API 来公开 Service Fabric 事件。 这些 EventStore API 允许直接查询群集来获取关于群集中的任何实体的诊断数据，并且应当用来帮助执行以下操作：
* 在开发或测试时或者当可能使用监视管道时对问题进行诊断
* 确认群集在正确处理你对群集执行的管理操作

若要查看 EventStore 中可用的事件的完整列表，请参阅 [Service Fabric 事件](service-fabric-diagnostics-event-generation-operational.md)。

可以向 EventStore 服务查询可用于群集中的每个实体和实体类型的事件。 这意味着可以在以下级别查询事件：
* 群集：所有群集级事件
* 多节点：所有节点级事件
* 节点：特定于一个节点的事件，基于 `nodeName`
* 多应用程序：所有应用程序级事件
* 应用程序：特定于一个应用程序的事件
* 多服务：来自群集中所有服务的事件
* 服务：来自特定服务的事件
* 多分区：来自所有分区的事件
* 分区：来自特定分区的事件
* 多副本：来自所有副本 / 实例的事件
* 副本：来自特定副本 / 实例的事件


EventStore 服务还能够将群集中的事件相关联。 通过查看在同一时间从可能已相互影响的不同实体写入的事件，EventStore 服务能够将这些事件进行关联来帮助查明群集中发生各项活动的原因。 例如，如果某个应用程序变得不正常且没有诱发任何变化，则 EventStore 将查看由平台公开的其他事件并且可能会将此情况与 `NodeDown` 事件相关联。 这有助于更快地进行故障检测和根本原因分析。

若要开始使用 EventStore 服务，请参阅[通过 EventStore API 查询群集事件](service-fabric-diagnostics-eventstore-query.md)。

## <a name="next-steps"></a>后续步骤
* Service Fabric 中的监视和诊断概述 - [Service Fabric 的监视和诊断](service-fabric-diagnostics-overview.md)
* 了解有关监视群集的详细信息 - [监视群集和平台](service-fabric-diagnostics-event-generation-infra.md)。