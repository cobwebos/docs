---
title: 在 Azure Sentinel Preview 中启用合成 |Microsoft Docs
description: 了解如何在 Azure Sentinel 中启用合成。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 82becf50-6628-47e4-b3d7-18d7d72d505f
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: f90bb86632886d8a2ca6c6e8c60d3b79f5d0b9e9
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780444"
---
# <a name="enable-fusion"></a>启用融合

> [!IMPORTANT]
> Azure Sentinel 当前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Sentinel 中的机器学习是从头开始创建的。 我们周全为系统设计了 ML 创新, 旨在使安全分析师、安全数据科学家和工程师提高工作效率。 其中一种创新是专门构建的 Azure Sentinel 合成, 用于减少警报疲劳。

合成使用图形驱动的机器学习算法来关联不同产品 (如 Azure AD Identity Protection 和 Microsoft Cloud App Security) 中数百万个低保真异常活动, 以便将它们合并到可管理数量的有意思的安全事件。

## <a name="enable-fusion"></a>启用融合

1. 在 Azure 门户中, 选择图标以打开 Cloud Shell。
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  在下面打开的 "**欢迎使用" Cloud Shell**窗口中, 选择 "PowerShell"。

3.  选择在其上部署 Azure Sentinel 的订阅, 并**创建存储**。

4. 经过身份验证并生成 Azure 驱动器后, 请在命令提示符下运行以下命令:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>禁用合成

按照上述过程操作, 并运行以下命令:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>查看合成状态

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>后续步骤

本文档介绍了如何在 Azure Sentinel 中启用合成。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何了解[你的数据以及潜在的威胁](quickstart-get-visibility.md)。
- 开始[通过 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。

