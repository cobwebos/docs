---
title: Azure Service Fabric 中的重新配置 | Microsoft Docs
description: 了解 Service Fabric 中的分区重新配置
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: ''
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: a24aa6aa1695a3d1166816b7960bdd7b551e1a37
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60882191"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Azure Service Fabric 中的重新配置
*配置*定义为有状态服务的分区的副本及其角色。

重新配置  是将一个配置迁移为另一个配置的过程。 这会更改有状态服务的分区副本集。 旧配置称为“先前配置 (PC)”  ，新配置称为“当前配置 (CC)”  。 Azure Service Fabric 中的重新配置协议可以在对副本集进行任何更改期间，保持一致性和可用性。

为了应对系统中的不同事件，故障转移管理器会启动重新配置。 例如，如果主要副本发生故障，重新配置会启动以将活动的次要副本升级为主要副本。 再例如，在应用程序升级期间，可能有必要将主要副本移至另一节点，从而升级节点。

## <a name="reconfiguration-types"></a>重新配置类型
重新配置可以分为以下两种类型：

- 主要副本发生改变的重新配置：
    - **故障转移**：故障转移是响应故障的正在运行的主要重新配置。
    - **交换主要副本**:交换为主要副本重新配置 Service Fabric 需要移动正在运行的主要从一个节点到另一个，通常在响应负载均衡或升级。

- 主要副本未发生改变的重新配置。

## <a name="reconfiguration-phases"></a>重新配置阶段
重新配置按以下几个阶段进行：

- **阶段 0**:此阶段发生在交换主要重新配置当前主副本将其状态传输到新的主副本并转换为活动辅助数据库。

- **阶段 1**:此阶段发生改变的重新配置期间发生。 在此阶段，Service Fabric 在当前副本中确定正确的主要副本。 在交换主要副本的重新配置期间，无需执行此阶段，因为已经选择了新的主要副本。 

- **Phase2**:在此阶段，Service Fabric 可确保所有数据现都已推出的大多数副本的当前配置。

其他几个阶段仅供内部使用。

## <a name="stuck-reconfigurations"></a>重新配置已停止
重新配置无法执行  的原因有很多种。 一些常见原因包括：

- **关闭副本**:某些重新配置阶段要求的大多数副本在配置中能够正常运行。
- **网络或通信问题**:重新配置要求在不同节点之间的网络连接。
- **API 故障**:重新配置协议要求服务实现完成特定的 Api。 例如，不履行可靠服务中的取消令牌会导致交换主要副本的重新配置无法运行。

使用系统组件（如 System.FM、System.RA、System.RAP）提供的运行状况报告，诊断在哪里无法运行重新配置。 [系统健康状况报表页](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)将介绍这些健康状况报表。

## <a name="next-steps"></a>后续步骤
有关 Service Fabric 概念的详细信息，请参阅以下文章：

- [Reliable Services 生命周期 - C#](service-fabric-reliable-services-lifecycle.md)
- [系统运行状况报告](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [副本和实例](service-fabric-concepts-replica-lifecycle.md)
