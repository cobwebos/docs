---
title: "在 Log Analytics 中收集 Azure 存储数据概述 | Microsoft Docs"
description: "通常，Azure 资源可以使用 Azure 诊断将日志和度量值写入 Azure 存储帐户。 Log Analytics 可以为此数据编制索引并使其可搜索。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2a160030ab51799199fc6df08133f811d4987feb


---
# <a name="collecting-azure-storage-data-in-log-analytics-overview"></a>在 Log Analytics 中收集 Azure 存储数据概述
许多 Azure 资源都可以将日志和度量值写入 Azure 存储帐户。 Log Analytics 可以使用此数据，并使监视 Azure 资源变得更容易。

若要写入 Azure 存储，资源可以使用 Azure 诊断，或者使用其自己的方式来写入数据。 此数据可通过多种格式写入以下位置之一：

* Azure 表
* Azure blob
* EventHub

Log Analytics 支持使用 [Azure 诊断日志](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)写入数据的 Azure 服务。 此外，Log Analytics 还支持采用其他格式和在其他位置输出日志和度量值的其他服务。  

> [!NOTE]
> 当用户向存储帐户发送诊断时，以及当 Log Analytics 从存储帐户读取数据时，系统会针对存储和事务收取正常 Azure 数据费率。
> 
> 

![Azure 存储关系图](media/log-analytics-azure-storage/azure-storage-diagram.png)

## <a name="supported-azure-resources"></a>支持的 Azure 资源
Log Analytics 可以收集以下 Azure 资源的数据：

| 资源类型 | 日志（诊断类别） | Log Analytics 解决方案 |
| --- | --- | --- |
| Application Insights |可用性 <br> 自定义事件 <br> 异常 <br> 请求 <br> |Application Insights（预览版） |
| 自动化 <br> Microsoft.Automation/AutomationAccounts |JobLogs <br> JobStreams |AzureAutomation（预览版） |
| 密钥保管库 <br> Microsoft.KeyVault/Vaults |AuditEvent |KeyVault（预览版） |
| 应用程序网关 <br> Microsoft.Network/ApplicationGateways |ApplicationGatewayAccessLog <br> ApplicationGatewayPerformanceLog |AzureNetworking（预览版） |
| 网络安全组 <br> Microsoft.Network/NetworkSecurityGroups |NetworkSecurityGroupEvent <br> NetworkSecurityGroupRuleCounter |AzureNetworking（预览版） |
| Service Fabric |ETWEvent <br> 操作事件 <br> 可靠角色事件 <br> 可靠服务事件 |ServiceFabric（预览版） |
| 虚拟机 |Linux Syslog <br> Windows 事件 <br> IIS 日志 <br> Windows ETWEvent |*无* |
| Web 角色 <br> 辅助角色 |Linux Syslog <br> Windows 事件 <br> IIS 日志 <br> Windows ETWEvent |*无* |

> [!NOTE]
> 若要监视 Azure 虚拟机（Linux 和 Windows），建议安装 [Log Analytics VM 扩展](log-analytics-azure-vm-extension.md)。 与使用写入存储的诊断情形相比，代理提供关于虚拟机的更深层次见解。
> 
> 

可以通过在我们的[反馈页](http://feedback.azure.com/forums/267889-azure-log-analytics/category/88086-log-management-and-log-collection-policy)上投票，来帮助我们设置附加日志的优先级以供 OMS 进行分析。

* 请参阅[使用 Log Analytics 分析 Azure 诊断日志](log-analytics-azure-storage-json.md)，详细了解 Log Analytics 如何从支持 [Azure 诊断日志](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)的 Azure 服务中读取日志：
  * Azure 密钥保管库
  * Azure 自动化
  * 应用程序网关
  * 网络安全组
* 请参阅[使用适用于 IIS 的 blob 存储和适用于事件的表存储](log-analytics-azure-storage-iis-table.md)，详细了解 Log Analytics 如何读取将诊断写入到表存储或将 ISS 日志写入到 blob 存储的 Azure 服务的日志，包括：
  * Service Fabric
  * Web 角色
  * 辅助角色
  * 虚拟机

## <a name="next-steps"></a>后续步骤
* [使用 Log Analytics 分析 Azure 诊断日志](log-analytics-azure-storage-json.md)，读取将诊断以 JSON 格式写入 blob 存储的 Azure 服务中的日志。
* [使用适用于 IIS 的 blob 存储和适用于事件的表存储](log-analytics-azure-storage-iis-table.md)，读取将诊断写入到表存储或将 ISS 日志写入到 blob 存储的 Azure 服务的日志。
* [启用解决方案](log-analytics-add-solutions.md)深入分析数据。
* [使用搜索查询](log-analytics-log-searches.md)分析数据。




<!--HONumber=Nov16_HO3-->


