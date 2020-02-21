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
ms.date: 02/20/2020
ms.author: rkarlin
ms.openlocfilehash: c643c037506725b1a48588ca779d074b6aecf7c2
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77506082"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>教程：创建自定义分析规则以检测可疑威胁

将 [数据 源连接](quickstart-onboard.md)到 Azure Sentinel 后，你可以创建自定义规则，这些规则可以在你的环境中搜索特定的条件，并在符合条件时生成事件，以便你可以对其进行调查。 本教程将帮助你创建自定义规则，以检测 Azure Sentinel 的威胁。

本教程可帮助你检测 Azure Sentinel 的威胁。
> [!div class="checklist"]
> * 创建分析规则
> * 自动响应威胁

## <a name="create-custom-analytic-rules"></a>创建自定义分析规则

你可以创建自定义分析规则，以帮助你搜索环境中可疑的威胁类型和异常。 此规则可确保你立即收到通知，以便你可以对威胁进行会审、调查和修正。

1. 在 Azure 门户的 Azure Sentinel 下，选择“分析”。

1. 在顶部菜单栏中，选择 " **+ 创建**"，并选择 "**计划的查询规则**"。 这会打开**分析规则向导**。

    ![创建计划查询](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. 在 "**常规**" 选项卡中，提供唯一**名称**和**描述**。 在 "**策略**" 字段中，可以从分类规则所依据的各种攻击类别中进行选择。 根据需要设置警报**严重性**。创建规则时，默认情况下会**启用**其**状态**，这意味着它将在创建完成后立即运行。 如果你不希望它立即运行，请选择 "**禁用**"，并且该规则将添加到 "**活动规则**" 选项卡中，你可以在需要时从该选项卡中启用它。

    ![开始创建自定义分析规则](media/tutorial-detect-threats-custom/general-tab.png)

1. 在 "**设置规则逻辑**" 选项卡中，可以直接在**规则查询**字段中编写查询，也可以在 Log Analytics 中创建查询，然后将其复制并粘贴到其中。
 
   ![在 Azure Sentinel 中创建查询](media/tutorial-detect-threats-custom/settings-tab.png)

   - 请参阅右侧的**结果预览**区域，其中，Azure Sentinel 显示查询将生成的结果（日志事件）数（日志事件），在编写和配置查询时进行动态更改。 此图显示了定义的时间段内的结果数，该值由 "**查询计划**" 部分中的设置确定。
    - 如果你发现你的查询将触发过多或过多的警报，你可以在 "**警报阈值**" 部分中设置基线。

      下面是一个示例查询，当在 Azure 活动中创建异常数量的资源时，该查询将向你发出警报。

      `AzureActivity
     \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
     \| where ActivityStatus == "Succeeded"
     \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

      > [!NOTE]
      > 查询长度应介于1到10000个字符之间，并且不能包含 "search \*" 或 "union \*"。

    1. 使用 "**地图实体**" 部分可将参数从查询结果链接到 Azure Sentinel 识别的实体。 这些实体构成进一步分析的基础，包括 "**事件设置**" 选项卡中的警报分组到事件。
    1. 在 "**查询计划**" 部分中，设置以下参数：

       1. 设置 "**运行查询**"，以控制运行查询的频率（频率为每5分钟一次，或每天不经常出现一次）。

       1. 设置**最后的查找数据**，以确定查询所覆盖的数据的时间段（例如，它可以查询过去10分钟的数据或过去6小时的数据）。

       > [!NOTE]
       > 这两个设置彼此独立，最多是一个点。 您可以在涵盖时间段的短时间间隔内运行查询，该时间段比间隔更长（实际上具有重叠的查询），但您不能以超过覆盖期的时间间隔运行查询，否则，整体查询覆盖范围内会出现间隙。

    1. 使用 "**警报阈值**" 部分可定义基线。 例如，如果希望仅在查询每次运行时生成超过1000个结果时才生成警报，请在 "**查询结果数** **大于**" 时设置 "生成警报"，然后输入数字1000。 由于这是必填字段，如果不想设置基线（即，如果希望警报注册每个事件，请在 "数字" 字段中输入0）。

    1. 在 "**禁止**显示" 部分中，如果收到警报后，你可以在 "**生成警报后停止正在运行的查询**" 设置，你**希望在超过**查询间隔的时间段内挂起此规则的操作。 如果启用此设置，则必须将**停止运行查询的**时间设置为查询停止运行的时间，最长为24小时。

1. 在 "**事件设置**" 选项卡中，可以选择 Azure Sentinel 是否会将警报转换为可操作事件。 如果此选项卡为单独的选项卡，则 Azure Sentinel 将从每个警报和每个警报创建单个独立的事件。 你可以选择不创建事件，也可以通过更改此选项卡中的设置，将多个警报分组为一个事件。

    1. 在 "**事件设置**" 部分中，默认情况下，将**此分析规则触发的警报**中的 "创建事件" 设置为 "**已启用**"，这意味着 Azure Sentinel 将从该规则触发的每个警报创建单个独立的事件。<br></br>如果你不希望此规则导致创建任何事件（例如，如果此规则只是为了收集用于后续分析的信息），请将此项设置为 "**已禁用**"。

    1. 在 "**警报分组**" 部分中，如果想要从一组类似或定期警报生成单个事件，请将**此分析规则触发的组相关警报**设置为 "要**启用**的事件"，并设置以下参数。

    1. 将**组限制为在所选时间范围内创建的警报**：<br></br> 确定将类似或重复出现的警报组合在一起的时间范围。 此时间范围内的所有相应警报将共同生成一个事件或一组事件（具体取决于下面的分组设置）。 此时间范围外的警报将生成一个单独的事件或一组事件。

    2. 通过以下**方式将此分析规则触发的警报分组到单个事件中**：选择将警报组合在一起的基础：

        - **如果所有实体均匹配，则将警报分组为一个事件**： <br></br>如果为每个映射实体共享相同的值（在上面的 "设置规则逻辑" 选项卡中定义），则将警报组合在一起。 建议使用此设置。

        - **将此规则触发的所有警报分组为一个事件**： <br></br>此规则生成的所有警报都将组合在一起，即使它们不共享相同的值。

        - **如果所选实体匹配，则将警报分组为一个事件**： <br></br>如果将警报共享某些映射实体（可以从下拉列表中选择）的值相同，则会将其组合在一起。 例如，你可能想要根据源或目标 IP 地址创建单独的事件时使用此设置。

    3. **重新打开已关闭的匹配事件**：如果事件已关闭（也就是说，已解决了基本问题），然后生成了已分组到事件中的另一个警报，则将此设置设置为 "**已启用**" （如果你想要重新打开已关闭的事件），并将此设置保留为 "已**禁用**" （如果你希望警报创建新事件）。

1. 在 "**自动响应**" 选项卡中，选择自定义规则生成警报时要自动运行的任何行动手册。 有关创建和自动执行行动手册的详细信息，请参阅 [应对威胁](tutorial-respond-threats-playbook.md)。

1. 选择 "**查看" 和 "创建**" 以查看新警报规则的所有设置，然后选择 "创建"**以初始化警报规则**。
  
1. 创建警报后，会将自定义规则添加到 "**活动规则**" 下的表中。 从此列表中，你可以启用、禁用或删除每个规则。

1. 若要查看所创建的警报规则的结果，请在 "**事件**" 页上，你可以在其中进行会审、 [调查事件](tutorial-investigate-cases.md)，并修正威胁。


> [!NOTE]
> Azure Sentinel 中生成的警报通过 [Microsoft Graph 安全机制](https://aka.ms/securitygraphdocs)提供。 有关详细信息，请参阅 [Microsoft Graph 安全警报文档](https://aka.ms/graphsecurityreferencebetadocs)。

## <a name="next-steps"></a>后续步骤

在本教程中，已学习如何使用 Azure Sentinel 来检测威胁。

若要了解如何自动响应威胁，请 [在 Azure Sentinel 中设置自动威胁响应](tutorial-respond-threats-playbook.md)。

