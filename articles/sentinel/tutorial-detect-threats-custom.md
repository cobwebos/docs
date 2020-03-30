---
title: 创建自定义分析规则，以便使用 Azure Sentinel 检测可疑威胁*微软文档
description: 使用本教程了解如何创建自定义分析规则以使用 Azure Sentinel 检测可疑威胁。
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
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: cea7429ecea105355b0afe306bfa334e55d5d9c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585101"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>教程：创建自定义分析规则以检测可疑威胁

 [将数据源](quickstart-onboard.md) 连接到 Azure Sentinel 后，可以创建自定义规则，这些规则可以搜索整个环境中的特定条件，并在匹配条件时生成事件，以便可以调查它们。 本教程可帮助您创建自定义规则，以便使用 Azure Sentinel 检测威胁。

本教程可帮助您使用 Azure Sentinel 检测威胁。
> [!div class="checklist"]
> * 创建分析规则
> * 自动响应威胁

## <a name="create-custom-analytic-rules"></a>创建自定义分析规则

您可以创建自定义分析规则，以帮助您搜索环境中可疑的威胁和异常类型。 该规则确保立即通知您，以便您可以对威胁进行会审、调查和补救。

1. 在 Azure 门户的 Azure Sentinel 下，选择“分析”。****

1. 在顶部菜单栏中，选择 **"创建"** 并选择 **"计划查询规则**"。 这将打开**分析规则向导**。

    ![创建计划查询](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. 在 **"常规"** 选项卡中，提供唯一**的名称**和**说明**。 在 **"战术"** 字段中，您可以从对规则进行分类的攻击类别中进行选择。 根据需要设置警报**严重性**。 创建规则时，默认情况下，其**状态****处于启用状态**状态，这意味着它将在您完成创建后立即运行。 如果不希望它立即运行，请选择 **"禁用**"，规则将添加到 **"活动规则"** 选项卡中，并在需要时可从那里启用它。

    ![开始创建自定义分析规则](media/tutorial-detect-threats-custom/general-tab.png)

1. 在 **"设置规则逻辑"** 选项卡中，可以直接在 **"规则"查询**字段中编写查询，也可以在日志分析中创建查询，然后复制并粘贴到该查询。
 
   ![在 Azure 哨兵中创建查询](media/tutorial-detect-threats-custom/settings-tab.png)

   - 请参阅右侧**的"结果预览"** 区域，其中 Azure Sentinel 显示查询将生成的结果（日志事件），在编写和配置查询时动态更改。 该图显示定义时间段内的结果数，由 **"查询计划"** 部分中的设置确定。
    - 如果您发现查询将触发太多或太频繁的警报，则可以在 **"警报阈值"** 部分设置基线。

      下面是一个示例查询，当在 Azure 活动中创建异常数量的资源时，它会提醒您。

      `AzureActivity
     \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
     \| where ActivityStatus == "Succeeded"
     \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

      > [!NOTE]
      > 查询长度应介于 1 到 10，000 个字符之间，不能\*包含"搜索"\*或"联合"。

    1. 使用 **"映射实体**"部分将查询结果中的参数链接到 Azure Sentinel 识别的实体。 这些实体构成进一步分析的基础，包括在 **"事件设置"** 选项卡中将警报分组到事件中。
    1. 在 **"查询计划"** 部分中，设置以下参数：

       1. 每次设置**Run 查询**以控制查询的运行频率 - 频率为每 5 分钟，或不常以每天一次的速度运行。

       1. 从**上次设置查找数据**以确定查询涵盖的数据的时间段 - 例如，它可以查询过去 10 分钟的数据或过去 6 小时的数据。

       > [!NOTE]
       > 这两个设置彼此独立，最多一点。 可以在短时间内运行查询，覆盖的时间周期比间隔长（实际上具有重叠查询），但不能在超过覆盖率的间隔内运行查询，否则整个查询覆盖率将存在差距。

    1. 使用 **"警报阈值"** 部分定义基线。 例如，将查询结果数设置为**大于"为大于"****时生成警报**，如果希望规则仅在每次运行时生成 1000 多个结果时生成警报，则设置"生成警报"，并输入数字 1000。 由于这是必填字段，因此，如果您不想设置基线（即，如果希望警报注册每个事件），请在数字字段中输入 0。

    1. 在 **"抑制"** 部分中，您可以在**生成警报后打开"停止运行"查询**，设置 **"** 如果一旦收到警报，则要暂停此规则的操作超过查询间隔的时间段。 如果打开此选项，则必须将 **"停止运行查询"设置为**查询应停止运行的时间，最多 24 小时。

1. 在"**事件设置"** 选项卡中，您可以选择 Azure 哨兵是否以及如何将警报转换为可操作事件。 如果此选项卡单独保留，Azure Sentinel 将创建与每个警报分开的单一事件。 通过更改此选项卡中的设置，您可以选择不创建事件，或将多个警报分组到单个事件中。

    1. 在 **"事件设置"** 部分中，从**此分析规则触发的警报创建事件**默认设置为**启用**，这意味着 Azure Sentinel 将从规则触发的每个警报创建单个单独的事件。<br></br>如果不希望此规则导致创建任何事件（例如，如果此规则只是为了收集信息以便后续分析），则将此规则设置为 **"已禁用**"。

    1. 在 **"警报分组**"部分中，如果希望从一组类似或定期警报生成单个事件，则**将由此分析规则触发的组相关警报设置为"****已启用"，** 并设置以下参数。

    1. **将组限制为在选定时间范围内创建的警报**：<br></br> 确定将类似或定期警报分组在一起的时间范围。 此时间段内的所有相应警报将共同生成事件或一组事件（具体取决于下面的分组设置）。 此时间范围以外的警报将生成单独的事件或一组事件。

    2. 通过：选择将警报分组在一起的基础：将**此分析规则触发的警报分组到单个事件中**：

        - **如果所有实体都匹配，则将警报分组到单个事件**中： <br></br>如果警报为每个映射的实体共享相同的值（在上面的"设置规则逻辑"选项卡中定义），则它们将分组在一起。 建议使用此设置。

        - **将此规则触发的所有警报分组到单个事件中**： <br></br>此规则生成的所有警报都分组在一起，即使它们没有共享相同的值。

        - **如果所选实体匹配，则将警报分组到单个事件**中： <br></br>如果警报共享某些映射实体的相同值（您可以从下拉列表中选择），则将组合在一起。 例如，如果要基于源或目标 IP 地址创建单独的事件，则可能需要使用此设置。

    3. **重新打开已关闭的匹配事件**：如果事件已关闭（意味着基础问题已解决），随后将生成另一个警报，该警报将分组到该事件中，如果希望重新打开已关闭的事件，请将此设置设置为 **"已启用"，** 如果希望警报创建新事件，则保留为 **"已禁用**"。

1. 在 **"自动响应**"选项卡中，选择自定义规则生成警报时要自动运行的任何行动手册。 有关创建和自动化行动手册的详细信息，请参阅 [响应威胁](tutorial-respond-threats-playbook.md)。

1. 选择 **"查看并创建**"以查看新警报规则的所有设置，然后选择 **"创建"以初始化警报规则**。
  
1. 创建警报后，在**活动规则**下向表添加自定义规则。 在此列表中，您可以启用、禁用或删除每个规则。

1. 要查看您创建的警报规则的结果，请转到 **"事件"** 页，您可以在其中会审、 [调查事件](tutorial-investigate-cases.md)并修复威胁。


> [!NOTE]
> 在 Azure 哨兵中生成的警报可通过 [Microsoft 图形安全](https://aka.ms/securitygraphdocs)版获得。 有关详细信息，请参阅 Microsoft [图形安全警报文档](https://aka.ms/graphsecurityreferencebetadocs)。

## <a name="next-steps"></a>后续步骤

在本教程中，您学习了如何使用 Azure Sentinel 开始检测威胁。

要了解如何自动响应威胁，请 [在 Azure Sentinel 中设置自动威胁响应](tutorial-respond-threats-playbook.md)。

