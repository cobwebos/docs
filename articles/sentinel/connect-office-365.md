---
title: 将 Office 365 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何将 Office 365 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/12/2020
ms.author: yelevin
ms.openlocfilehash: c3e63063b3ea4e7fba3997ddd645aa59fe857488
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758565"
---
# <a name="connect-data-from-office-365-logs"></a>连接来自 Office 365 日志的数据



只需单击一次即可将审核日志从 [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) 流式传输到 Azure Sentinel。 可以将审核日志从 Office 365 流式传输到同一租户上的 Azure Sentinel 工作区中。 Office 365 活动日志连接器提供对正在进行的用户活动的见解。 你会从 Office 365 获取有关各种用户、管理员、系统和策略操作与事件的信息。 通过将 Office 365 日志连接到 Azure Sentinel，可以使用此数据查看仪表板、创建自定义警报以及改善调查过程。

> [!IMPORTANT]
> 如果有 E3 许可证，则必须先为 Office 365 组织启用统一审核日志记录，然后才能通过 Office 365 管理活动 API 访问数据。 可以通过打开 Office 365 审核日志来完成此操作。 有关说明，请参阅[打开或关闭 Office 365 审核日志搜索](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off)。 有关详细信息，请参阅 [Office 365 管理活动 API 参考](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)。

## <a name="prerequisites"></a>先决条件

- 你必须是你租户上的全局管理员或安全管理员。
- 你的租户必须启用统一审核。 默认情况下，具有 Office 365 E3 或 E5 许可证的租户会启用统一审核。 <br>如果你的租户没有这些许可证之一，则必须使用以下方法之一在租户上启用统一审核：
    - [使用 Set-AdminAuditLogConfig cmdlet](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) 并启用参数“UnifiedAuditLogIngestionEnabled”。
    - [使用安全与合规中心 UI](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin)。
   
   > [!NOTE]
   > 目前，O365 数据连接器仅自动捕获 Exchange 和 Sharepoint 活动，如数据类型部分中的连接器页面上所述。 建议查看[本文，以防需要团队审核数据并使用 Sentinel 保护团队](https://techcommunity.microsoft.com/t5/azure-sentinel/protecting-your-teams-with-azure-sentinel/ba-p/1265761)。 

## <a name="connect-to-office-365"></a>连接到 Office 365

1. 在 Azure Sentinel 中，选择“数据连接器”，然后单击“Office 365”磁贴。

2. 如果尚未启用该功能，则可以转到“数据连接器”边栏选项卡，并选择“Office 365”连接器，从而实现此目的。 可在此处单击“打开连接器页面”，并在标记为“配置”的配置部分下，选择要连接到 Azure Sentinel 的所有 Office 365 活动日志。 
   > [!NOTE]
   > 如果已在 Azure Sentinel 中以前支持的 Office 365 连接器版本中连接了多个租户，则能够查看和修改从每个租户收集的日志。 无法添加其他租户，但是可以删除以前添加的租户。
3. 若要将 Log Analytics 中的相关架构用于 Office 365 日志，请搜索 OfficeActivity。


## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Office 365 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

