---
title: Azure 安全中心支持的平台 | Microsoft Docs
description: 本文档列出了 Azure 安全中心支持的平台。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 68cb738ae6e4689a0356ea56c1de2d383ea83ad6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91449941"
---
# <a name="supported-platforms"></a>支持的平台 

此页面显示 Azure 安全中心支持的平台和环境。

## <a name="combinations-of-environments"></a>环境组合 <a name="vm-server"></a>

Azure 安全中心支持基于不同类型的混合环境的虚拟机和服务器：

* 仅限 Azure
* Azure 和本地
* Azure 和其他云
* Azure、其他云和本地

对于在 Azure 订阅上激活的 Azure 环境，Azure 安全中心会自动发现在该订阅中部署的 IaaS 资源。

## <a name="supported-operating-systems"></a>支持的操作系统

安全中心依赖于 [Log Analytics 代理](../azure-monitor/platform/agents-overview.md#log-analytics-agent)。 请确保计算机正在运行此代理支持的操作系统之一，如以下页面所述：

* [适用于 Windows 的 Log Analytics 代理支持的操作系统](../azure-monitor/platform/agents-overview.md#supported-operating-systems)
* [适用于 Linux 的 Log Analytics 代理支持的操作系统](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

还要确保 Log Analytics 代理已[正确配置为将数据发送到安全中心](security-center-enable-data-collection.md#manual-agent)

> [!TIP]
> 若要详细了解 Windows 和 Linux 上提供的特定安全中心功能，请参阅[适用于计算机的功能覆盖范围](security-center-services.md)。

## <a name="managed-virtual-machine-services"></a>托管的虚拟机服务 <a name="virtual-machine"></a>

虚拟机也会作为某些 Azure 托管服务（比如 Azure Kubernetes (AKS)、Azure Databricks 等）的一部分在客户订阅中创建。 安全中心也会发现这些虚拟机，并且可以在支持的 OS 可用的情况下安装和配置 Log Analytics 代理。

## <a name="cloud-services"></a>云服务 <a name="cloud-services"></a>

此外，云服务中运行的虚拟机也受支持。 仅监视云服务 Web 和在生产槽运行的辅助角色。 若要了解有关云服务的详细信息，请参阅[云服务概述](../cloud-services/cloud-services-choose-me.md)。

还支持对驻留在 Azure Stack 中的 VM 进行保护。 若要详细了解安全中心与 Azure Stack 的集成，请参阅[将 Azure Stack 虚拟机加入安全中心](quickstart-onboard-machines.md)。 

## <a name="next-steps"></a>后续步骤

- 了解[安全中心如何使用 Log Analytics 代理收集数据](security-center-enable-data-collection.md)。
- 了解[安全中心如何管理和保护数据](security-center-data-security.md)。
- 了解如何[规划并理解设计注意事项，以便采用 Azure 安全中心](security-center-planning-and-operations-guide.md)。