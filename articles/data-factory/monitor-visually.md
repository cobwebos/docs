---
title: 以可视化方式监视 Azure 数据工厂
description: 了解如何以可视化方式监视 Azure 数据工厂
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 7b79fd9c87e97e624cce567b57c1c65fefcc151e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684637"
---
# <a name="visually-monitor-azure-data-factories"></a>以可视化方式监视 Azure 数据工厂
Azure 数据工厂是基于云的数据集成服务。 可以使用数据工厂在云中创建数据驱动的工作流，用于协调和自动化数据移动和数据转换。 使用 Azure 数据工厂可以：

- 创建和计划数据驱动型工作流（称为管道），以便从不同的数据存储引入数据。
- 使用计算服务（例如 Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics 和 Azure 机器学习）处理/转换数据。
- 将输出数据发布到数据存储（例如 Azure SQL 数据仓库），供商业智能 (BI) 应用程序使用。

在本快速入门中，你将学习如何在不需要编写任何代码的情况下以可视化方式监视数据工厂管道。

如果没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="monitor-data-factory-pipelines"></a>监视数据工厂管道

使用简单的列表视图界面监视管道和活动运行。 所有运行都以浏览器的本地时区显示。 如果更改时区，所有日期/时间字段都将调整为所选的时区。  

1. 启动 Microsoft Edge 或 Google Chrome。 目前，只有这两款 Web 浏览器支持数据工厂 UI。
2. 登录到 [Azure 门户](https://portal.azure.com/)。
3. 在 Azure 门户中，转到创建的数据工厂对应的边栏选项卡。 选择“监视和管理”磁贴启动数据工厂可视监视体验。

## <a name="monitor-pipeline-runs"></a>监视管道运行
列表视图显示数据工厂管道的每个管道运行。 其中包括以下列：

| **列名** | **说明** |
| --- | --- |
| 管道名称 | 管道名称 |
| 操作 | 可用于查看活动运行的单个操作 |
| 运行开始时间 | 管道运行的开始日期和时间（MM/DD/YYYY，HH:MM:SS AM/PM） |
| 持续时间 | 运行持续时间 (HH:MM:SS) |
| 触发者 | 手动触发器或计划触发器 |
| Status | “失败”、“成功”或“正在进行” |
| parameters | 管道运行的参数（名称/值对） |
| 错误 | 管道运行错误（如果有） |
| 运行 ID | 管道运行的 ID |

![用于监视管道运行的列表视图](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>监视活动运行
列表视图显示对应于每个管道运行的活动运行。 若要查看每个管道运行的活动运行，请在“操作”列下选择“活动运行”图标。 列表视图包含以下列：

| **列名** | **说明** |
| --- | --- |
| 活动名称 | 管道中的活动的名称 |
| 活动类型 | 活动类型，例如“复制”、“HDInsightSpark”或“HDInsightHive”。 |
| 运行开始时间 | 活动运行的开始日期和时间（MM/DD/YYYY，HH:MM:SS AM/PM） |
| 持续时间 | 运行持续时间 (HH:MM:SS) |
| Status | “失败”、“成功”或“正在进行” |
| 输入 | 描述活动输入的 JSON 数组 |
| 输出 | 描述活动输出的 JSON 数组 |
| 错误 | 活动运行错误（如果有） |

![用于监视活动运行的列表视图](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> 需要选择顶部的“刷新”按钮来刷新管道和活动运行的列表。 当前不支持自动刷新。

![“刷新”按钮](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>选择要监视的数据工厂
将鼠标指针悬停在左上角的“数据工厂”图标上。 选择箭头图标查看可以监视的 Azure 订阅和数据工厂的列表。

![选择数据工厂](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>配置列表视图

### <a name="apply-rich-ordering-and-filtering"></a>应用丰富的排序和筛选

根据运行开始时间按降序/升序将管道运行排序。 使用以下列来筛选管道运行：

| **列名** | **说明** |
| --- | --- |
| 管道名称 | 管道的名称。 选项包括针对“过去 24 小时”、“过去一周”和“过去 30 天”的快速筛选器。 或者选择自定义日期和时间。 |
| 运行开始时间 | 管道运行的开始日期和时间。 |
| 运行状态 | 按状态筛选运行：已**成功**、已**失败**或**正在进行**。 |

![筛选选项](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>添加或删除列
右键单击列表视图标题，并选择希望在列表视图中显示的列。

![列选项](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>调整列宽
通过将鼠标指针悬停在列标题上来增大或减小列表视图中的列宽。

## <a name="promote-user-properties-to-monitor"></a>提升要监视的用户属性

可以将任何管道活动属性提升为用户属性，使其成为可以监视的实体。 例如，可以将管道中复制活动的**源**和**目标**属性提升为用户属性。 还可以选择“自动生成”，为复制活动生成**源**和**目标**用户属性。

![创建用户属性](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> 最多只能将五个管道活动属性提升为用户属性。

创建用户属性后，便可在监视列表视图中监视它们。 如果复制活动的源是表名，则可以将源表名称作为活动运行列表视图中的列进行监视。

![没有用户属性的活动运行列表](media/monitor-visually/monitor-user-properties-image2.png)

![将用户属性的列添加到活动运行列表](media/monitor-visually/monitor-user-properties-image3.png)

![具有用户属性的列的活动运行列表](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>重新运行管道中的活动

现在可以重新运行管道中的活动。 选择“查看活动运行”并选择要从管道中的活动中的哪个点重新运行管道。

![查看活动运行](media/monitor-visually/rerun-activities-image1.png)

![选择一个活动运行](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>查看重新运行历史记录

可以查看列表视图中所有管道运行的重新运行历史记录。

![查看历史记录](media/monitor-visually/rerun-history-image1.png)

还可以查看特定管道运行的重新运行历史记录。

![查看管道运行的历史记录](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>甘特图

使用甘特图可以快速可视化管道和活动运行。 可以根据在管道中创建的注释/标记，按管道或组查看甘特图。

![甘特图示例](media/monitor-visually/gantt1.png)

![甘特图注释](media/monitor-visually/gantt2.png)

条形长度表示管道持续时间。 还可以选择条形来查看更多详细信息。

![甘特图持续时间](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>引导式演示
选择左下角的“信息”图标。 然后选择“引导式演示”来获取有关如何监视管道和活动运行的分步说明。

![引导式演示](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>反馈
选择“反馈”图标可向我们提供有关各种功能以及你可能遇到的任何问题的反馈。

![反馈](media/monitor-visually/feedback.png)

## <a name="alerts"></a>警报

可在数据工厂中发出有关受支持指标的警报。 若要开始，请在 "数据工厂监视" 页上选择 "**监视** > **警报" & 指标**。

![数据工厂监视页](media/monitor-visually/alerts01.png)

有关此功能的 7 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>创建警报

1.  选择 "**新建警报规则**" 以创建新的警报。

    ![“新建警报规则”按钮](media/monitor-visually/alerts02.png)

1.  指定规则名称，然后选择警报严重性。

    ![规则名称和严重性的框](media/monitor-visually/alerts03.png)

1.  选择警报条件。

    ![目标条件的框](media/monitor-visually/alerts04.png)

    ![条件列表](media/monitor-visually/alerts05.png)

1.  配置警报逻辑。 可以针对所选的指标为所有管道和对应的活动创建警报。 还可以选择特定的活动类型、活动名称、管道名称或故障类型。

    ![用于配置警报逻辑的选项](media/monitor-visually/alerts06.png)

1.  为警报配置电子邮件、短信、推送和语音通知。 为警报通知创建一个操作组，或选择现有的操作组。

    ![用于配置通知的选项](media/monitor-visually/alerts07.png)

    ![用于添加通知的选项](media/monitor-visually/alerts08.png)

1.  创建警报规则。

    ![用于创建警报规则的选项](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>后续步骤

若要了解有关监视和管理管道的信息，请参阅[以编程方式监视和管理管道](https://docs.microsoft.com/azure/data-factory/monitor-programmatically)一文。
