---
title: 以可视化方式监视 Azure 数据工厂
description: 了解如何直观监视 Azure 数据工厂
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: fcbf48081484941fd93f209bee21eb14c2c38310
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570130"
---
# <a name="visually-monitor-azure-data-factory"></a>以可视化方式监视 Azure 数据工厂

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在 Azure 数据工厂中创建并发布管道以后，即可将其与触发器关联，或者手动启动临时运行。 可以在 Azure 数据工厂用户体验中以本机方式监视所有管道运行。 若要打开监视体验，请在 [Azure 门户](https://portal.azure.com/)的数据工厂边栏选项卡中选择“监视和管理”磁贴。 如果已在 ADF UX 中，请单击左侧边栏上的“监视”图标。

所有数据工厂运行都以浏览器的本地时区显示。 如果你更改时区，所有日期/时间字段都会与你选择的时区保持一致。

## <a name="monitor-pipeline-runs"></a>监视管道运行

默认监视视图是所选时段内管道运行的列表。 以下列会显示：

| **列名** | **说明** |
| --- | --- |
| 管道名称 | 管道的名称 |
| 操作 | 图标，用于查看活动详细信息、取消或重新运行管道 |
| 运行开始时间 | 管道运行的开始日期和时间（MM/DD/YYYY，HH:MM:SS AM/PM） |
| 持续时间 | 运行持续时间 (HH:MM:SS) |
| 触发者 | 启动了管道的触发器的名称 |
| 状态 | **失败**、**成功**、**正在进行**、**已取消**或**已排队** |
| 批注 | 与管道关联的可筛选标记  |
| parameters | 管道运行的参数（名称/值对） |
| 错误 | 在管道失败的情况下出现的运行错误 |
| 运行 ID | 管道运行的 ID |

![用于监视管道运行的列表视图](media/monitor-visually/pipeline-runs.png)

需要手动选择“刷新”按钮来刷新管道和活动运行的列表。 当前不支持自动刷新。

![“刷新”按钮](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>监视活动运行

若要查看每个管道运行的活动运行，请在“操作”列下选择“查看活动运行”图标。 此列表视图显示了与每个管道运行对应的活动运行。

| **列名** | **说明** |
| --- | --- |
| 活动名称 | 管道中的活动的名称 |
| 活动类型 | 活动类型，如“复制”、“ExecuteDataFlow”或“AzureMLExecutePipeline” |
| 操作 | 图标，用于查看 JSON 输入信息、JSON 输出信息或详细的特定于活动的监视体验 | 
| 运行开始时间 | 活动运行的开始日期和时间（MM/DD/YYYY，HH:MM:SS AM/PM） |
| 持续时间 | 运行持续时间 (HH:MM:SS) |
| 状态 | **失败**、**成功**、**正在进行**或**已取消** |
| Integration Runtime | 运行活动时所在的 Integration Runtime |
| 用户属性 | 活动的用户定义属性 |
| 错误 | 在活动失败的情况下出现的运行错误 |
| 运行 ID | 活动运行的 ID |

![用于监视活动运行的列表视图](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>提升要监视的用户属性

将任何管道活动属性提升为用户属性，使其成为受监视的实体。 例如，可以将管道中复制活动的“源”和“目标”属性提升为用户属性。 选择“自动生成”，为复制活动生成“源”和“目标”用户属性。

![创建用户属性](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> 最多只能将五个管道活动属性提升为用户属性。

创建用户属性后，便可在监视列表视图中监视它们。 如果复制活动的源是表名，则可以将源表名称作为活动运行列表视图中的列进行监视。

![没有用户属性的活动运行列表](media/monitor-visually/monitor-user-properties-image2.png)

![将用户属性的列添加到活动运行列表](media/monitor-visually/monitor-user-properties-image3.png)

![具有用户属性的列的活动运行列表](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>配置列表视图

### <a name="order-and-filter"></a>排序和筛选

进行切换操作，以便根据运行开始时间按降序或升序来排列管道运行。 使用以下列来筛选管道运行：

| **列名** | **说明** |
| --- | --- |
| 管道名称 | 按管道名称筛选。 |
| 运行开始时间 |  确定所显示的管道运行的时间范围。 选项包括针对“过去 24 小时”、“过去一周”、“过去 30 天”的快速筛选器，还可以选择一个自定义日期和时间。 |
| 运行状态 | 按状态筛选运行：**成功**、**失败**、**已排队**、**已取消**或**正在进行**。 |
| 批注 | 按应用到每个管道的标记进行筛选 |
| 运行次数 | 筛选是否要查看重新运行的管道 |

![筛选选项](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>添加或删除列
右键单击列表视图标题，并选择希望在列表视图中显示的列。

![列选项](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>调整列宽
通过将鼠标悬停在列标题之上，可以增加和减少列表视图中的列宽。

## <a name="rerun-activities-inside-a-pipeline"></a>重新运行管道中的活动

可以重新运行管道中的活动。 依次选择“查看活动运行”和管道中的活动，以从这一点开始重新运行管道。

![查看活动运行](media/monitor-visually/rerun-activities-image1.png)

![选择一个活动运行](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>从失败的活动重新运行

如果某个活动失败、超时或被取消，则可以通过选择“从失败的活动重新运行”来从该失败的活动重新运行管道。

![重新运行失败的活动](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>查看重新运行历史记录

可以查看列表视图中所有管道运行的重新运行历史记录。

![查看历史记录](media/monitor-visually/rerun-history-image1.png)

还可以查看特定管道运行的重新运行历史记录。

![查看管道运行的历史记录](media/monitor-visually/rerun-history-image2.png)

## <a name="monitor-consumption"></a>监视消耗

通过单击管道运行旁边的消耗图标，可以查看运行消耗的资源。 

![屏幕截图显示了管道使用的资源。](media/monitor-visually/monitor-consumption-1.png)

单击此图标会打开相应管道运行所使用的资源的消耗报告。 

![监视消耗](media/monitor-visually/monitor-consumption-2.png)

可以将这些值插入到 [Azure 定价计算器](https://azure.microsoft.com/pricing/details/data-factory/)中，以估计管道运行的成本。 若要详细了解 Azure 数据工厂定价，请参阅[了解定价](pricing-concepts.md)。

> [!NOTE]
> 定价计算器返回的这些值是估计值。 它并不反映 Azure 数据工厂将向你收取的确切金额 

## <a name="gantt-views"></a>甘特视图

使用甘特视图可快速可视化管道和活动运行。

![甘特图示例](media/monitor-visually/gantt1.png)

可以根据在管道上创建的注释/标记，按管道或组查看甘特图。

![甘特图注释](media/monitor-visually/gantt2.png)

条形长度表示管道持续时间。 还可以选择条形来查看更多详细信息。

![甘特图持续时间](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>引导式演示
选择左下角的“信息”图标。 然后选择“引导式演示”来获取有关如何监视管道和活动运行的分步说明。

![引导式演示](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>警报

可在数据工厂中发出有关受支持指标的警报。 在“数据工厂监视”页上，依次选择“监视” > “警报和指标”，以开始操作。

![数据工厂监视页](media/monitor-visually/start-page.png)

有关此功能的 7 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>创建警报

1.  选择“新建警报规则”，以新建警报。

    ![“新建警报规则”按钮](media/monitor-visually/new-alerts.png)

1.  指定规则名称，然后选择警报严重性。

    ![规则名称和严重性的框](media/monitor-visually/name-and-severity.png)

1.  选择警报条件。

    ![目标条件的框](media/monitor-visually/add-criteria-1.png)

    ![屏幕截图，显示你选择一个指标来设置警报条件的位置。](media/monitor-visually/add-criteria-2.png)

    ![条件列表](media/monitor-visually/add-criteria-3.png)

    可以针对各种指标创建警报，包括 ADF 实体计数/大小、活动/管道/触发器运行、Integration Runtime (IR) CPU 利用率/内存/节点计数/队列，以及 SSIS 包执行和 SSIS IR 启动/停止操作的指标。

1.  配置警报逻辑。 可以针对所选的指标为所有管道和对应的活动创建警报。 还可以选择特定的活动类型、活动名称、管道名称或故障类型。

    ![用于配置警报逻辑的选项](media/monitor-visually/alert-logic.png)

1.  为警报配置电子邮件、短信、推送和语音通知。 为警报通知创建一个操作组，或选择现有的操作组。

    ![用于配置通知的选项](media/monitor-visually/configure-notification-1.png)

    ![用于添加通知的选项](media/monitor-visually/configure-notification-2.png)

1.  创建警报规则。

    ![用于创建警报规则的选项](media/monitor-visually/create-alert-rule.png)

## <a name="next-steps"></a>后续步骤

若要了解有关监视和管理管道的信息，请参阅[以编程方式监视和管理管道](https://docs.microsoft.com/azure/data-factory/monitor-programmatically)一文。
