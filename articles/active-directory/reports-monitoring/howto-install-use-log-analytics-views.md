---
title: 如何安装和使用 log analytics 视图 for Azure Active Directory |Microsoft Docs
description: 了解如何安装和使用适用于 Azure Active Directory 的 log analytics 视图
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92a5d9539d254eb7d52e3277691c9d8ff5a41821
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60286705"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>安装和使用用于 Azure Active Directory 的日志分析视图

Azure Active Directory 日志分析视图可以帮助你分析和搜索 Azure AD 租户中的 Azure AD 活动日志。 Azure AD 活动日志包括：

* 审核日志：可通过[审核日志活动报表](concept-audit-logs.md)访问在租户中执行的每个任务的历史记录。
* 登录日志：可通过[登录活动报表](concept-sign-ins.md)来确定谁执行了审核日志中报告的任务。

## <a name="prerequisites"></a>必备组件

若要使用日志分析视图，需要执行以下操作：

* 在 Azure 订阅中创建 Log Analytics 工作区。 了解如何[创建 Log Analytics 工作区](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace)。
* 首先，完成[将 Azure AD 活动日志路由到 Log Analytics 工作区](howto-integrate-activity-logs-with-log-analytics.md)的相关步骤。
* 将视图从 [GitHub 存储库](https://aka.ms/AADLogAnalyticsviews)下载到本地计算机。

## <a name="install-the-log-analytics-views"></a>安装日志分析视图

1. 导航到 Log Analytics 工作区。 若要执行此操作，首先导航到 [Azure 门户](https://portal.azure.com)，然后选择“所有服务”。 在文本框中键入“Log Analytics”，然后选择“Log Analytics 工作区”。 选择要将活动日志路由到的工作区，作为必备项的一部分。
2. 选择“视图设计器”，选择“导入”，然后选择“选择文件”，从本地计算机中导入视图。
3. 选择从必备项中下载的视图，然后选择“保存”，以保存导入。 对“Azure AD 帐户预配事件”视图和“登录事件”视图执行此操作。

## <a name="use-the-views"></a>使用视图

1. 导航到 Log Analytics 工作区。 若要执行此操作，首先导航到 [Azure 门户](https://portal.azure.com)，然后选择“所有服务”。 在文本框中键入“Log Analytics”，然后选择“Log Analytics 工作区”。 选择要将活动日志路由到的工作区，作为必备项的一部分。

2. 位于工作区中时，选择“工作区摘要”。 应该会看到以下三个视图：

    * **Azure AD 帐户预配事件**：此视图显示与审核预配活动相关的报表，例如，预配的新用户数和预配失败情况、更新的用户数和更新失败情况以及取消预配的用户数和相应失败情况。    
    * **登录事件**：此视图显示与监视登录活动最相关的报表，例如，分别按应用程序、用户、设备统计的登录情况，以及随时间推移跟踪登录情况的摘要视图。

3. 选择其中一个视图，跳转到各个报表。 此外，还可以设置有关任何报表参数的警报。 例如，针对每次登录错误设置警报。 若要执行此操作，首先选择“登录事件”视图，选择“随着时间推移发生的登录错误”报表，然后选择“分析”，打开详细信息页面，其中包含报表中的实际查询。 

    ![详细信息](./media/howto-install-use-log-analytics-views/details.png)


4. 选择“设置警报”，然后选择“警报条件”部分下的“每当自定义日志搜索为逻辑未定义”&lt;&gt;。 由于我们希望在每当发生登录错误时发出警报，因此将默认警报逻辑的阈值设置为 1 并选择“完成”。 

    ![配置信号逻辑](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. 为警报输入名称和描述，然后将严重性设置为“警告”。

    ![创建规则](./media/howto-install-use-log-analytics-views/create-rule.png)

6. 选择要发出警报的操作组。 一般情况下，这可能是你想要通过电子邮件或短信通知的团队，也可以是使用 webhook、runbook、函数、逻辑应用或外部 ITSM 解决方案的自动执行的任务。 了解如何[在 Azure 门户中创建和管理操作组](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)。

7. 选择“创建警报规则”可以创建警报。 现在，每当出现登录错误时都会发出警报。

## <a name="next-steps"></a>后续步骤

* [如何使用 Azure Monitor 日志分析活动日志](howto-analyze-activity-logs-log-analytics.md)
* [Azure 门户中的 Azure Monitor 日志入门](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)