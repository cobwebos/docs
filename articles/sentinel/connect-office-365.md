---
title: 将 Office 365 日志连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Office 365 日志连接器引入有关 Exchange、团队和 SharePoint （包括 OneDrive）中正在进行的用户和管理活动的信息。
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
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: d6b59de048cdf00d352c4f488ecb51bfdf83640f
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89178920"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>将 Office 365 日志连接到 Azure Sentinel

[Office 365](https://docs.microsoft.com/office/)日志连接器会在**Exchange**和**SharePoint** (中提供有关正在进行的用户和管理活动的 Azure Sentinel 信息，包括**OneDrive**) ，现在还在**团队**中。 此信息包括操作的详细信息，例如文件下载、发送的访问请求、对组事件的更改、邮箱操作、团队事件 (如聊天、团队、成员和频道事件) ，以及执行操作的用户的详细信息。 通过将 Office 365 日志连接到 Azure Sentinel，你可以查看和分析工作簿中的此数据，查询该数据以创建自定义警报，并将其结合起来以改进调查过程，从而更深入了解 Office 365 安全性。

> [!IMPORTANT]
> **Microsoft 团队日志**的 Office 365 日志连接器扩展目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必备条件

- 您必须对 Azure Sentinel 工作区具有读取和写入权限。

- 你必须是你租户上的全局管理员或安全管理员。

- Office 365 部署必须与 Azure Sentinel 工作区在同一租户中。

> [!IMPORTANT]
> - 为了使连接器能够通过 Office 365 管理活动 API 访问数据，你必须在 Office 365 部署上启用 **统一审核日志记录** 。 根据你所具有的 Office 365/Microsoft 365 许可证的类型，默认情况下可能会启用或。 请参阅 [Office 365 安全与合规中心](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) ，根据许可证类型检查统一审核日志记录的状态。
> - 你还可以手动启用、禁用和检查 Office 365 统一审核日志记录的当前状态。 有关说明，请参阅[打开或关闭 Office 365 审核日志搜索](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off)。
> - 有关详细信息，请参阅 [Office 365 管理活动 API 参考](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)。


   > [!NOTE]
   > 如前文所述，在 " **数据类型**" 下的 "连接器" 页上，Azure Sentinel Office 365 连接器目前仅支持从 Microsoft Exchange 和 SharePoint (引入审核日志，包括 OneDrive) ， **现在也来自团队**。 但是，如果你有兴趣将 [其他 Office 数据](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888) 引入 Azure Sentinel，则有一些外部解决方案。 

## <a name="enable-the-office-365-log-connector"></a>启用 Office 365 日志连接器

### <a name="instructions-tab"></a>说明选项卡

1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器**"。

1. 从 " **数据连接器** " 库中，选择 " **Office 365**"，然后选择预览窗格中的 " **打开连接器" 页面** 。

1. 在标记为 " **配置**" 的部分下，标记要连接到 Azure Sentinel 的 Office 365 活动日志的复选框，然后单击 " **应用更改**"。 

   > [!NOTE]
   > 如果以前已将多个租户连接到 Azure Sentinel，使用受支持的旧版 Office 365 连接器，则可以查看和修改从每个租户收集的日志。 无法添加其他租户，但是可以删除以前添加的租户。

### <a name="next-steps-tab"></a>后续步骤选项卡

- 请参阅与 **Office 365** 日志连接器捆绑在一起的建议的工作簿、查询示例和分析规则模板，以了解你的 SharePoint、OneDrive、Exchange 和团队日志数据。

- 若要在 **日志**中手动查询 Office 365 日志数据，请 `OfficeActivity` 在 "查询" 窗口的第一行中输入。
   - 若要筛选特定日志类型的查询，请 `| where OfficeWorkload == "<logtype>"` 在查询的第二行中输入，其中 *\<logtype\>* 为 `SharePoint` 、、 `OneDrive` `Exchange` 或 `MicrosoftTeams` 。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Office 365 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何了解 [你的数据和潜在威胁](quickstart-get-visibility.md)。
- 使用 [内置](tutorial-detect-threats-built-in.md) 或 [自定义](tutorial-detect-threats-custom.md) 规则开始使用 Azure Sentinel 检测威胁。

