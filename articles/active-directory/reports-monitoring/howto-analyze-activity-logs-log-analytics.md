---
title: 使用 Azure Monitor 日志分析活动日志 | Microsoft Docs
description: 了解如何使用 Azure Monitor 日志分析 Azure Active Directory 活动日志
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a48a72aa021a17c59adb86bece66cec966e234bd
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056134"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>使用 Azure Monitor 日志分析 Azure AD 活动日志

[将 Azure AD 活动日志与 Azure Monitor 日志集成](howto-integrate-activity-logs-with-log-analytics.md)之后，可以使用 Azure Monitor 日志的强大功能来深入了解自己的环境。 此外，可以安装[用于 Azure AD 活动日志的 Log Analytics 视图](howto-install-use-log-analytics-views.md)，访问有关环境中审核和登录事件的预建报表。

本文介绍如何在 Log Analytics 工作区中分析 Azure AD 活动日志。 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>先决条件 

若要按照文中内容操作，需要：

* 在 Azure 订阅中创建 Log Analytics 工作区。 了解如何[创建 Log Analytics 工作区](../../azure-monitor/learn/quick-create-workspace.md)。
* 首先，完成[将 Azure AD 活动日志路由到 Log Analytics 工作区](howto-integrate-activity-logs-with-log-analytics.md)的相关步骤。
*  [访问](../../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions) Log Analytics 工作区
* Azure Active Directory 中的以下角色（如果要通过 Azure Active Directory 门户访问 Log Analytics）
    - 安全管理员
    - 安全读取者
    - 报表读取者
    - 全局管理员
    
## <a name="navigate-to-the-log-analytics-workspace"></a>导航到 Log Analytics 工作区

1. 登录 [Azure 门户](https://portal.azure.com)。 

2. 选择“Azure Active Directory”，然后从“监视”部分选择“日志”以打开 Log Analytics 工作区。 随即工作区打开，并含有默认查询。

    ![默认查询](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>查看 Azure AD 活动日志的架构

日志将推送到工作区中的“AuditLogs”和“SigninLogs”表。 查看这些表的架构：

1. 在上一节中的默认查询视图中，选择“架构”并展开工作区。 

2. 展开“日志管理”部分，然后展开“AuditLogs”或“SigninLogs”以查看日志架构  。
    ![Audit logs](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![Signin logs](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

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

1. 在工作区中，选择“设置警报”以打开“创建规则”页面。

    ![设置警报](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. 选择警报中创建的默认“警报条件”，将默认指标中的“阈值”更新为 10。

    ![警报条件](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. 为警报输入名称和描述，然后选择严重级别。 示例中可将其设置为“信息性”。

4. 选择“操作组”，信号发生时将向其发出警报。 可以选择通过电子邮件或短信来通知团队，或使用 webhook、Azure functions 或逻辑应用来自动执行此操作。 详细了解如何[在 Azure 门户中创建和管理警报组](../../azure-monitor/platform/action-groups.md)。

5. 配置警报后，选择“创建警报”来启用它。 

## <a name="use-pre-built-workbooks-for-azure-ad-activity-logs"></a>使用预先构建的工作簿进行 Azure AD 活动日志

工作簿提供了若干与常见方案相关的报告，涉及审核、登录和预配事件。 还可使用上一节中所述步骤，针对报表中提供的任何数据设置警报。

* **预配分析**：此 [工作簿](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-log-analytics) 显示与审核预配活动相关的报告，如预配的新用户数和预配失败数、已更新的用户数和更新失败数，以及取消设置的用户和相应失败的次数。    
* **登录事件**：此工作簿显示与监视登录活动（例如，通过应用程序、用户、设备登录）相关的最相关报表，以及跟踪一段时间内的登录数的摘要视图。
* **条件性访问见解**：条件性访问见解和报表 [工作簿](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) 使你可以了解条件访问策略在一段时间内的影响。 

## <a name="next-steps"></a>后续步骤

* [Azure Monitor 日志查询入门](../../azure-monitor/log-query/get-started-queries.md)
* [在 Azure 门户中创建和管理器警报组](../../azure-monitor/platform/action-groups.md)
* [安装和使用用于 Azure Active Directory 的日志分析视图](howto-install-use-log-analytics-views.md)
