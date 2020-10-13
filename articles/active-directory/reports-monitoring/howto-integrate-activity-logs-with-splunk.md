---
title: 使用 Azure Monitor 集成 Splunk | Microsoft Docs
description: 了解如何使用 Azure Monitor 将 Azure Active Directory 日志与 Splunk 集成。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/10/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 351669453a5ce6930d3eb912e95e530d14febf61
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335844"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>如何：使用 Azure Monitor 将 Azure Active Directory 日志与 Splunk 集成

本文介绍如何使用 Azure Monitor 将 Azure Active Directory (Azure AD) 日志与 Splunk 集成。 首先将日志路由到 Azure 事件中心，然后将事件中心与 Splunk 集成。

## <a name="prerequisites"></a>先决条件

若要使用此功能，需满足以下条件:

- 包含 Azure AD 活动日志的 Azure 事件中心。 了解如何[将活动日志流式传输到事件中心](./tutorial-azure-monitor-stream-logs-to-event-hub.md)。 

-  [针对 Splunk 的 Microsoft Azure 添加](https://splunkbase.splunk.com/app/3757/)。 

## <a name="integrate-azure-active-directory-logs"></a>集成 Azure Active Directory 日志 

1. 打开 Splunk 实例，并选择“数据摘要”。

    ![“数据摘要”按钮](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. 选择“Sourcetypes”选项卡，然后选择“amal: aadal:audit” 

    ![数据摘要 Sourcetypes 选项卡](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Azure AD 活动日志将如下图中所示：

    ![活动日志](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> 如果无法在 Splunk 实例中安装加载项（例如，如果使用代理或在 Splunk Cloud 上运行），则可以将这些事件转发到 Splunk HTTP 事件收集器。 为此，请使用此 [Azure 函数](https://github.com/Microsoft/AzureFunctionforSplunkVS)，该函数通过事件中心中的新消息触发。 
>

## <a name="next-steps"></a>后续步骤

* [解释 Azure Monitor 中的审核日志架构](reference-azure-monitor-audit-log-schema.md)
* [解释 Azure Monitor 中的登录日志架构](reference-azure-monitor-sign-ins-log-schema.md)
* [常见问题解答和已知的问题](concept-activity-logs-azure-monitor.md#frequently-asked-questions)