---
title: Azure 安全中心支持的平台 |微软文档
description: 本文档提供 Azure 安全中心支持的平台列表。
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
ms.openlocfilehash: 3c8bf69b745f5dba8c08556908df4d4ae5b5769f
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521921"
---
# <a name="supported-platforms"></a>支持的平台 

此页显示 Azure 安全中心支持的平台和环境。

## <a name="combinations-of-environments"></a>环境组合<a name="vm-server"></a>

Azure 安全中心支持不同类型的混合环境中的虚拟机和服务器：

* 仅 Azure
* Azure 和本地
* Azure 和其他云
* Azure、其他云和本地

对于在 Azure 订阅上激活的 Azure 环境，Azure 安全中心将自动发现在订阅中部署的 IaaS 资源。

## <a name="supported-operating-systems"></a>支持的操作系统

安全中心取决于[日志分析代理](../azure-monitor/platform/agents-overview.md#log-analytics-agent)。 确保您的计算机运行此代理受支持的操作系统之一，如下页所述：

* [适用于 Windows 支持的操作系统的日志分析代理](../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)
* [Linux 支持的操作系统的日志分析代理](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)

还确保您的日志分析代理已[正确配置为将数据发送到安全中心](security-center-enable-data-collection.md#manual-agent)

> [!TIP]
> 要了解有关 Windows 和 Linux 上提供的特定安全中心功能的更多信息，请参阅[计算机的功能覆盖范围](security-center-services.md)。

## <a name="managed-virtual-machine-services"></a>托管虚拟机服务<a name="virtual-machine"></a>

虚拟机也是在客户订阅中创建的，作为某些 Azure 托管服务的一部分，例如 Azure 库伯奈斯 （AKS）、Azure 数据块等。 安全中心也会发现这些虚拟机，如果支持的操作系统可用，则可以安装和配置日志分析代理。

## <a name="cloud-services"></a>云服务<a name="cloud-services"></a>

还支持在云服务中运行的虚拟机。 仅监视云服务 Web 和在生产槽运行的辅助角色。 若要了解有关云服务的详细信息，请参阅[云服务概述](../cloud-services/cloud-services-choose-me.md)。

还支持保护驻留在 Azure 堆栈中的 VM。 有关安全中心与 Azure 堆栈集成的详细信息，请参阅[将 Azure 堆栈虚拟机装到安全中心](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)。

## <a name="next-steps"></a>后续步骤

- 了解[安全中心如何使用日志分析代理收集数据](security-center-enable-data-collection.md)。
- 了解[安全中心如何管理和保护数据](security-center-data-security.md)。
- 了解如何[规划和了解采用 Azure 安全中心的设计注意事项](security-center-planning-and-operations-guide.md)。