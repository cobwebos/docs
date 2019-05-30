---
title: 监视 Azure 中的解决方案的清单 |Microsoft Docs
description: Azure Monitor 中的监视解决方案是逻辑、可视化效果和数据采集规则的集合，提供围绕特定问题领域制定的指标。  本文提供了一系列监视解决方案可从 Microsoft 和有关其方法和数据收集的频率的详细信息。
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
ms.openlocfilehash: 9398815ea75c0eacd99a6e40c569254fac671cbb
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234017"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>用于监视 Azure 中的解决方案的清单和数据收集详细信息
[监视解决方案](solutions.md)利用 Azure 提供的特定应用程序或服务操作的其他见解中的服务。 监视解决方案通常收集日志数据并提供查询和视图，用于分析收集的数据。 可以在 Azure Monitor 中针对你使用的任何应用程序和服务添加监视解决方案。 这些解决方案是免费提供的，但收集数据可能会产生使用费。

本文包含一系列[监视解决方案](solutions.md)可从 Microsoft 获得与链接到其详细文档。  它还提供了这些解决方案将数据收集到 Azure Monitor 中时采用的方法和频率的相关信息。  可以使用本文中的信息来确定可用的各种解决方案以及了解用于不同的监视解决方案的数据流动和连接要求。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>监视解决方案的列表

下表列出[监视解决方案](solutions.md)由 Microsoft 提供的 Azure 中。 列中的条目意味着解决方案使用该方法将数据收集到 Azure Monitor 中。  如果解决方案没有选择任何列，则它将从另一个 Azure 服务直接写入到 Azure Monitor 中。 访问每个解决方案的链接可以查看其详细文档来了解更多信息。

各个列的说明如下：

- **Microsoft 监视代理**-代理使用 Windows 和 Linux 上从 SCOM 运行管理包和监视从 Azure 的解决方案。 在此配置中，代理直接连接到 Azure Monitor，无需连接到 Operations Manager 管理组。 
- **Operations Manager** - 与 Microsoft Monitoring Agent 相同的代理。 在此配置中，它[连接到已连接到 Azure Monitor 的 Operations Manager 管理组](../platform/om-agents.md)。 
-  **Azure 存储** - 此解决方案从 Azure 存储帐户收集数据。 
- **是否需要 Operations Manager？** -A 连接的 Operations Manager 管理组是必需的数据收集的监视解决方案。 
- **通过管理组发送 Operations Manager 代理数据** - 如果代理[连接到 SCOM 管理组](../platform/om-agents.md)，则数据将从管理服务器发送到 Azure Monitor。 在这种情况下，代理不需要直接连接到 Azure Monitor。 如果未选中此框，则数据将从代理直接发送到 Azure Monitor，即使代理已连接到一个 SCOM 管理组。 它还需要能够通过 [Log Analytics 网关](../platform/gateway.md)与 Azure Monitor 进行通信。
- **收集频率**-指定的频率收集数据的监视解决方案。 



| **监视解决方案** | 平台  | **Microsoft Monitoring Agent** | **Operations Manager 代理** | **Azure 存储** | **是否需要 Operations Manager？** | **通过管理组发送 Operations Manager 代理数据** | **收集频率** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [活动日志分析](../platform/activity-log-collect.md) | Azure | | | | | | 通知时 |
| [AD 评估](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 天 |
| [AD 复制状态](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 天 |
| [代理运行状况](solution-agenthealth.md) | Windows 和 Linux | &#8226; | &#8226; | | | &#8226; | 1 分钟 |
| [警报管理](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |到达时 |
| [警报管理](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 分钟 |
| [警报管理](../platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 分钟 |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | 不适用 |
| [Application Insights 连接器（已弃用）](../platform/app-insights-connector.md) | Azure | | | |  |  | 通知时 |
| [自动化混合辅助角色](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | 不适用 |
| [Azure 应用程序网关分析](azure-networking-analytics.md) | Azure |  |  |  |  |  | 通知时 |
| **监视解决方案** | 平台  | **Microsoft Monitoring Agent** | **Operations Manager 代理** | **Azure 存储** | **是否需要 Operations Manager？** | **通过管理组发送 Operations Manager 代理数据** | **收集频率** |
| [Azure 网络安全组分析（已弃用）](azure-networking-analytics.md) | Azure |  |  |  |  |  | 通知时 |
| [Azure SQL Analytics（预览版）](azure-sql.md) | Windows | | | | | | 1 分钟 |
| [备份](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | 通知时 |
| [容量和性能（预览版）](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |到达时 |
| [更改跟踪](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[varies](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [更改跟踪](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[varies](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [容器](containers.md) | Windows 和 Linux | &#8226; | &#8226; |  |  |  | 3 分钟 |
| [密钥保管库分析](azure-key-vault.md) |Windows | | | | | |通知时 |
| [恶意软件评估](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |每小时 |
| [网络性能监视器](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | 每隔 5 秒钟进行 TCP 握手，每隔 3 分钟发送数据 |
| [Office 365 分析（预览版）](solution-office-365.md) |Windows | | | | | |通知时 |
| **监视解决方案** | 平台  | **Microsoft Monitoring Agent** | **Operations Manager 代理** | **Azure 存储** | **是否需要 Operations Manager？** | **通过管理组发送 Operations Manager 代理数据** | **收集频率** |
| [Service Fabric 分析](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 分钟 |
| [服务地图](service-map.md) | Windows 和 Linux | &#8226; | &#8226; |  |  |  | 15 秒 |
| [SQL 评估](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 天 |
| [SurfaceHub](surface-hubs.md) |Windows |&#8226; | | | | |到达时 |
| [System Center Operations Manager 评估（预览版）](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | 七天 |
| [更新管理](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |每天和安装更新后的 15 分钟内至少 2 次 |
| [升级准备情况](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 天 |
| [VMware 监视（已弃用）](vmware.md) | Linux | &#8226; |  |  |  |  | 3 分钟 |
| [Wire Data 2.0（预览版）](wire-data.md) |Windows（2012 R2 / 8.1 或更高版本） |&#8226; |&#8226; | | | | 1 分钟 |




## <a name="next-steps"></a>后续步骤
* 了解如何[安装和使用情况监视解决方案](solutions.md)。
* 了解如何[创建查询](../log-query/log-query-overview.md)来分析监视解决方案收集的数据。
