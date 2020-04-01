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
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: f9b948714f72ba02a100d9941721f073953bf22a
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473240"
---
# <a name="supported-platforms"></a>支持的平台 

## <a name="virtual-machines--servers"></a>虚拟机/服务器<a name="vm-server"></a>

安全中心支持不同类型的混合环境中的虚拟机/服务器：

* 仅 Azure
* Azure 和本地
* Azure 和其他云
* Azure、其他云和本地

对于在 Azure 订阅上激活的 Azure 环境，Azure 安全中心将自动发现在订阅中部署的 IaaS 资源。

> [!NOTE]
> 要接收全套安全功能，必须安装 Azure 安全中心使用的[日志分析代理](../azure-monitor/platform/agents-overview.md#log-analytics-agent)，并[正确配置该代理以将数据发送到 Azure 安全中心](security-center-enable-data-collection.md#manual-agent)。

以下各节列出了 Azure 安全中心使用的[日志分析代理](../azure-monitor/platform/agents-overview.md#log-analytics-agent)可以运行的支持服务器操作系统。

### <a name="windows-server-operating-systems"></a>Windows 服务器操作系统<a name="os-windows"></a>

|OS|Azure 安全中心支持|支持与微软后卫ATP集成|
|:---|:-:|:-:|
|Windows Server 2019|✔|X|
|Windows Server 2016|✔|✔|
|Windows Server 2012 R2|✔|✔|
|Windows Server 2008 R2|✔|✔|

要了解有关上面列出的 Windows 操作系统支持的功能，请参阅[虚拟机/服务器支持的功能](security-center-services.md#vm-server-features)。

### <a name="windows-operating-systems"></a>视窗操作系统<a name="os-windows (non-server)"></a>

Azure 安全中心与 Azure 服务集成，以监视和保护基于 Windows 的虚拟机。

### <a name="linux-operating-systems"></a>Linux 操作系统<a name="os-linux"></a>

64 位

* CentOS 6 和 7
* Amazon Linux 2017.09
* 甲骨文Linux 6和甲骨文Linux 7
* Red Hat Enterprise Linux Server 6 和 7
* Debian GNU/Linux 8 和 9
* Ubuntu Linux 14.04 LTS、16.04 LTS 和 18.04 LTS
* SUSE Linux Enterprise Server 12

32 位
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 和 9
* Ubuntu Linux 14.04 LTS 和 16.04 LTS

> [!NOTE]
> 由于支持的 Linux 操作系统列表在不断变化，如果您愿意，[请单击此处](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems)查看支持版本的最新列表，以防自本主题上次发布以来发生更改。

要了解有关上面列出的 Linux 操作系统支持的功能，请参阅[虚拟机/服务器支持的功能](security-center-services.md#vm-server-features)。

### <a name="managed-virtual-machine-services"></a>托管虚拟机服务<a name="virtual-machine"></a>

虚拟机也是在客户订阅中创建的，作为某些 Azure 托管服务的一部分，例如 Azure 库伯奈斯 （AKS）、Azure 数据块等。 这些虚拟机也由 Azure 安全中心发现，日志分析代理可以根据上面列出的支持的[Windows/Linux 操作系统](#os-windows)进行安装和配置。

### <a name="cloud-services"></a>云服务<a name="cloud-services"></a>

还支持在云服务中运行的虚拟机。 仅监视云服务 Web 和在生产槽运行的辅助角色。 若要了解有关云服务的详细信息，请参阅[云服务概述](../cloud-services/cloud-services-choose-me.md)。

还支持保护驻留在 Azure 堆栈中的虚拟机。 有关安全中心与 Azure 堆栈集成的详细信息，请参阅[将 Azure 堆栈虚拟机装到安全中心](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)。

## <a name="next-steps"></a>后续步骤

- 了解[安全中心如何收集数据和日志分析代理](security-center-enable-data-collection.md)。
- 了解[安全中心如何管理和保护数据](security-center-data-security.md)。
- 了解如何[规划和了解采用 Azure 安全中心的设计注意事项](security-center-planning-and-operations-guide.md)。
- 了解[可用于不同云环境的功能](security-center-services.md)。
- 详细了解[Azure 安全中心中 Windows 和 Linux 计算机的威胁保护](threat-protection.md#windows-machines)。
