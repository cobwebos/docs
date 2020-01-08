---
title: 直观监视 Azure 数据工厂
description: 了解如何以视觉方式监视 Azure 数据工厂
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: 85b1d6b532ba11819947558226291e62af6b5119
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2020
ms.locfileid: "75690931"
---
# <a name="visually-monitor-azure-data-factory"></a>直观监视 Azure 数据工厂

在 Azure 数据工厂中创建并发布管道后，可以将其与触发器相关联，或手动启动即席运行。 可以在 Azure 数据工厂用户体验中以本机方式监视所有管道运行情况。 若要打开监视体验，请在[Azure 门户](https://portal.azure.com/)的 "数据工厂" 边栏选项卡中选择 "**监视" & "管理**" 磁贴。 如果已进入 ADF UX，请单击左侧边栏上的 "**监视器**" 图标。

所有数据工厂运行都显示在浏览器的本地时区中。 如果更改时区，则所有日期/时间字段都将与所选的字段对齐。

## <a name="monitor-pipeline-runs"></a>监视管道运行

默认监视视图为所选时间段内的管道运行列表。 显示以下列：

| **列名** | **说明** |
| --- | --- |
| 管道名称 | 管道的名称 |
| 操作 | 允许你查看活动详细信息、取消或重新运行管道的图标 |
| 运行开始时间 | 管道运行的开始日期和时间（MM/DD/YYYY，HH： MM： SS AM/PM） |
| 持续时间 | 运行持续时间 (HH:MM:SS) |
| 触发者 | 启动管道的触发器的名称 |
| 状态 | **失败**、已**成功**、**正在进行**、**已取消或已** **排队** |
| 批注 | 与管道关联的可筛选标记  |
| 参数 | 管道运行的参数（名称/值对） |
| 错误 | 如果管道失败，则运行错误 |
| 运行 ID | 管道运行的 ID |

![用于监视管道运行的列表视图](media/monitor-visually/pipeline-runs.png)

需要手动选择 "**刷新**" 按钮来刷新管道和活动运行的列表。 当前不支持 Autorefresh。

![“刷新”按钮](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>监视活动运行

若要查看每个管道运行的活动运行，请选择 "**操作**" 列下的 "**查看活动运行**" 图标。 列表视图显示对应于每个管道运行的活动运行。

| **列名** | **说明** |
| --- | --- |
| 活动名称 | 管道内活动的名称 |
| 活动类型 | 活动类型，例如**Copy**、 **ExecuteDataFlow**或**AzureMLExecutePipeline** |
| 操作 | 允许你查看 JSON 输入信息、JSON 输出信息或特定于活动的详细监视体验的图标 | 
| 运行开始时间 | 活动运行的开始日期和时间（MM/DD/YYYY，HH： MM： SS AM/PM） |
| 持续时间 | 运行持续时间 (HH:MM:SS) |
| 状态 | **失败**、已**成功**、**正在进行**或**已取消** |
| Integration Runtime | 运行活动的 Integration Runtime |
| 用户属性 | 活动的用户定义属性 |
| 错误 | 如果活动失败，则运行错误 |
| 运行 ID | 活动运行的 ID |

![用于监视活动运行的列表视图](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>提升要监视的用户属性

将任何管道活动属性提升为用户属性，使其成为你所监视的实体。 例如，可以将管道中复制活动的**源**和**目标**属性提升为用户属性。 选择 "**自动生成**"，生成复制活动的**源**和**目标**用户属性。

![创建用户属性](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> 只能将最多五个管道活动属性提升为用户属性。

创建用户属性后，可以在 "监视" 列表视图中对其进行监视。 如果复制活动的源是表名称，则可以在活动运行的列表视图中监视源表名称作为列。

![没有用户属性的活动运行列表](media/monitor-visually/monitor-user-properties-image2.png)

![将用户属性列添加到活动运行列表](media/monitor-visually/monitor-user-properties-image3.png)

![具有用户属性的列的活动运行列表](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>配置列表视图

### <a name="order-and-filter"></a>订单和筛选器

根据运行开始时间，切换管道的运行是降序还是升序。 使用以下各列来运行筛选器管道：

| **列名** | **说明** |
| --- | --- |
| 管道名称 | 按管道名称筛选。 |
| 运行开始时间 |  确定所显示的管道运行的时间范围。 选项包括**过去24小时**、**上周**和**最近30天**的快速筛选器，或选择自定义日期和时间。 |
| 运行状态 | 按状态筛选运行：已**成功**、已**失败**、已**排队**、**已取消**或**正在进行**。 |
| 批注 | 按应用于每个管道的标记筛选 |
| 运行 | 筛选是否要查看重新运行管道 |

![用于筛选的选项](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>添加或删除列
右键单击列表视图标题，然后选择要在列表视图中显示的列。

![列选项](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>调整列宽
通过将鼠标悬停在列标题上，增加并减小列表视图中的列宽。

## <a name="rerun-activities-inside-a-pipeline"></a>重新运行管道中的活动

可以在管道内重新运行活动。 选择 "**查看活动运行**"，然后在管道中选择要从中重新运行管道的点的活动。

![查看活动运行](media/monitor-visually/rerun-activities-image1.png)

![选择一个活动运行](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>从失败的活动重新运行

如果某个活动失败、超时或被取消，您可以通过选择 "**从失败的活动重新运行**" 从该失败活动重新运行该管道。

![重新运行失败的活动](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>查看重新运行历史记录

可以查看列表视图中所有管道运行的重新运行历史记录。

![查看历史记录](media/monitor-visually/rerun-history-image1.png)

还可以查看特定管道运行的重新运行历史记录。

![查看管道运行的历史记录](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>甘特图视图

使用甘特图视图可快速可视化管道和活动运行。

![甘特图的示例](media/monitor-visually/gantt1.png)

您可以查看每个管道的甘特图视图，也可以按您在管道上创建的注释/标记进行分组。

![甘特图批注](media/monitor-visually/gantt2.png)

条的长度指示管道的持续时间。 你还可以选择栏来查看更多详细信息。

![甘特图持续时间](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>引导式演示
选择左下角的 "**信息**" 图标。 然后选择 "**引导式教程**"，获取有关如何监视管道和活动运行的分步说明。

![引导式演示](media/monitor-visually/guided-tours.png)

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
