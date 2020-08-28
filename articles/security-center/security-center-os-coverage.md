---
title: Azure 安全中心支持的平台 |Microsoft Docs
description: 本文档列出了 Azure 安全中心支持的平台。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: f02e45804234fc8f8aa2b966db7ddce5efc9cfd1
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998826"
---
# <a name="supported-platforms"></a>支持的平台 

此页面显示 Azure 安全中心支持的平台和环境。

## <a name="combinations-of-environments"></a>环境组合 <a name="vm-server"></a>

Azure 安全中心支持不同类型的混合环境中的虚拟机和服务器：

* 仅限 Azure
* Azure 和本地
* Azure 和其他云
* Azure、其他云和本地

对于在 Azure 订阅上激活的 Azure 环境，Azure 安全中心会自动发现在该订阅中部署的 IaaS 资源。

## <a name="supported-operating-systems"></a>支持的操作系统

安全中心取决于 [Log Analytics 代理](../azure-monitor/platform/agents-overview.md#log-analytics-agent)。 请确保计算机正在运行此代理支持的操作系统之一，如以下页面所述：

* [适用于 Windows 的 Log Analytics 代理支持的操作系统](../azure-monitor/platform/agents-overview.md#supported-operating-systems)
* [适用于 Linux 的 Log Analytics 代理支持的操作系统](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

还要确保正确配置 Log Analytics 代理 [，以将数据发送到安全中心](security-center-enable-data-collection.md#manual-agent)

> [!TIP]
> 若要详细了解 Windows 和 Linux 上提供的特定安全中心功能，请参阅 [计算机的功能覆盖](security-center-services.md)。

## <a name="managed-virtual-machine-services"></a>托管的虚拟机服务 <a name="virtual-machine"></a>

还会在客户订阅中创建虚拟机作为某些 Azure 托管服务的一部分，例如 Azure Kubernetes (AKS) 、Azure Databricks 等。 安全中心还会发现这些虚拟机，并且可以在支持的操作系统可用时安装和配置 Log Analytics 代理。

## <a name="cloud-services"></a>云服务 <a name="cloud-services"></a>

此外，云服务中运行的虚拟机也受支持。 仅监视云服务 Web 和在生产槽运行的辅助角色。 若要了解有关云服务的详细信息，请参阅[云服务概述](../cloud-services/cloud-services-choose-me.md)。

还支持对驻留在 Azure Stack 中的 Vm 进行保护。 有关安全中心与 Azure Stack 的集成的详细信息，请参阅将 [Azure Stack 虚拟机加入到安全中心](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)。

## <a name="next-steps"></a>后续步骤

- 了解 [安全中心如何使用 Log Analytics 代理收集数据](security-center-enable-data-collection.md)。
- 了解[安全中心如何管理和保护数据](security-center-data-security.md)。
- 了解如何[规划并理解设计注意事项，以便采用 Azure 安全中心](security-center-planning-and-operations-guide.md)。