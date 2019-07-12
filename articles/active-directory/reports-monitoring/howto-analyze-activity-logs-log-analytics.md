---
title: 分析使用 Azure Monitor 日志的 Azure Active Directory 活动日志 |Microsoft Docs
description: 了解如何分析使用 Azure Monitor 日志的 Azure Active Directory 活动日志
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4145ea2162cdd56deb8bbdcfa81d5a90a2ed9382
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611570"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>分析 Azure AD 活动日志与 Azure Monitor 日志

[将 Azure AD 活动日志与 Azure Monitor 日志集成](howto-integrate-activity-logs-with-log-analytics.md)之后，可以使用 Azure Monitor 日志的强大功能来深入了解自己的环境。 此外，可以安装[用于 Azure AD 活动日志的 Log Analytics 视图](howto-install-use-log-analytics-views.md)，访问有关环境中审核和登录事件的预建报表。

本文介绍如何在 Log Analytics 工作区中分析 Azure AD 活动日志。 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>系统必备 

若要按照文中内容操作，需要：

* 在 Azure 订阅中创建 Log Analytics 工作区。 了解如何[创建 Log Analytics 工作区](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace)。
* 首先，完成[将 Azure AD 活动日志路由到 Log Analytics 工作区](howto-integrate-activity-logs-with-log-analytics.md)的相关步骤。

## <a name="navigate-to-the-log-analytics-workspace"></a>导航到 Log Analytics 工作区

1. 登录到 [Azure 门户](https://portal.azure.com)。 

2. 选择“Azure Active Directory”  ，然后从“监视”  部分选择“日志”  以打开 Log Analytics 工作区。 随即工作区打开，并含有默认查询。

    ![默认查询](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>查看 Azure AD 活动日志的架构

日志将推送到工作区中的“AuditLogs”  和“SigninLogs”  表。 查看这些表的架构：

1. 在上一节中的默认查询视图中，选择“架构”  并展开工作区。 

2. 展开“日志管理”  部分，然后展开“AuditLogs”  或“SignInLogs”  以查看日志架构。
    ![审核日志](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png)![登录日志](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>查询 Azure AD 活动日志

现在工作区中已有日志，可以对其运行查询。 例如，若要获取最近一周使用最多的应用程序，将默认查询替换为以下查询并选择“运行” 

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

若要获取最近一周发生最多的审核事件，可使用以下查询：

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Azure AD 活动日志数据警报

还可以针对查询设置警报。 例如，若要配置为当最近一周使用的应用程序超过 10 个时发出警报：

1. 在工作区中，选择“设置警报”  以打开“创建规则”  页面。

    ![设置警报](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. 选择警报中创建的默认“警报条件”  ，将默认指标中的“阈值”  更新为 10。

    ![警报条件](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. 为警报输入名称和描述，然后选择严重级别。 示例中可将其设置为“信息性”  。

4. 选择“操作组”  ，信号发生时将向其发出警报。 可以选择通过电子邮件或短信来通知团队，或使用 webhook、Azure functions 或逻辑应用来自动执行此操作。 详细了解如何[在 Azure 门户中创建和管理警报组](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)。

5. 配置警报后，选择“创建警报”  来启用它。 

## <a name="install-and-use-pre-built-views-for-azure-ad-activity-logs"></a>安装并使用用于 Azure AD 活动日志的预建视图

还可以下载用于 Azure AD 活动日志的预建日志分析视图。 这些视图提供与涉及审核和登录事件的常见方案相关的多个报表。 还可使用上一节中所述步骤，针对报表中提供的任何数据设置警报。

* **Azure AD 帐户预配事件**：此视图显示与审核预配活动相关的报表，例如，预配的新用户数和预配失败情况、更新的用户数和更新失败情况以及取消预配的用户数和相应失败情况。    
* **登录事件**：此视图显示与监视登录活动最相关的报表，例如，分别按应用程序、用户、设备统计的登录情况，以及随时间推移跟踪登录情况的汇总视图。
* **用户执行“同意”相关情况**：此视图显示与用户同意相关的报表，如由用户执行的“同意”操作、按执行“同意”操作的用户统计的登录情况，以及按所有基于同意操作的应用程序统计的登录情况。 

了解如何[安装和使用 Azure AD 活动日志的日志分析视图](howto-install-use-log-analytics-views.md)。 


## <a name="next-steps"></a>后续步骤

* [Azure Monitor 日志查询入门](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)
* [在 Azure 门户中创建和管理器警报组](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)
* [安装和使用用于 Azure Active Directory 的日志分析视图](howto-install-use-log-analytics-views.md)
