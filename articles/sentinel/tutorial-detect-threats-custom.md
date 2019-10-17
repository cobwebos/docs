---
title: 创建自定义分析规则，用 Azure Sentinel 检测可疑威胁 |Microsoft Docs
description: 使用本教程来了解如何创建自定义分析规则，以便使用 Azure Sentinel 来检测可疑的威胁。
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
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 5d4c549eaded78c69d3e7fa7173b5ad9b1d82f2f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333147"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>教程：创建自定义分析规则以检测可疑威胁

在将 [数据源连接到](quickstart-onboard.md)@no__t 1To Azure Sentinel 后，你可以创建自定义规则，这些规则可以在你的环境中搜索特定的条件，并在符合条件时生成事件，以便你能够调查它们。 本教程将帮助你创建自定义规则，以检测 Azure Sentinel 的威胁。

本教程可帮助你检测 Azure Sentinel 的威胁。
> [!div class="checklist"]
> * 创建分析规则
> * 自动响应威胁

## <a name="create-custom-analytic-rules"></a>创建自定义分析规则

你可以创建自定义分析规则，以帮助你搜索环境中可疑的威胁类型和异常。 此规则可确保你立即收到通知，以便你可以对威胁进行会审、调查和修正。

1. 在 Azure 门户的 Azure Sentinel 下，选择“分析”。

1. 在顶部菜单栏中，选择 " **+ 创建**"，并选择 "**计划的查询规则**"。 这将打开 "**自定义规则创建向导**"。

    ![创建计划查询](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. 在 "**常规**" 选项卡中，提供描述性名称和说明。 根据需要设置**警报严重性**。 创建规则时，可以启用它，这会使其在完成创建后立即运行。 如果将其创建为禁用，则该规则将添加到 "**活动规则**" 选项卡，并且可以在需要时从该选项卡中启用它。

    ![开始创建自定义分析规则](media/tutorial-detect-threats-custom/general-tab.png)

1. 在 "**设置**" 选项卡中，可以直接编写查询，也可以在 Log Analytics 中创建查询，然后将其粘贴到 "**搜索查询**" 字段中。 更改和配置查询时，Azure Sentinel 会在**结果预览**窗口的右侧模拟查询结果。 这使您可以深入了解要创建的警报的特定时间间隔内生成的数据量。 此量取决于您为 "**运行查询**" 设置的内容，每个和**最后一个查找数据**。 如果你发现警报会过于频繁地触发警报，你可以将结果数设置得更高，使其高于平均基线。

   ![在 Azure Sentinel 中创建查询](media/tutorial-detect-threats-custom/settings-tab.png)

   下面是一个示例查询，当在 Azure 活动中创建异常数量的资源时，该查询将向你发出警报。

    `AzureActivity
    \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    \| where ActivityStatus == "Succeeded"
    \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

   > [!NOTE]
   > 查询长度应介于1到1000个字符之间，并且不能包含 "search \*" 或 "union \*"。

    1. 在 "**查询计划**" 下，设置以下参数：

        1.  设置 "**运行查询"，每个**设置运行查询的**频率**（频率为每5分钟一次，或每天不经常出现一次）。

        1.  设置**最后的查找数据**以控制运行查询所需的数据的时间范围（例如，它可每小时运行一次，可跨60分钟的数据运行）。

        1. 如果你只想在警报发生后收到警报，则可以设置 Azure Sentinel**停止运行查询**。 您必须设置该窗口，查询应在此期间停止运行，最多24小时。

    1. 定义**警报触发器**下的警报触发器条件。 在**实体映射**下，可以将查询中的列映射到 Azure Sentinel 识别的实体字段。 对于每个字段，将在 Log Analytics 中创建的查询中的相关列映射到相应的实体字段。 每个实体都包含多个字段，例如 SID 和 GUID。 您可以根据任何字段（而不仅仅是上层实体）映射实体。

1.  在 "**自动响应**" 选项卡中，选择自定义规则生成警报时要自动运行的任何行动手册。 有关创建和自动执行行动手册的详细信息，请参阅 [应对威胁](tutorial-respond-threats-playbook.md)。

    ![在 Azure Sentinel 中自动响应威胁](media/tutorial-detect-threats-custom/response-automation-custom.png)

1. 选择 "**查看**" 以查看新警报规则的所有设置，然后选择 "**创建" 以初始化警报规则**。

   ![查看自定义查询](media/tutorial-detect-threats-custom/review-tab.png)

1.  创建警报后，会将自定义规则添加到 "**活动规则**" 下的表中。 从此列表中，你可以启用、禁用或删除每个规则。

1.  若要查看所创建的警报规则的结果，请在 "**事件**" 页上，你可以在其中进行会审、 [调查事件](tutorial-investigate-cases.md)，并修正威胁。


> [!NOTE]
> Azure Sentinel 中生成的警报通过 [Microsoft Graph 安全机制](https://aka.ms/securitygraphdocs)提供。 有关详细信息，请参阅 [Microsoft Graph 安全警报文档](https://aka.ms/graphsecurityreferencebetadocs)。

## <a name="next-steps"></a>后续步骤

在本教程中，已学习如何使用 Azure Sentinel 来检测威胁。

若要了解如何自动响应威胁，请 [在 Azure Sentinel 中设置自动威胁响应](tutorial-respond-threats-playbook.md)。

