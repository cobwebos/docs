---
title: 使用 Azure Monitor 创建、查看和管理警报
description: 使用新的统一 Azure 警报体验从一个位置创建、查看和管理指标与日志警报规则。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: a913075c051c6b784495917b7edbd7340254a212
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952624"
---
# <a name="create-view-and-manage-alerts-using-azure-monitor"></a>使用 Azure Monitor 创建、查看和管理警报  

## <a name="overview"></a>概述
本文介绍如何使用 Azure 门户中新的警报界面设置警报。 警报规则的定义分为三个部分：
- 目标：要监视的特定 Azure 资源
- 条件：特定的条件或逻辑，出现在“信号”中时，应触发操作
- 操作：发送到通知接收方 - 电子邮件、短信、Webhook 等的特定调用。

Azure 警报还提供所有警报规则的统一视图，可让用户在单个位置管理这些规则，包括查看任何未解决的警报。 [Azure 警报 - 概述](monitoring-overview-unified-alerts.md)中详细介绍了该功能。

警报使用术语**日志警报**来描述警报，其中的信号是基于 [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) 或 [Application Insights](../application-insights/app-insights-analytics.md) 的自定义查询。 [新指标警报功能](monitoring-near-real-time-metric-alerts.md)可针对特定 Azure 资源的[多维指标](monitoring-metric-charts.md)发出警报。 此类资源的警报可以使用基于维度的其他筛选器来创建**多维指标警报**。


> [!NOTE]
> 同时，Azure 警报还提供新的增强体验，用于在 Azure 中创建警报。 现有的[警报（经典）](monitoring-overview-alerts.md)体验仍然可用。
>

下面详细介绍有关使用 Azure 警报的分步指南。

