---
title: 删除 Azure Sentinel |Microsoft Docs
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
ms.date: 12/29/2019
ms.author: rkarlin
ms.openlocfilehash: d71a9fc21cca75312696a1bc17c4896c2e5bce2d
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610347"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>从工作区中删除 Azure Sentinel

如果不再想要使用 Azure Sentinel，本文介绍了如何将其从工作区中删除。

## <a name="how-to-remove-azure-sentinel"></a>如何删除 Azure Sentinel

按照此过程从工作区中删除 Azure Sentinel：

1. 中转到**Azure Sentinel**，然后选择 "**设置**"，然后选择 "**删除 Azure sentinel**" 选项卡。

1. 删除 Azure Sentinel 之前，请使用复选框告诉我们你为何要将其删除。

1. 选择 "**从工作区中删除 Azure Sentinel**"。
    
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

在前48小时内，数据和分析规则（包括实时自动化配置）将无法再在 Azure Sentinel 中访问或查询。

**30天后，这些资源将被删除：**

-   事件（包括调查元数据）

-   分析规则

-   书签

不会删除行动手册、保存的工作簿、保存的搜寻查询和笔记本。 **某些可能会因删除的数据而中断。您可以手动删除这些。**

删除服务后，会有30天的宽限期，在此期间，你可以重新启用该解决方案，你的数据和分析规则将会还原，但必须重新连接已断开连接的已配置连接器。

> [!NOTE]
> 如果删除解决方案，则会继续向 Azure Sentinel 资源提供程序注册订阅。 **您可以手动将其删除。**




## <a name="next-steps"></a>后续步骤
本文档介绍了如何删除 Azure Sentinel 服务。 如果你改变主意并想要重新安装它：
- 开始进行[Azure Sentinel](quickstart-onboard.md)入门。
