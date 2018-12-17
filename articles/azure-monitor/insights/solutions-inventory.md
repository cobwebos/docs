---
title: Azure 中的管理解决方案的数据收集详细信息 | Microsoft Docs
description: Azure 中的管理解决方案是逻辑、可视化效果和数据采集规则的集合，提供围绕特定问题领域制定的指标。  本文提供了 Microsoft 提供的管理解决方案的列表以及有关其数据收集方法和频率的详细信息。
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/26/2018
ms.author: bwren
ms.openlocfilehash: 14f373b5459aaa7a9e51ba284f6a30094c4e7617
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53194175"
---
# <a name="data-collection-details-for-management-solutions-in-azure"></a>Azure 中的管理解决方案的数据收集详细信息
本文包括了 Microsoft 提供的[管理解决方案](solutions.md)的列表以及指向其详细文档的链接。  它还提供了这些解决方案将数据收集到 Log Analytics 中时采用的方法和频率的相关信息。  可以使用本文中的信息来了解可用的各种解决方案，并了解各种管理解决方案的数据流和连接要求。 

## <a name="list-of-management-solutions"></a>管理解决方案的列表

下表列出了 Azure 中由 Microsoft 提供的[管理解决方案](solutions.md)的列表。 列中的条目意味着解决方案使用该方法将数据收集到 Log Analytics 中。  如果解决方案没有选择任何列，则它将从另一个 Azure 服务直接写入到 Log Analytics 中。 访问每个解决方案的链接可以查看其详细文档来了解更多信息。

各个列的说明如下：

- **Microsoft Monitoring Agent** - Windows 和 Linux 上用来运行来自 SCOM 的管理包和来自 Azure 的管理解决方案的代理。 在此配置中，代理直接连接到 Log Analytics，无需连接到 Operations Manager 管理组。 
- **Operations Manager** - 与 Microsoft Monitoring Agent 相同的代理。 在此配置中，它[连接到已连接到 Log Analytics 的 Operations Manager 管理组](../../azure-monitor/platform/om-agents.md)。 
-  **Azure 存储** - 此解决方案从 Azure 存储帐户收集数据。 
- **是否需要 Operations Manager？** - 管理解决方案进行数据收集时需要一个已连接的 Operations Manager 管理组。 
- **通过管理组发送 Operations Manager 代理数据** - 如果代理[连接到 SCOM 管理组](../../azure-monitor/platform/om-agents.md)，则数据将从管理服务器发送到 Log Analytics。 在这种情况下，代理不需要直接连接到 Log Analytics。 如果未选中此框，则数据将从代理直接发送到 Log Analytics，即使代理已连接到一个 SCOM 管理组。 它还需要能够通过 [Log Analytics 网关](../../azure-monitor/platform/gateway.md)与 Log Analytics 进行通信。
- **收集频率** - 指定管理解决方案收集数据时采用的频率。 



| **管理解决方案** | 平台 | **Microsoft Monitoring Agent** | **Operations Manager 代理** | **Azure 存储** | **是否需要 Operations Manager？** | **通过管理组发送 Operations Manager 代理数据** | **收集频率** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [活动 Log Analytics](../../azure-monitor/platform/collect-activity-logs.md) | Azure | | | | | | 通知时 |
| [AD 评估](../../azure-monitor/insights/ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 天 |
| [AD 复制状态](../../azure-monitor/insights/ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 天 |
| [代理运行状况](solution-agenthealth.md) | Windows 和 Linux | &#8226; | &#8226; | | | &#8226; | 1 分钟 |
| [警报管理](../../azure-monitor/platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |到达时 |
| [警报管理](../../azure-monitor/platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 分钟 |
| [警报管理](../../azure-monitor/platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 分钟 |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | 不适用 |
| [Application Insights 连接器（预览版）](../../azure-monitor/platform/app-insights-connector.md) | Azure | | | |  |  | 通知时 |
| [自动化混合辅助角色](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | 不适用 |
| [Azure 应用程序网关分析](../../azure-monitor/insights/azure-networking-analytics.md) | Azure |  |  |  |  |  | 通知时 |
| **管理解决方案** | 平台 | **Microsoft Monitoring Agent** | **Operations Manager 代理** | **Azure 存储** | **是否需要 Operations Manager？** | **通过管理组发送 Operations Manager 代理数据** | **收集频率** |
| [Azure 网络安全组分析（已弃用）](../../azure-monitor/insights/azure-networking-analytics.md) | Azure |  |  |  |  |  | 通知时 |
| [Azure SQL Analytics（预览版）](../../azure-monitor/insights/azure-sql.md) | Windows | | | | | | 1 分钟 |
| [备份](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | 通知时 |
| [容量和性能（预览版）](../../azure-monitor/insights/capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |到达时 |
| [更改跟踪](../../automation/automation-change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |每小时 |
| [更改跟踪](../../automation/automation-change-tracking.md) |Linux |&#8226; | | | | |每小时 |
| [容器](../../azure-monitor/insights/containers.md) | Windows 和 Linux | &#8226; | &#8226; |  |  |  | 3 分钟 |
| [密钥保管库分析](../../azure-monitor/insights/azure-key-vault.md) |Windows | | | | | |通知时 |
| [恶意软件评估](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |每小时 |
| [网络性能监视器](../../azure-monitor/insights/network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | 每隔 5 秒钟进行 TCP 握手，每隔 3 分钟发送数据 |
| [Office 365 分析（预览版）](solution-office-365.md) |Windows | | | | | |通知时 |
| **管理解决方案** | 平台 | **Microsoft Monitoring Agent** | **Operations Manager 代理** | **Azure 存储** | **是否需要 Operations Manager？** | **通过管理组发送 Operations Manager 代理数据** | **收集频率** |
| [Service Fabric 分析](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 分钟 |
| [服务地图](../../azure-monitor/insights/service-map.md) | Windows 和 Linux | &#8226; | &#8226; |  |  |  | 15 秒 |
| [SQL 评估](../../azure-monitor/insights/sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 天 |
| [SurfaceHub](../../azure-monitor/insights/surface-hubs.md) |Windows |&#8226; | | | | |到达时 |
| [System Center Operations Manager 评估（预览版）](../../azure-monitor/insights/scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | 七天 |
| [更新管理](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |每天和安装更新后的 15 分钟内至少 2 次 |
| [升级准备情况](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 天 |
| [VMware 监视（已弃用）](../../azure-monitor/insights/vmware.md) | Linux | &#8226; |  |  |  |  | 3 分钟 |
| [Wire Data 2.0（预览版）](../../azure-monitor/insights/wire-data.md) |Windows（2012 R2 / 8.1 或更高版本） |&#8226; |&#8226; | | | | 1 分钟 |




## <a name="next-steps"></a>后续步骤
* 了解如何[创建查询](../../azure-monitor/log-query/log-query-overview.md)来分析管理解决方案收集的数据。
