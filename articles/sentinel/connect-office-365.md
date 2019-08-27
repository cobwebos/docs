---
title: 将 Office 365 数据连接到 Azure Sentinel Preview |Microsoft Docs
description: 了解如何将 Office 365 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: ff7c862e-2e23-4a28-bd18-f2924a30899d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2019
ms.author: rkarlin
ms.openlocfilehash: 39245cb43dacfeec2b647936d5e5790d4a185467
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019002"
---
# <a name="connect-data-from-office-365-logs"></a>连接 Office 365 日志中的数据

> [!IMPORTANT]
> Azure Sentinel 当前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

只需要单击一次即可将审核日志从[Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide)流式传输到 Azure Sentinel。 可以将来自多个租户的审核日志流式传输到 Azure Sentinel 中的单个工作区。 Office 365 活动日志连接器提供对正在进行的用户活动的见解。 你将从 Office 365 获取有关各种用户、管理员、系统和策略操作和事件的信息。 通过将 Office 365 日志连接到 Azure Sentinel, 你可以使用此数据来查看仪表板、创建自定义警报并改善调查过程。

> [!IMPORTANT]
> 如果有 E3 许可证, 则必须先为 Office 365 组织启用统一审核日志记录, 然后才能通过 Office 365 管理活动 API 访问数据。 可以通过打开 Office 365 审核日志来完成此操作。 有关说明, 请参阅[打开或关闭 Office 365 审核日志搜索](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off)。 有关详细信息, 请参阅[Office 365 管理活动 API 参考](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)。

## <a name="prerequisites"></a>先决条件

- 你必须是租户的全局管理员或安全管理员
- 在你登录到 Azure Sentinel 以创建连接的计算机上, 确保端口4433已打开 web 流量。
- 如果你的租户没有 Office 365 E3 或 Office 365 E5 许可证, 则必须使用以下过程之一在 tentant 上启用统一审核:
    - [使用 AdminAuditLogConfig cmdlet](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps)并启用参数 "UnifiedAuditLogIngestionEnabled")。
    - [或使用安全与合规中心 UI](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin)。

## <a name="connect-to-office-365"></a>连接到 Office 365

1. 在 Azure Sentinel 中, 选择 "**数据连接器**", 然后单击 " **Office 365** " 磁贴。

2. 如果尚未启用此功能, 请在 "**连接**" 下, 使用 "**启用**" 按钮启用 Office 365 解决方案。 如果已启用, 则会在 "连接" 屏幕中将其标识为已启用。
1. Office 365 使你能够将来自多个租户的数据流式传输到 Azure Sentinel。 对于想要连接到的每个租户, 在 "**将租户连接到 Azure" Sentinel**下添加租户。 
1. 此时将打开一个 Active Directory 屏幕。 系统将提示你向要连接到 Azure Sentinel 的每个租户上的全局管理员用户进行身份验证, 并向 Azure Sentinel 提供权限来读取其日志。 
5. 在 "Stream Office 365 活动日志" 下, 单击 "**选择**" 以选择要流式传输到 Azure Sentinel 的日志类型。 目前, Azure Sentinel 支持 Exchange 和 SharePoint。

4. 单击 "**应用更改**"。

3. 若要在 Office 365 日志 Log Analytics 中使用相关架构, 请搜索**OfficeActivity**。


## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Office 365 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何了解[你的数据以及潜在的威胁](quickstart-get-visibility.md)。
- 开始[通过 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。

