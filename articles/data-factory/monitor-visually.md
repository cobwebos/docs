---
title: 以可视化方式监视 Azure 数据工厂 | Microsoft Docs
description: 了解如何以视觉方式监视 Azure 数据工厂
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
ms.openlocfilehash: f7c27bde3806684045bc43f8ff99eefb14c8d04a
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029139"
---
# <a name="visually-monitor-azure-data-factories"></a>以可视化方式监视 Azure 数据工厂
Azure 数据工厂是基于云的数据集成服务。 可以使用它在云中创建数据驱动型工作流，以便协调和自动化数据移动和数据转换。 使用 Azure 数据工厂，可以：

- 创建和计划数据驱动型工作流（称为管道），以便从不同的数据存储引入数据。
- 使用计算服务（例如 Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics 和 Azure 机器学习）处理/转换数据。
- 将输出数据发布到数据存储（例如 Azure SQL 数据仓库），供商业智能 (BI) 应用程序使用。

在本快速入门中，你将学习如何在不需要编写任何代码的情况下以可视化方式监视数据工厂管道。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="monitor-data-factory-pipelines"></a>监视数据工厂管道

使用简单的列表视图界面监视管道和活动运行。 所有运行都显示在浏览器的本地时区中。 如果更改时区，则所有日期/时间字段都将与所选的字段对齐。  

