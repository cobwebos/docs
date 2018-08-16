---
title: 如何使用 Azure Monitor 将 Azure Active Directory 日志与 Splunk 集成（预览）| Microsoft Docs
description: 了解如何使用 Azure Monitor 将 Azure Active Directory 日志与 Splunk 集成（预览）
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
ms.openlocfilehash: 42dbb8c7e74bd3acb99028477f34f99f1334d577
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503836"
---
# <a name="integrate-azure-ad-logs-with-splunk-by-using-azure-monitor-preview"></a>使用 Azure Monitor 将 Azure AD 日志与 Splunk 集成（预览）

本文介绍如何使用 Azure Monitor 将 Azure Active Directory (Azure AD) 日志与 Splunk 集成。 首先将日志路由到 Azure 事件中心，然后将事件中心与 Splunk 集成。

## <a name="prerequisites"></a>先决条件

若要使用此功能，需满足以下条件：
* 包含 Azure AD 活动日志的 Azure 事件中心。 了解如何[将活动日志流式传输到事件中心](reporting-azure-monitor-diagnostics-azure-event-hub.md)。 
* 适用于 Splunk 的 Azure Monitor 加载项。 [下载并配置 Splunk 实例](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)。

## <a name="tutorial"></a>教程 

1. 打开 Splunk 实例，并选择“数据摘要”。

    ![“数据摘要”按钮](./media/reporting-azure-monitor-diagnostics-splunk-integration/DataSummary.png)

2. 选择“Sourcetypes”选项卡，然后选择“amal: aadal:audit”

    ![数据摘要 Sourcetypes 选项卡](./media/reporting-azure-monitor-diagnostics-splunk-integration/sourcetypeaadal.png)

    Azure AD 活动日志将如下图中所示：

    ![活动日志](./media/reporting-azure-monitor-diagnostics-splunk-integration/activitylogs.png)

> [!NOTE]
> 如果无法在 Splunk 实例中安装加载项（例如，如果使用代理或在 Splunk Cloud 上运行），则可以将这些事件转发到 Splunk HTTP 事件收集器。 为此，请使用此 [Azure 函数](https://github.com/Microsoft/AzureFunctionforSplunkVS)，该函数通过事件中心中的新消息触发。 
>

## <a name="next-steps"></a>后续步骤

* [解释 Azure Monitor 中的审核日志架构](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [解释 Azure Monitor 中的登录日志架构](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [常见问题解答和已知的问题](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)
