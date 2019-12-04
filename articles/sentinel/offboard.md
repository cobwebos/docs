---
title: 下架 Azure Sentinel |Microsoft Docs
description: 如何删除 Azure Sentinel 实例。
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
ms.date: 12/02/2019
ms.author: rkarlin
ms.openlocfilehash: 4c0c415235fd290bc47ac402a6b81a1afa7af903
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74777428"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>从工作区中删除 Azure Sentinel

如果不再想要使用 Azure Sentinel，本文介绍了如何将其从工作区中删除。

## <a name="how-to-delete-azure-sentinel"></a>如何删除 Azure Sentinel

在后台，当你安装 Azure Sentinel 时， **SecurityInsights**解决方案会安装在所选工作区上。 因此，您需要做的第一件事就是删除**SecurityInsights**解决方案。

1.  依次访问 " **Azure Sentinel**"、"**配置**"、"**工作区设置**" 和 "**解决方案**"。

2.  选择 `SecurityInsights` 并单击它。

    ![查找 SecurityInsights 解决方案](media/offboard/find-solution.png)

3.  在页面顶部选择 "**删除**"。

    > [!IMPORTANT]
    > 如果删除工作区，它可能会影响使用此工作区的其他解决方案和数据源，包括 Azure Monitor。 若要查看使用此工作区的解决方案，请参阅[列出已安装的监视解决方案](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions)。 若要检查哪些解决方案的数据正在引入工作区中，请参阅[了解引入数据量](../azure-monitor/platform/manage-cost-storage.md#understanding-ingested-data-volume)。

    ![删除 SecurityInsights 解决方案](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>幕后会发生什么情况呢？

删除解决方案时，Azure Sentinel 需要48小时才能完成删除过程的第一阶段。

确定断开连接后，将开始脱离进程。

**这些连接器的配置已删除：**
-   PowerApps

-   AWS

-   Microsoft 服务安全警报（Azure ATP，Microsoft Cloud App Security 包括 Cloud Discovery 影子 IT 报告、Azure AD Identity Protection、Microsoft Defender ATP、Azure 安全中心）

-   威胁智能

-   常见安全日志（包括基于 CEF 的日志、Barracuda 和 Syslog）（如果你有 Azure 安全中心，将继续收集这些日志。）

-   Windows 安全事件（如果你有 Azure 安全中心，将继续收集这些日志。）

在前48小时内，数据和警报规则（包括实时自动化配置）将无法再在 Azure Sentinel 中访问或查询。

**30天后，将删除这些资源：**

-   事件（包括调查元数据）

-   预警规则

-   书签

不会删除行动手册、保存的工作簿、保存的搜寻查询和笔记本。 **某些可能会因删除的数据而中断。您可以手动删除这些。**

删除服务后，会有30天的宽限期，在此期间，你可以重新启用该解决方案，并且你的数据和警报规则将还原，但必须重新连接已断开连接的已配置连接器。

> [!NOTE]
> 如果删除解决方案，则会继续向 Azure Sentinel 资源提供程序注册订阅。 **您可以手动将其删除。**




## <a name="next-steps"></a>后续步骤
本文档介绍了如何删除 Azure Sentinel 服务。 如果你改变主意并想要重新安装它：
- 开始进行[Azure Sentinel](quickstart-onboard.md)入门。

