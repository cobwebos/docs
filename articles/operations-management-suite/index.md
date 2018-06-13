---
title: Azure Operations Management Suite (OMS) 文档 - 教程 | Microsoft 文档
description: Microsoft Operations Management Suite (OMS) 是 Microsoft 基于云的 IT 管理解决方案，有助于管理和保护本地和云基础结构。 本文标识了 OMS 中包括的不同服务并提供了指向其详细内容的链接。
services: operations-management-suite
author: czeumault
manager: carolz
layout: LandingPage
ms.assetid: ''
ms.service: operations-management-suite
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing-page
ms.date: 01/23/2017
ms.author: carolz
ms.openlocfilehash: 12f959376d4923e4e2481e37108ade632ac14902
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23071205"
---
# <a name="what-is-operations-management-suite-oms"></a>什么是 Operations Management Suite (OMS)？
Microsoft Operations Management Suite (OMS) 是 Microsoft 基于云的 IT 管理解决方案，有助于管理和保护本地和云基础结构。  由于 OMS 作为基于云的服务实现，因此在基础结构服务上进行极低的投资即可快速使其启动并运行。  将自动交付新增功能，从而节省持续维护和升级成本。

除自行提供有价值的服务，OMS 还可与 System Center Operations Manager 等 System Center 组件集成，将现有管理投资扩展到云。  System Center 和 OMS 可协同工作来提供完整的混合管理体验。

以下部分提供 OMS 的各个值范围以及实现它们的服务的高级说明。  查看每个 OMS 组件的详细文档之前，可参考 OMS 体系结构以了解其各自的概况。

## <a name="insight-and-analyticsmediaoperations-management-suite-overviewicon-insight-analyticspng-insight-and-analytics"></a>![见解与分析](media/operations-management-suite-overview/icon-insight-analytics.png) 见解与分析
[日志分析](http://azure.microsoft.com/documentation/services/log-analytics)可帮助对操作系统和应用程序生成的日志和性能数据进行收集、关联、搜索和操作。 使用集成的搜索和自定义仪表板，轻松分析所有工作负载和服务器上的数百万记录，而无需考虑它们的物理位置，从而获得实时见解。表板，轻松分析所有工作负载和服务器上的数百万记录，而无需考虑它们的物理位置，从而获得实时操作见解。

可将解决方案轻松添加到日志分析，后者用于定义要收集的数据及其分析逻辑。  解决方案可以包括只需极少或无需配置即可自动交付到代理的附加功能。  除使用独立解决方案提供的分析工具之外，还可在整个数据集中执行自定义搜索，以便在系统和应用程序之间关联数据。  

## <a name="automation--controlmediaoperations-management-suite-overviewicon-automation-controlpng-automation--control"></a>![自动化与控制](media/operations-management-suite-overview/icon-automation-control.png) 自动化与控制
Azure 自动化通过基于 PowerShell 并在 Azure 云中运行的 [Runbook](../automation/automation-runbook-types.md) 自动执行管理流程。  Runbook 可以访问可通过 PowerShell 进行管理的任何产品或服务，包括 Amazon Web Services (AWS) 等其他云中的资源。  也可在本地数据中心内的服务器上运行 Runbook 以管理本地资源。

Azure 自动化通过 [PowerShell DSC](../automation/automation-dsc-overview.md) 提供配置管理。  可以创建和管理在 Azure 中托管的 DSC 资源，并将其应用到云和本地的系统，从而定义并自动实施其配置。

## <a name="protection-and-recoverymediaoperations-management-suite-overviewicon-protection-recoverypng-protection-and-disaster-recovery"></a>![保护和恢复](media/operations-management-suite-overview/icon-protection-recovery.png) 保护和灾难恢复
[Azure 备份](http://azure.microsoft.com/documentation/services/backup)可保护用程序数据，并且无需资本投资、只需最低的运行成本，即可将这些数据保留多年。  它可以备份物理和虚拟 Windows 服务器以及 SQL Server 和 SharePoint 等应用程序工作负荷中的数据。  System Center Data Protection Manager (DPM) 也可使用它将受保护的数据复制到 Azure，以实现冗余和长期存储。

[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) 有助于业务连续性和灾难恢复 (BCDR) 策略，因为它可以协调本地 Hyper-V 虚拟机、VMware 虚拟机和 Windows/Linux 物理服务器的复制故障转移和恢复。 可将计算机复制到辅助数据中心，或将其复制到 Azure 以扩展数据中心。 Site Recovery 还为工作负荷提供简单的故障转移和恢复。 它与 SQL Server AlwaysOn 等灾难恢复机制集成，提供恢复计划以便对多台计算机间分层的工作负荷实现轻松故障转移。

## <a name="oms-security-and-compliancemediaoperations-management-suite-overviewicon-security-compliancepng-security-and-compliance"></a>![OMS 安全与合规性](media/operations-management-suite-overview/icon-security-compliance.png) 安全与合规性
安全与合规性可帮助识别、评估和缓解基础结构的安全风险。  Log Analytics 中的多个解决方案可分析代理系统中的日志数据和配置，从而实现 OMS 的这些功能，帮助确保环境始终安全。

* [安全和审核解决方案](oms-security-getting-started.md)收集并分析托管系统上的安全事件以识别可疑活动。
* [反恶意软件解决方案](../log-analytics/log-analytics-malware.md)报告托管系统上的反恶意软件防护状态。  
* 系统更新解决方案对托管系统上的安全更新和其他更新进行分析，以便轻松识别需要修补的系统。

## <a name="next-steps"></a>后续步骤
* 了解 [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) 的相关信息。
* 了解 [Azure 自动化](../automation/automation-intro.md)的相关信息。
* 了解 [Azure 备份](http://azure.microsoft.com/documentation/services/backup)的相关信息。
* 了解 [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) 的相关信息。

