---
title: "适用于服务提供商的 Log Analytics 功能 | Microsoft 文档"
description: "Log Analytics 可以帮助托管服务提供商 (MSP)、大型企业、独立软件供应商 (ISV) 和主机托管服务提供商管理和监视客户本地或云基础结构中的服务器。"
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: richrund
ms.openlocfilehash: 8a67d9a9d345682e9e6c8f5c7779204a038f5f6a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="log-analytics-features-for-service-providers"></a>适用于服务提供商的 Log Analytics 功能
Log Analytics 可以帮助托管服务提供商 (MSP)、大型企业、独立软件供应商 (ISV) 和主机托管服务提供商管理和监视客户本地或云基础结构中的服务器。 

大型企业与服务提供商有许多相似之处，特别是当有一个集中式 IT 团队负责管理许多不同业务部门的 IT 时。 为了简单起见，本文档使用术语*服务提供商*，但同样的功能也可用于企业和其他客户。

## <a name="cloud-solution-provider"></a>云解决方案提供商
对于加入[云解决方案提供商 (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) 计划的合作伙伴和服务提供商，Log Analytics 是 CSP 订阅中可用的 Azure 服务之一。 

对于 Log Analytics，在*云解决方案提供商*订阅中启用了以下功能。

作为*云解决方案提供商*，可以：

* 在租户（客户）订阅中创建 Log Analytics 工作区。
* 访问由租户创建的工作区。 
* 使用 Azure 用户管理添加和删除用户对工作区的访问权限。 在 OMS 门户的租户工作区中，“设置”下的用户管理页面不可用
  * Log Analytics 尚不支持基于角色的访问 - 在 Azure 门户中授予用户 `reader` 权限即可允许他们在 OMS 门户中进行配置更改

要登录租户的订阅，需要指定租户标识符。 租户标识符通常是用于登录的电子邮件地址的最后一部分。

* 在 OMS 门户中的 URL 中添加 `?tenant=contoso.com`。 例如 `mms.microsoft.com/?tenant=contoso.com`
* 当在 PowerShell 中使用 `Add-AzureRmAccount` cmdlet 时，请使用 `-Tenant contoso.com` 参数
* 使用 Azure 门户中的 `OMS portal` 链接打开并登录所选工作区的 OMS 门户时，系统会自动添加租户标识符

作为云解决方案提供商的*客户*，可以：

* 在 CSP 订阅中创建 log analytics 工作区
* 访问由 CSP 创建的工作区
  * 使用 Azure 门户中的 `OMS portal` 链接打开并登录所选工作区的 OMS 门户
* 在 OMS 门户中，查看和使用“设置”下的用户管理页面

> [!NOTE]
> 所包括的针对 Log Analytics 的备份和站点恢复解决方案无法连接到恢复服务保管库，并且无法在 CSP 订阅中进行配置。 
> 
> 

## <a name="managing-multiple-customers-using-log-analytics"></a>使用 Log Analytics 管理多个客户
建议管理的每个客户创建一个 Log Analytics 工作区。 Log Analytics 工作区可提供：

* 待存储数据的地理位置。 
* 计费粒度 
* 数据隔离 
* 独特配置

通过为每个客户创建工作区，就能够将每个客户的数据分开，并跟踪每个客户的使用情况。

[管理对 Log Analytics 的访问](log-analytics-manage-access.md#determine-the-number-of-workspaces-you-need)中更详细地介绍了何时以及为何创建多个工作区。

使用 [PowerShell](log-analytics-powershell-workspace-configuration.md)、[Resource Manager 模板](log-analytics-template-workspace-configuration.md) 或 [REST API](https://www.nuget.org/packages/Microsoft.Azure.Management.OperationalInsights/) 可自动创建和配置客户工作区。

将 Resource Manager 模板用于工作区配置允许拥有可用于创建和配置工作区的主配置。 可以确信，为客户创建的工作区是根据要求自动配置的。 更新要求时，将更新模板，然后重新应用现有工作区。 此过程可确保现有工作区也能满足新标准。    

在管理多个 Log Analytics 工作区时，我们建议使用[警报](log-analytics-alerts.md)功能将每个工作区与现有票证系统/操作控制台相集成。 通过与现有系统集成，支持人员可以继续遵循其熟悉的过程。 日志分析定期检查针对您指定的警报条件的每个工作区，无需任何操作时生成警报。

若要获得个性化的数据视图，请使用 Azure 门户中的[仪表板](../azure-portal/azure-portal-dashboards.md)功能。  

对于在工作区中汇总数据的管理级报表，可以使用 Log Analytics 和 [PowerBI](log-analytics-powerbi.md) 之间的集成。 如果需要与其他报表系统集成，则可以使用 Search API（通过 PowerShell 或 [REST](log-analytics-log-search-api.md)）运行查询并导出搜索结果。

## <a name="next-steps"></a>后续步骤
* 使用[Resource Manager 模板](log-analytics-template-workspace-configuration.md)自动执行创建和配置工作区
* 使用 [PowerShell](log-analytics-powershell-workspace-configuration.md) 自动创建工作区 
* 使用[警报](log-analytics-alerts.md)以便与现有系统集成
* 使用 [PowerBI](log-analytics-powerbi.md) 生成摘要报告

