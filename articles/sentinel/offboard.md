---
title: 删除 Azure 哨兵*微软文档
description: 如何删除 Azure Sentinel 实例。
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
ms.date: 12/29/2019
ms.author: yelevin
ms.openlocfilehash: 4042f7b9d0f6fdf293f338c89d5783fe266c4edf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77581678"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>从工作区中删除 Azure 哨兵

如果不再想要使用 Azure Sentinel，本文将介绍如何将其从工作区中删除。

## <a name="how-to-remove-azure-sentinel"></a>如何删除 Azure 哨兵

按照此过程从工作区中删除 Azure 哨兵：

1. 转到**Azure 哨兵**，后跟 **"设置"，** 然后选择"**删除 Azure 哨兵"选项卡**。

1. 在删除 Azure 哨兵之前，请使用复选框告知我们删除它的原因。

1. 选择**从工作区中删除 Azure 哨兵**。
    
    ![删除安全见解解决方案](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>幕后会发生什么？

删除解决方案时，Azure Sentinel 最多需要 48 小时才能完成删除过程的第一阶段。

确定断开连接后，开始卸载过程。

**这些连接器的配置将被删除：**
-   Office 365

-   AWS

-   微软服务安全警报（Azure ATP、微软云应用安全（包括云发现影子 IT 报告、Azure AD 标识保护、微软防御者 ATP、Azure 安全中心）

-   威胁智能

-   常见安全日志（包括基于 CEF 的日志、梭子鱼和 Syslog）（如果您有 Azure 安全中心，将继续收集这些日志。

-   Windows 安全事件（如果您有 Azure 安全中心，将继续收集这些日志。

在最初的 48 小时内，数据和分析规则（包括实时自动化配置）将不再在 Azure Sentinel 中访问或查询。

**30 天后，这些资源将被删除：**

-   事件（包括调查元数据）

-   分析规则

-   书签

不会删除您的行动手册、保存的工作簿、保存的狩猎查询和笔记本。 **有些可能会由于删除的数据而中断。您可以手动删除这些。**

删除服务后，有 30 天的宽限期，在此期间您可以重新启用解决方案，您的数据和分析规则将被还原，但必须重新连接断开连接的已配置连接器。

> [!NOTE]
> 如果删除解决方案，您的订阅将继续注册到 Azure Sentinel 资源提供程序。 **您可以手动将其删除。**




## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何删除 Azure Sentinel 服务。 如果您改变主意，想再次安装它：
- 开始[载入 Azure 哨兵](quickstart-onboard.md)。
