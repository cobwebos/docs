---
title: 将 Office 365 数据连接到 Azure 哨兵*微软文档
description: 了解如何将 Office 365 数据连接到 Azure 哨兵。
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
ms.openlocfilehash: 43eba727b1dc724aae6eea3ec77de1363c5db73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252519"
---
# <a name="connect-data-from-office-365-logs"></a>连接来自 Office 365 日志的数据



只需单击一下，即可将审核日志从[Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide)流式传输到 Azure Sentinel 中。 可以将 Office 365 中的审核日志流式传输到同一租户上的 Azure Sentinel 工作区中。 Office 365 活动日志连接器提供对正在进行的用户活动的见解。 您将从 Office 365 获取有关各种用户、管理员、系统以及策略操作和事件的信息。 通过将 Office 365 日志连接到 Azure Sentinel，可以使用此数据查看仪表板、创建自定义警报并改进调查过程。

> [!IMPORTANT]
> 如果您有 E3 许可证，则必须先为 Office 365 组织启用统一审核日志记录，然后才能通过 Office 365 管理活动 API 访问数据。 为此，请打开 Office 365 审核日志。 有关说明，请参阅[打开或关闭 Office 365 审核日志搜索](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off)。 有关详细信息，请参阅[Office 365 管理活动 API 引用](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)。

## <a name="prerequisites"></a>先决条件

- 您必须是租户上的全局管理员或安全管理员。
- 租户必须启用统一审核。 默认情况下，具有 Office 365 E3 或 E5 许可证的租户启用了统一审核。 <br>如果租户没有这些许可证之一，则必须使用以下方法之一对租户启用统一审核：
    - [使用集-管理审核日志配置 cmdlet](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps)并启用参数"启用统一审核日志"。
    - [使用安全&合规性中心 UI](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin)。

## <a name="connect-to-office-365"></a>连接到 Office 365

1. 在 Azure 哨兵中，选择 **"数据"连接器**，然后单击**Office 365**磁贴。

2. 如果尚未启用它，则可以通过访问**数据连接器**边栏选项卡并选择**Office 365**连接器来执行此操作。 在这里，您可以单击 **"打开连接器页面**"，并在标记为 **"配置"** 的配置部分下选择要连接到 Azure Sentinel 的所有 Office 365 活动日志。 
   > [!NOTE]
   > 如果在 Azure Sentinel 中以前支持的 Office 365 连接器版本中连接了多个租户，则可以查看和修改从每个租户收集的日志。 您将无法添加其他租户，但可以删除以前添加的租户。
3. 要在记录分析中为 Office 365 日志使用相关架构，请搜索**OfficeActivity**。


## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 Office 365 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