1. 启动 Microsoft Edge 或 Google Chrome。 目前，只有这两个 web 浏览器支持数据工厂 UI。
2. 登录到[Azure 门户](https://portal.azure.com/)。
3. 在 Azure 门户中，请切换到所创建的数据工厂的边栏选项卡。 选择 "**监视" & "管理**" 磁贴以启动数据工厂视觉对象监视体验。

## <a name="monitor-pipeline-runs"></a>监视管道运行
列表视图显示数据工厂管道的每个管道运行。 其中包括以下各列：

| **列名** | **说明** |
| --- | --- |
| 管道名称 | 管道的名称 |
| 操作 | 可用于查看活动运行的单一操作 |
| 运行开始时间 | 管道运行的开始日期和时间（MM/DD/YYYY，HH： MM： SS AM/PM） |
| Duration | 运行持续时间 (HH:MM:SS) |
| 触发者 | 手动触发器或计划触发器 |
| 状态 | **失败**、**成功**或**正在进行** |
| Parameters | 管道运行的参数（名称/值对） |
| Error | 管道运行错误（如果有） |
| 运行 ID | 管道运行的 ID |

![用于监视管道运行的列表视图](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>监视活动运行
列表视图显示对应于每个管道运行的活动运行。 若要查看每个管道运行的活动运行，请选择 "**操作**" 列下的 "**活动运行**" 图标。 列表视图包含以下各列：

| **列名** | **说明** |
| --- | --- |
| 活动名称 | 管道内活动的名称 |
| 活动类型 | 活动类型，例如**Copy**、 **HDInsightSpark**或**HDInsightHive** |
| 运行开始时间 | 活动运行的开始日期和时间（MM/DD/YYYY，HH： MM： SS AM/PM） |
| Duration | 运行持续时间 (HH:MM:SS) |
| 状态 | **失败**、**成功**或**正在进行** |
| 输入 | 描述活动输入的 JSON 数组 |
| Output | 描述活动输出的 JSON 数组 |
| Error | 活动运行错误（如果有） |

![用于监视活动运行的列表视图](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> 需要选择顶部的 "**刷新**" 按钮来刷新管道和活动运行的列表。 当前不支持自动刷新。

![刷新按钮](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>选择要监视的数据工厂
将鼠标悬停在左上角的 "**数据工厂**" 图标上。 选择箭头图标以查看可监视的 azure 订阅和数据工厂的列表。

![选择数据工厂](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>配置列表视图

### <a name="apply-rich-ordering-and-filtering"></a>应用丰富的排序和筛选

根据运行开始时间，订单管道在 DESC/ASC 中运行。 使用以下各列来运行筛选器管道：

| **列名** | **说明** |
| --- | --- |
| 管道名称 | 管道的名称。 选项包括**过去24小时**、**上周**和**最近30天**的快速筛选器。 或选择一个自定义日期和时间。 |
| 运行开始时间 | 管道运行的开始日期和时间。 |
| 运行状态 | 按状态运行筛选器：已**成功**、已**失败**或**正在进行**。 |

![用于筛选的选项](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>添加或删除列
右键单击列表视图标题，然后选择要在列表视图中显示的列。

![列选项](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>调整列宽
通过将鼠标悬停在列标题上，增加并减小列表视图中的列宽。

## <a name="promote-user-properties-to-monitor"></a>提升要监视的用户属性

可以将任何管道活动属性提升为用户属性，使其成为可以监视的实体。 例如，可以将管道中复制活动的**源**和**目标**属性提升为用户属性。 你还可以选择 "**自动生成**" 以生成复制活动的**源**和**目标**用户属性。

![创建用户属性](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> 只能将最多五个管道活动属性提升为用户属性。

创建用户属性后，可以在 "监视" 列表视图中对其进行监视。 如果复制活动的源是表名称，则可以在活动运行的列表视图中监视源表名称作为列。

![没有用户属性的活动运行列表](media/monitor-visually/monitor-user-properties-image2.png)

![将用户属性列添加到活动运行列表](media/monitor-visually/monitor-user-properties-image3.png)

![具有用户属性的列的活动运行列表](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>重新运行管道中的活动

现在可以重新运行管道中的活动。 选择 "**查看活动运行**"，然后在管道中选择要从中重新运行管道的点的活动。

![查看活动运行](media/monitor-visually/rerun-activities-image1.png)

![选择一个活动运行](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>查看重新运行历史记录

可以查看列表视图中所有管道运行的重新运行历史记录。

![查看历史记录](media/monitor-visually/rerun-history-image1.png)

还可以查看特定管道运行的重新运行历史记录。

![查看管道运行的历史记录](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>甘特图视图

使用甘特图视图可快速可视化管道和活动运行。 您可以查看每个管道的甘特图视图，也可以按您在管道上创建的注释/标记进行分组。

![甘特图的示例](media/monitor-visually/gantt1.png)

![甘特图批注](media/monitor-visually/gantt2.png)

条的长度指示管道的持续时间。 你还可以选择栏来查看更多详细信息。

![甘特图持续时间](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>引导式演示
选择左下角的 "**信息**" 图标。 然后选择 "**引导式教程**"，获取有关如何监视管道和活动运行的分步说明。

![引导式演示](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>反馈
选择 "**反馈**" 图标可向我们提供有关各种功能或你可能面临的任何问题的反馈。

![反馈](media/monitor-visually/feedback.png)

## <a name="alerts"></a>警报

可在数据工厂中发出有关受支持指标的警报。 若要开始，请在 "数据工厂监视" 页上选择 "**监视** > **警报" & 指标**。

![数据工厂监视器页](media/monitor-visually/alerts01.png)

有关此功能的 7 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>创建警报

1.  选择 "**新建警报规则**" 以创建新的警报。

    !["新建警报规则" 按钮](media/monitor-visually/alerts02.png)

1.  指定规则名称并选择警报严重性。

    ![规则名称和严重性的框](media/monitor-visually/alerts03.png)

1.  选择 "警报条件"。

    ![目标条件的框](media/monitor-visually/alerts04.png)

    ![条件列表](media/monitor-visually/alerts05.png)

1.  配置警报逻辑。 可以针对所选的指标为所有管道和对应的活动创建警报。 你还可以选择特定的活动类型、活动名称、管道名称或故障类型。

    ![用于配置警报逻辑的选项](media/monitor-visually/alerts06.png)

1.  为警报配置电子邮件、短信、推送和语音通知。 为警报通知创建操作组，或选择现有操作组。

    ![用于配置通知的选项](media/monitor-visually/alerts07.png)

    ![用于添加通知的选项](media/monitor-visually/alerts08.png)

1.  创建警报规则。

    ![用于创建警报规则的选项](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>后续步骤

若要了解有关监视和管理管道的信息，请参阅[以编程方式监视和管理管道](https://docs.microsoft.com/azure/data-factory/monitor-programmatically)一文。