## <a name="create-an-alert-rule-with-the-azure-portal"></a>使用 Azure 门户创建警报规则
1. 在[门户](https://portal.azure.com/)中选择“监视器”，然后在“监视器”部分下选择“警报”。  
    ![监视](./media/monitor-alerts-unified/AlertsPreviewMenu.png)

2. 选择“新建警报规则”按钮，在 Azure 中创建新警报。
    ![添加警报](./media/monitor-alerts-unified/AlertsPreviewOption.png)

3. 此时会显示“创建警报”部分，其中包括三个组成部分：“定义警报条件”、“定义警报详细信息”和“定义操作组”。

    ![创建规则](./media/monitor-alerts-unified/AlertsPreviewAdd.png)

4.  定义警报条件：使用“选择资源”链接，然后通过选择资源来指定目标。 进行筛选：选择“订阅”和“资源类型”，最后选择所需的资源。

    >[!NOTE]

    > 在继续操作之前，请验证可用于所选资源的信号。

    ![选择资源](./media/monitor-alerts-unified/Alert-SelectResource.png)

 在用户界面中，可从一个位置创建各种类型的警报。 根据所需的警报类型选择以下步骤：

    - 对于**指标警报**：执行步骤 5 到 7；然后直接转到步骤 11
    - 对于**日志警报**，跳转到步骤 8。

    > [!NOTE]

    > 活动日志警报也受支持，但以预览版提供。 [了解详细信息](monitoring-activity-log-alerts-new-experience.md)。

5. *指标警报*：确保已选择资源类型，且信号类型为“指标”；选择适当的“资源”后，请单击“完成”按钮返回到“创建警报”。 接下来，使用“添加条件”按钮从信号选项列表中选择特定的信号及其监视服务和所列的类型（适用于前面选择的资源）。

    ![选择资源](./media/monitor-alerts-unified/AlertsPreviewResourceSelection.png)

    > [!NOTE]

    >  列出了有监控服务平台且信号类型为“指标”的所有[准实时警报](monitoring-near-real-time-metric-alerts.md)可用资源

6. *指标警报*：选择信号后，可以指定用于触发警报的逻辑。 作为参考，界面中会显示信号的历史数据，以及用于通过“显示历史记录”调整时间范围的选项（从过去六个小时到过去一周）。 打开可视化效果后，可以从显示的“条件”、“聚合”和“阈值”选项中选择“警报逻辑”。 由于提供了逻辑预览，条件将连同信号历史记录一起显示在可视化效果中，以指示何时触发了警报。 最后，通过选择“时间段”选项来指定警报服务要在哪个时限内查找指定的条件，并通过选择“频率”来指警报服务的运行频率。

    ![配置指标的信号逻辑](./media/monitor-alerts-unified/AlertsPreviewCriteria.png)

7. *指标警报*：如果信号是某个指标，则可以使用所述 Azure 资源的多个数据点或维度来筛选警报窗口。 

    a. 从“显示历史记录”下拉列表中选择持续时间，以可视化不同的时间段。 可以选择受支持指标的维度以根据某个时序进行筛选；选择维度是可选操作，最多可以使用五个维度。 

    b. 可以从显示的“条件”、“聚合”和“阈值”选项中选择“警报逻辑”。 由于提供了逻辑预览，条件将连同信号历史记录一起显示在可视化效果中，以指示在过去的哪个时间触发了警报。 

    c. 若要指定持续时间，请选择“时间段”，同时选择“频率”来指定运行警报的频率。

    ![配置多维指标的信号逻辑](./media/monitor-alerts-unified/AlertsPreviewCriteriaMultiDim.png)

8. *日志警报*：确保“资源类型”是分析源（例如 Log Analytics 或 Application Insights），且信号类型为“日志”，并选择相应的资源，然后单击“完成”。 接下来，使用“添加条件”按钮查看适用于该资源的信号选项列表，并针对所选日志监视服务（如 *Log Analytics* 或 *Application Insights*）从信号列表中选择“自定义日志搜索”选项。

   ![选择资源 - 自定义日志搜索](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > 警报列表可以导入分析查询作为信号类型 - **日志（已保存查询）**，如上图所示。 这样用户便能够在 Analytics 中完善查询，然后保存这些查询供将来在警报中使用 - 有关使用保存的查询的详细信息，请参阅[在 Log Analytics 中使用日志搜索](../log-analytics/log-analytics-log-searches.md)或 [Application Insights Analytics 中的共享查询](../log-analytics/log-analytics-overview.md)。 

9.  *日志警报*：选择后，可以在“搜索查询”字段中指定警报查询；如果查询语法不正确，该字段将以红色显示错误。 如果查询语法正确 - 将以图表形式显示指定查询的历史数据供参考，同时显示用于调整时间范围（过去六个小时到过去一周）的选项。

 ![配置警报规则](./media/monitor-alerts-unified/AlertsPreviewAlertLog.png)

 > [!NOTE]

    > 仅当查询结果包含时间详细信息时，才能显示历史数据可视化效果。 如果查询生成了汇总数据或特定列值 - 则以单一绘图的形式显示相同的数据。

    >  对于使用 Application insights 的指标度量类型的日志警报，可以使用“聚合基于”选项指定要使用哪个特定变量对数据进行分组，如下面所示：

    ![“聚合基于”选项](./media/monitor-alerts-unified/aggregate-on.png)

10.  *日志警报*：打开可视化效果后，可以从显示的“条件”、“聚合”和“阈值”选项中选择“警报逻辑”。 最后，使用“时间段”选项在逻辑中指定评估指定条件的时间。 此外，通过选择“频率”来指定运行警报服务的频率。
**日志警报**可以基于：
   - *记录数目*：如果查询返回的记录计数大于或小于提供的值，则创建警报。
   - *指标度量*：如果结果中的每个聚合值超过提供的阈值并且是“分组依据”选定值，则创建警报。 警报违规数是在选定时间段内超过阈值的次数。 可以为结果集中的任何违规组合指定总违规数，或指定连续违规数以要求违规必须在连续采样时发生。 详细了解[日志警报及其类型](monitor-alerts-unified-log.md)。

    > [!TIP]
    > 目前，在警报中，日志搜索警报可以使用自定义时间段和频率值（分钟）。 值的范围为 5 分钟到 1440 分钟（即 24 小时）。 因此，若要使用小时作为警报时间段（例如 3 小时），需要将其转换为分钟（180 分钟）

11. 第二个步骤是在“警报规则名称”字段中定义警报的名称，提供**说明**用于详细描述该警报的具体信息，并从提供的选项中指定“严重性”值。 在 Azure Monitor 发送的所有警报电子邮件、通知或推送内容中，将重用这些详细信息。 此外，用户可以通过相应地切换“创建后启用规则”选项，选择在创建后立即激活该警报规则。

    在警报详细信息中可以使用一些附加的功能（仅适用于**日志警报**）：

    - **阻止警报**：如果打开警报规则的阻止功能，则新建警报之后会在定义的时间段内禁用该规则的操作。 此规则仍在运行中，并且会在满足条件的情况下创建警报记录。 这是为了让你有时间更正问题，而无需运行重复操作。

        ![对日志警报禁止显示警报](./media/monitor-alerts-unified/AlertsPreviewSuppress.png)

        > [!TIP]
        > 指定的禁止显示警报值应大于警报的频率，以确保在没有重叠的情况下停止通知

12. 第三个步骤（也是最后一个步骤）是指定在满足警报条件的情况下，是否需要对警报规则触发任何**操作组**。 可以选择包含警报的任何现有操作组，也可以创建新的操作组。 根据选定的操作组，触发警报时，Azure 将会：发送电子邮件、发送短信、调用 Webhook、使用 Azure Runbook 进行补救、推送到 ITSM 工具，等等。 详细了解[操作组](monitoring-action-groups.md)。

    对于**日志警报**，提供了一些附加功能用于替代默认操作：

    - **电子邮件通知**：如果所述操作组中存在一个或多个电子邮件操作，替代通过操作组发送的电子邮件中的电子邮件主题。 无法修改邮件正文，并且该字段不能用于电子邮件地址。
    - **包括自定义 Json 有效负载**：如果所述操作组中存在一个或多个 Webhook 操作，替代操作组所使用的 Webhook JSON。 用户可以指定所有在关联的操作组中配置的 Webhook 所使用的 JSON 格式；有关 Webhook 格式的详细信息，请参阅[针对日志警报的 Webhook 操作](monitor-alerts-unified-log-webhook.md)。 提供了“测试 Webhook”选项，用于使用示例 JSON 检查格式和目标的处理，并且此选项已标记为仅用于**测试**目的。

        ![日志警报的操作替代](./media/monitor-alerts-unified/AlertsPreviewOverrideLog.png)

        > [!NOTE]
        > 要使“测试 Webhook”选项有效，终结点应该支持[跨源资源共享 (CORS)](https://www.w3.org/TR/cors/)，用户可以使用 CORS 代理绕过“无 Access-Control-Allow-Origin 标头”问题

13. 如果所有字段有效并且附带绿色的勾选标记，则可以单击“创建警报规则”按钮，在“Azure Monitor - 警报”中创建警报。 可以从警报仪表板查看所有警报。

    ![创建规则](./media/monitor-alerts-unified/AlertsPreviewCreate.png)

    几分钟后，警报将处于活动状态，并按前面所述进行触发。

## <a name="view-your-alerts-in-azure-portal"></a>在 Azure 门户中查看警报

1. 在[门户](https://portal.azure.com/)中选择“监视器”，然后在“监视器”部分下选择“警报”。  

2. 此时将显示**警报仪表板**，其中的的所有 Azure 警报都排列在统一位置，并显示在单个![警报仪表板](./media/monitoring-alerts-unified-usage/alerts-preview-overview.png)中
3. 在仪表板中，从左上方到右上方显示的元素概览如下 - 单击某个元素可以查看详细列表：
    - *触发的警报*：当前满足逻辑且处于触发状态的警报数
    - *警报规则总数*：创建的警报规则数，辅助文本中还显示了当前已启用的警报数 
    

        > [!NOTE]
        > 确保一致的仪表板包含有关触发的警报的所有详细信息，包括 Application Insights 和 Log Analytics 的日志警报，应使用[增强的统一警报（预览版）](monitoring-overview-unified-alerts.md#enhanced-unified-alerts-public-preview)
  
  
4. 将显示所有已触发的警报列表，用户单击其中的某个警报可以查看详细信息
5. 用于帮助查找特定警报的信息；可以使用顶部的下拉选项来筛选特定的订阅、资源组和/或资源。 此外，对于任何未解决的警报，可以使用“筛选警报”选项来查找提供的关键字 - 使用“名称”、“警报条件”、“资源组”和“目标资源”进行具体的警报匹配

## <a name="managing-your-alerts-in-azure-portal"></a>在 Azure 门户中管理警报
1. 在[门户](https://portal.azure.com/)中选择“监视器”，然后在“监视器”部分下选择“警报”。  
2. 在顶部栏中选择“管理规则”按钮，导航到规则管理部分 - 其中列出了创建的所有警报规则，包括已禁用的警报。
3. 若要查找特定的警报规则，可以使用顶部的下拉筛选器，根据特定的订阅、资源组和/或资源搜索警报规则。 或者，可以使用警报规则列表上面标有“筛选警报”的搜索窗格并提供关键字，以仅显示与警报名称、条件和目标资源匹配的规则。
   ![警报管理规则](./media/monitoring-alerts-unified-usage/alerts-preview-rules.png)
4. 若要查看或修改特定的警报规则，请单击其名称（显示为可单击的链接）。
5. 随后将以包含以下三个阶段的结构显示定义的警报：1) 警报条件，2) 警报详细信息，3) 操作组。 可以单击“目标条件”以修改警报逻辑；使用回收站图标删除以前的逻辑之后，可以添加新的条件。 同样，在“警报详细信息”部分，可以修改“说明”和“严重性”。 此外，可以使用“新建操作组”按钮更改操作组，或者新建一个可以链接到警报的操作组。

   ![修改警报规则](./media/monitor-alerts-unified/AlertsPreviewEdit.png)

6. 使用顶部面板可以确定如何处理对警报所做的更改，选项包括：**保存**：提交对警报所做的任何更改；**放弃**：返回且不提交对警报所做的任何更改；**禁用**：停用警报 - 停用后，该警报不再运行或触发任何操作。 **删除**：从 Azure 中永久删除整个警报规则。

## <a name="next-steps"></a>后续步骤

- 详细了解新的[近实时指标警报](monitoring-near-real-time-metric-alerts.md)
- 获取[指标集合概述](insights-how-to-customize-monitoring.md)以确保服务可用且响应迅速。
- 了解 [Azure 警报中的日志警报](monitor-alerts-unified-log.md)
- [了解警报（预览版）体验中的活动日志警报](monitoring-activity-log-alerts-new-experience.md)
