---
title: 收集 Azure Sentinel 预览版中的 Office 365 数据 |Microsoft Docs
description: 了解如何收集 Azure Sentinel 中的 Office 365 数据。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: ff7c862e-2e23-4a28-bd18-f2924a30899d
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: 7cd90b987550dc60b30d3aa0cd1016b681eec85f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547006"
---
# <a name="collect-data-from-office-365-logs"></a>从 Office 365 日志中收集数据

> [!IMPORTANT]
> Azure Sentinel 目前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以从审核日志流式传输[Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide)到 Azure Sentinel 单击一次。 您可以流式传输到单个工作区中 Azure Sentinel 来自多个租户的审核日志。 Office 365 活动日志连接器提供了深入了解正在进行的用户活动。 将从 Office 365 来获取有关各种用户、 管理员、 系统和策略的操作和事件信息。 通过 Office 365 日志连接到 Azure Sentinel 可以使用此数据以查看仪表板、 创建自定义警报，并改进调查过程。


## <a name="prerequisites"></a>必备组件

- 您必须是全局管理员或安全管理员在你的租户
- 若要添加一个租户，请打开端口 4433 从中执行连接的计算机上的 web 流量。

## <a name="connect-to-office-365"></a>连接到 Office 365

1. 在 Azure Sentinel，选择**数据收集**，然后单击**Office 365**磁贴。

2. 如果你尚未启用，在**连接**使用**启用**按钮以启用 Office 365 解决方案。 如果已启用，那么将为已启用了连接屏幕中进行标识。
1. Office 365 使你能够对数据进行流式传输到 Azure Sentinel 的多个租户。 你想要连接到每个的租户，将添加在租户**将租户连接到 Azure Sentinel**。 
1. Active Directory 屏幕将打开。 系统会提示使用上你想要连接到 Azure Sentinel，并提供到 Azure Sentinel 读取其日志的权限的每个租户的全局管理员用户进行身份验证。 
5. 在 Stream Office 365 活动日志，请单击**选择**选择你想要流式传输到 Azure Sentinel 哪些日志类型。 目前，Azure Sentinel 支持 Exchange 和 SharePoint。

4. 单击**将更改应用**。

3. 若要使用 Log Analytics 中的 Office 365 日志相关的架构，搜索**OfficeActivity**。


## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 Office 365 连接到 Azure Sentinel。 若要了解有关 Azure Sentinel 的详细信息，请参阅以下文章：
- 了解如何[来了解一下你的数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[检测威胁 Azure Sentinel](tutorial-detect-threats.md)。

