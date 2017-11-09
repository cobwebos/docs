---
title: "Operations Management Suite (OMS) 体系结构 | Microsoft Docs"
description: "Microsoft Operations Management Suite (OMS) 是 Microsoft 基于云的 IT 管理解决方案，有助于管理和保护本地和云基础结构。  本文标识了 OMS 中包括的不同服务并提供了指向其详细内容的链接。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 40e41686-7e35-4d85-bbe8-edbcb295a534
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 76f69946724b5297b1f9a1f715819c69c4a4a51d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="oms-architecture"></a>OMS 体系结构
[Operations Management Suite (OMS)](https://azure.microsoft.com/documentation/services/operations-management-suite/) 是一套基于云的服务，用于管理本地和云环境。  本文介绍了 OMS 不同的本地和云组件及其高级云计算体系结构。  可参考各项服务的文档进一步了解详细信息。

## <a name="log-analytics"></a>Log Analytics
[Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) 收集的所有数据存储在 Azure 中托管的 OMS 存储库。  连接的源将生成的数据收集到 OMS 存储库。  目前，有三种受支持的连接的源。

* 直接连接到 OMS 的 [Windows](../log-analytics/log-analytics-windows-agents.md) 或 [Linux](../log-analytics/log-analytics-linux-agents.md) 计算机上安装的代理。
* [连接到 Log Analytics](../log-analytics/log-analytics-om-agents.md) 的 System Center Operations Manager (SCOM) 管理组。  SCOM 代理继续与管理服务器进行通信，该服务器将事件和性能数据转发到 Log Analytics。
* 从 Azure 辅助角色、Web 角色或虚拟机收集 [Azure 诊断](../cloud-services/cloud-services-dotnet-diagnostics.md)数据的 [Azure 存储帐户](../log-analytics/log-analytics-azure-storage.md)。

数据源定义 Log Analytics 从连接的源（包括事件日志和性能计数器）所收集的数据。  解决方案为 OMS 增添了功能，可从 [OMS 解决方案库](../log-analytics/log-analytics-add-solutions.md)轻松添加到工作区。  一些解决方案可能需要从 SCOM 代理直接连接到 Log Analytics，而另一些可能需要安装其他代理。

Log Analytics 具有基于 Web 的门户，可用于管理 OMS 资源、添加和配置 OMS 解决方案、查看和分析 OMS 存储库中的数据。

![Log Analytics 高级体系结构](media/operations-management-suite-architecture/log-analytics.png)

## <a name="azure-automation"></a>Azure 自动化
[Azure 自动化 Runbook](http://azure.microsoft.com/documentation/services/automation) 在 Azure 云中实现执行，可访问位于 Azure 和其他云服务中的资源，或者可从公共 Internet 访问的资源。  还可使用[混合 Runbook 辅助角色](../automation/automation-hybrid-runbook-worker.md)指定本地数据中心的本地计算机，以便 Runbook 可以访问本地资源。

存储在 Azure 自动化中的 [DSC 配置](../automation/automation-dsc-overview.md)可直接应用于 Azure 虚拟机。  其他物理计算机和虚拟机可从 Azure 自动化 DSC 拉取服务器请求配置。

Azure 自动化具有这样一个 OMS 解决方案，可显示统计信息和链接来启动 Azure 门户进行任何操作。

![Azure 自动化高级体系结构](media/operations-management-suite-architecture/automation.png)

## <a name="azure-backup"></a>Azure 备份
[Azure 备份](http://azure.microsoft.com/documentation/services/backup)中的受保护数据存储在位于特定地理区域的备份保管库。  数据在同一区域内复制，并且根据保管库类型，可能还会复制到其他区域以进一步实现冗余。

Azure 备份有三种基本方案。

* Windows 计算机与 Azure 备份代理。  这允许将文件和文件夹从任何 Windows 服务器或客户端直接备份到 Azure 备份保管库。  
* System Center Data Protection Manager (DPM) 或 Microsoft Azure 备份服务器。 这允许利用 DPM 或 Microsoft Azure 备份服务器将文件和文件夹以及 SQL 和 SharePoint 等应用程序工作负荷备份到本地存储器，再复制到 Azure 备份保管库。
* Azure 虚拟机扩展。  这允许将 Azure 虚拟机备份到 Azure 备份保管库。

Azure 备份具有这样一个 OMS 解决方案，可显示统计信息和链接来启动 Azure 门户进行任何操作。

![Azure 备份高级体系结构](media/operations-management-suite-architecture/backup.png)

## <a name="azure-site-recovery"></a>Azure Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) 可以安排虚拟机和物理服务器的复制、故障转移与恢复。 复制数据在 Hyper-V 主机、VMware Hypervisor 及主要和辅助数据中心内的物理服务器之间交换，或者在数据中心与 Azure 存储之间交换。  Site Recovery 将元数据存储在位于特定 Azure 地理区域的保管库。 复制的数据不由 Site Recovery 服务存储。

Azure Site Recovery 有三种基本复制方案。

**复制 Hyper-V 虚拟机**

* 如果 Hyper-V 虚拟机在 VMM 云中托管，则可复制到辅助数据中心或 Azure 存储。  通过安全的 Internet 连接复制到 Azure。  通过 LAN 复制到辅助数据中心。
* 如果 Hyper-V 虚拟机不由 VMM 托管，则只能复制到 Azure 存储。  通过安全的 Internet 连接复制到 Azure。

**复制 VMWare 虚拟机**

* 可将 VMware 虚拟机复制到运行 VMware 的辅助数据中心或复制到 Azure 存储。  可通过站点到站点 VPN 或 Azure ExpressRoute 或安全的 Internet 连接复制到 Azure。 通过 InMage Scout 数据通道复制到辅助数据中心。

**复制物理 Windows 和 Linux 服务器** 

* 可将物理服务器复制到辅助数据中心或 Azure 存储。 可通过站点到站点 VPN 或 Azure ExpressRoute 或安全的 Internet 连接复制到 Azure。 通过 InMage Scout 数据通道复制到辅助数据中心。  Azure Site Recovery 具有的 OMS 解决方案可显示一些统计信息，但必须使用 Azure 门户进行任何操作。

![Azure Site Recovery 高级体系结构](media/operations-management-suite-architecture/site-recovery.png)

## <a name="next-steps"></a>后续步骤
* 了解 [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) 的相关信息。
* 了解 [Azure 自动化](https://azure.microsoft.com/documentation/services/automation)的相关信息。
* 了解 [Azure 备份](http://azure.microsoft.com/documentation/services/backup)的相关信息。
* 了解 [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) 的相关信息。

