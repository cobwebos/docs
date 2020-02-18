---
title: 将 Office 365 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Office 365 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/12/2020
ms.author: rkarlin
ms.openlocfilehash: 709961fe63e5ed862a0e8dc3fa735d426dd02998
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371334"
---
# <a name="connect-data-from-office-365-logs"></a>连接 Office 365 日志中的数据



只需要单击一次即可将审核日志从[Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide)流式传输到 Azure Sentinel。 可以将来自 Office 365 的审核日志流式传输到同一租户的 Azure Sentinel 工作区中。 Office 365 活动日志连接器提供对正在进行的用户活动的见解。 你将从 Office 365 获取有关各种用户、管理员、系统和策略操作和事件的信息。 通过将 Office 365 日志连接到 Azure Sentinel，你可以使用此数据来查看仪表板、创建自定义警报并改善调查过程。

> [!IMPORTANT]
> 如果有 E3 许可证，则必须先为 Office 365 组织启用统一审核日志记录，然后才能通过 Office 365 管理活动 API 访问数据。 可以通过打开 Office 365 审核日志来完成此操作。 有关说明，请参阅[打开或关闭 Office 365 审核日志搜索](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off)。 有关详细信息，请参阅[Office 365 管理活动 API 参考](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)。

## <a name="prerequisites"></a>必备条件

- 你必须是租户的全局管理员或安全管理员。
- 如果你的租户没有 Office 365 E3 或 Office 365 E5 许可证，则必须使用以下过程之一在你的租户上启用统一审核：
    - [使用 AdminAuditLogConfig cmdlet](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps)并启用参数 "UnifiedAuditLogIngestionEnabled"）。
    - [或使用安全与合规中心 UI](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin)。

## <a name="connect-to-office-365"></a>连接到 Office 365

1. 在 Azure Sentinel 中，选择 "**数据连接器**"，然后单击 " **Office 365** " 磁贴。

2. 如果尚未启用此功能，可以转到 "**数据连接器**" 边栏选项卡并选择 " **Office 365**连接器"。 在此处，可以单击 "**打开连接器" 页**，然后在 "配置" 部分的 "**配置**" 下，选择要连接到 Azure Sentinel 的所有 Office 365 活动日志。 
   > [!NOTE]
   > 如果已在 Azure Sentinel 中以前支持的 Office 365 连接器版本中连接了多个租户，则可以查看和修改从每个租户收集的日志。 你将无法添加其他租户，但你可以删除以前添加的租户。
3. 若要在 Office 365 日志 Log Analytics 中使用相关架构，请搜索**OfficeActivity**。


## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Office 365 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

