---
title: 如何使用 Azure Monitor（预览版）将 Azure Active Directory 日志与 Splunk 集成 | Microsoft Docs
description: 了解如何使用 Azure Monitor（预览版）将 Azure Active Directory 日志与 Splunk 集成
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: c4b605b6-6fc0-40dc-bd49-101d03f34665
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d1dd62d06c7e3ed634795604ce9660694ea073ca
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240335"
---
# <a name="integrate-azure-active-directory-logs-with-splunk-using-azure-monitor-preview"></a>使用 Azure Monitor（预览版）将 Azure Active Directory 日志与 Splunk 集成

本文介绍如何使用 Azure Monitor 将 Azure Active Directory 日志与 Splunk 集成。 首先，需要将日志路由到 Azure 事件中心，然后将其与 Splunk 集成。

## <a name="prerequisites"></a>先决条件

1. 包含 Azure AD 活动日志的 Azure 事件中心。 了解如何[将活动日志流式传输到事件中心](reporting-azure-monitor-diagnostics-azure-event-hub.md)。 
2. 按照以下[说明](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)下载 Splunk 的 Azure Monitor 加载项并配置 Splunk 实例。

## <a name="tutorial"></a>教程 

1. 打开 Splunk 实例，并单击“数据摘要”。
    ![数据摘要](./media/reporting-azure-monitor-diagnostics-splunk-integration/DataSummary.png "数据摘要")

2. 导航到 **Sourcetypes** 选项卡并选择 **amal: aadal:audit** ![Sourcetypes 选项卡](./media/reporting-azure-monitor-diagnostics-splunk-integration/sourcetypeaadal.png "Sourcetypes 选项卡")

3. 你将看到 Azure AD 活动日志，如下图中所示。
    ![活动日志](./media/reporting-azure-monitor-diagnostics-splunk-integration/activitylogs.png "活动日志")

> [!NOTE]
> 如果无法在 Splunk 实例中安装加载项（例如，如果使用代理或在 Splunk Cloud 上运行），则可以使用此 [Azure 函数（由事件中心中的新消息触发）](https://github.com/Microsoft/AzureFunctionforSplunkVS)将这些事件转发到 Splunk HTTP 事件收集器。 
>

## <a name="next-steps"></a>后续步骤

* [解释 Azure Monitor 中的审核日志架构](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [解释 Azure Monitor 中的登录日志架构](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [常见问题解答和已知的问题](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)