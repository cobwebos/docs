---
title: 将 Office 365 日志连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Office 365 日志连接器引入有关 Exchange 和 SharePoint 中正在进行的用户和管理活动（包括 OneDrive）的信息。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2020
ms.author: yelevin
ms.openlocfilehash: 180b25f80bd27caea20b1c17cd84fda38c172e0f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559337"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>将 Office 365 日志连接到 Azure Sentinel

[Office 365](https://docs.microsoft.com/office/)日志连接器提供有关**Exchange**和**SharePoint**中正在进行的用户和管理活动（包括**OneDrive**）的 Azure Sentinel 信息。 此信息包括操作的详细信息，例如文件下载、发送的访问请求、对组事件的更改和邮箱操作，以及执行操作的用户的详细信息。 通过将 Office 365 日志连接到 Azure Sentinel，你可以查看和分析工作簿中的此数据，查询该数据以创建自定义警报，并将其结合起来以改进调查过程，从而更深入了解 Office 365 安全性。

## <a name="prerequisites"></a>先决条件

- 您必须对 Azure Sentinel 工作区具有读取和写入权限。

- 你必须是你租户上的全局管理员或安全管理员。

- Office 365 部署必须与 Azure Sentinel 工作区在同一租户中。

> [!IMPORTANT]
> - 为了使连接器能够通过 Office 365 管理活动 API 访问数据，你必须在 Office 365 部署上启用**统一审核日志记录**。 根据你所具有的 Office 365/Microsoft 365 许可证的类型，默认情况下可能会启用或。 请参阅[Office 365 安全与合规中心](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center)，根据许可证类型检查统一审核日志记录的状态。
> - 你还可以手动启用、禁用和检查 Office 365 统一审核日志记录的当前状态。 有关说明，请参阅[打开或关闭 Office 365 审核日志搜索](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off)。
> - 有关详细信息，请参阅[Office 365 管理活动 API 参考](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)。


   > [!NOTE]
   > 如上所述，在 "**数据类型**" 下的 "连接器" 页上，Azure Sentinel Office 365 连接器目前仅支持从 Microsoft Exchange 和 SharePoint （包括 OneDrive）引入审核日志。 但是，如果你有兴趣将[数据从团队](https://techcommunity.microsoft.com/t5/azure-sentinel/protecting-your-teams-with-azure-sentinel/ba-p/1265761)或[其他 Office 数据](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888)引入 Azure Sentinel，则有一些外部解决方案。 

## <a name="enable-the-office-365-log-connector"></a>启用 Office 365 日志连接器

1. 在 Azure Sentinel 导航菜单中，选择 "**数据连接器**"。

1. 从 "**数据连接器**" 列表中，单击 " **Office 365**"，然后单击右下角的 "**打开连接器页**" 按钮。

1. 在标记为 "**配置**" 的部分下，标记要连接到 Azure Sentinel 的 Office 365 活动日志的复选框，然后单击 "**应用更改**"。 

   > [!NOTE]
   > 如果以前已将多个租户连接到 Azure Sentinel，使用受支持的旧版 Office 365 连接器，则可以查看和修改从每个租户收集的日志。 无法添加其他租户，但是可以删除以前添加的租户。

1. 若要在 Log Analytics 中查询 Office 365 日志数据，请 `OfficeActivity` 在 "查询" 窗口的第一行中键入。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Office 365 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何了解[你的数据和潜在威胁](quickstart-get-visibility.md)。
- 使用[内置](tutorial-detect-threats-built-in.md)或[自定义](tutorial-detect-threats-custom.md)规则开始使用 Azure Sentinel 检测威胁。

