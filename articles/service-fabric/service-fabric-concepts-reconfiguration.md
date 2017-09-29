---
title: "Azure Service Fabric 中的重新配置 | Microsoft Docs"
description: "了解服务结构中的分区重新配置"
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: 
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2017
ms.author: aprameyr
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 39f9bec12d6fde1576f283ac80f72e62581efc9c
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="reconfiguration-in-azure-service-fabric"></a>Azure Service Fabric 中的重新配置
*配置*定义为有状态服务的分区的副本及其角色。

*重新配置*是将一个*配置*移动到另一个*配置*的过程。 该过程将对有状态服务的分区的副本集进行更改。 旧配置称为*以前的配置 (PC)*，而新配置称为*当前配置 (CC)*。 服务结构中的重新配置协议可保持一致性，并在对副本集进行任何更改期间保持可用性。

故障转移管理器启动重新配置以响应系统中的不同事件。 例如，如果主要副本发生故障，重新配置会启动以将活动的次要副本升级为主要副本。 另一个示例是在需要将主要副本移至另一节点来升级节点时响应应用程序升级。

## <a name="reconfiguration-types"></a>重新配置类型
重新配置可以分为以下两种类型：

- 主要副本发生改变的重新配置
    - **故障转移**：故障转移是响应正在运行的主要副本故障的重新配置
    - **交换主要副本**：交换是服务结构需要将正在运行的主要副本从一个节点移至另一节点时的重新配置，通常为了响应负载均衡或升级等。

- 主要副本未发生改变的重新配置

## <a name="reconfiguration-phases"></a>重新配置阶段
重新配置按以下几个阶段进行：

- **阶段 0**：此阶段在交换主要副本重新配置时执行，即当前主要副本将其状态传输到新的主要副本并转换为活动的次要副本。

- **阶段 1**：此阶段在主要副本发生改变的重新配置期间执行。 在此阶段，服务结构将识别当前副本中哪一个是正确的主要副本。 在交换主要副本重新配置期间无需执行此阶段，因为已经选择了新的主要副本。 

- **阶段 2**：在此阶段，服务结构确保所有数据可用于当前配置的大多数副本。

其他几个阶段仅供内部使用。

## <a name="stuck-reconfigurations"></a>重新配置已停止
重新配置*停止*的可能原因有很多种。 一些常见原因包括：

- **关闭副本**：某些重新配置阶段需要配置中的大多数副本处于启动状态
- **网络或通信问题**：重新配置需要不同节点之间的网络连接性
- **API 失败**：重新配置协议要求服务实施完成某些 API。 例如：不遵守可靠服务中的取消令牌会导致停止对交换主要副本的重新配置

来自系统组件（如 System.FM、System.RA、System.RAP）的健康状况报表可用于诊断重新配置停止的位置。 [系统健康状况报表页](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)将介绍这些健康状况报表。

## <a name="next-steps"></a>后续步骤
有关 Service Fabric 概念的详细信息，请参阅以下文章：

- [Reliable Services 生命周期 - C#](service-fabric-reliable-services-lifecycle.md)
- [系统健康状况报表](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [副本和实例](service-fabric-concepts-replica-lifecycle.md)
