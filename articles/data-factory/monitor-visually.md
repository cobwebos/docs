---
title: 直观监视 Azure 数据工厂
description: 了解如何直观监视 Azure 数据工厂
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: 44a95430ece9c55bb088ae5b968c33691f25b4c5
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860736"
---
# <a name="visually-monitor-azure-data-factory"></a>直观监视 Azure 数据工厂

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在 Azure 数据工厂中创建并发布管道后，就可以将它与触发器相关联，或手动启动临时运行。 可以在 Azure 数据工厂用户体验中以本机方式监视所有管道运行。 若要打开监视体验，请选择 [Azure 门户](https://portal.azure.com/)的“数据工厂”边栏选项卡中的“监视和管理”磁贴。 如果已进入 ADF 用户体验，请单击左侧边栏上的“监视”图标。

显示的所有数据工厂运行都采用浏览器的本地时区。 如果你更改时区，所有日期/时间字段都会与你选择的时区保持一致。

## <a name="monitor-pipeline-runs"></a>监视管道运行

默认监视视图是在所选时间段内的管道运行的列表。 显示以下列：

| **列名** | **说明** |
| --- | --- |
| 管道名称 | 管道的名称 |
| 操作 | 可便于查看活动详细信息、取消或重新运行管道的图标 |
| 运行开始时间 | 管道运行的开始日期和时间 (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Duration | 运行持续时间 (HH:MM:SS) |
| 触发者 | 启动管道的触发器的名称 |
| 状态 | “失败”、“成功”、“正在进行”、“已取消”或“已排队” |
| 批注 | 与管道关联的可筛选标记  |
| 参数 | 管道运行参数（名称/值对） |
| 错误 | 管道失败时的运行错误 |
| 运行 ID | 管道运行的 ID |

![用于监视管道运行的列表视图](media/monitor-visually/pipeline-runs.png)

需要手动选择“刷新”按钮来刷新管道运行和活动运行的列表。 暂不支持自动刷新。

![“刷新”按钮](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>监视活动运行

若要查看每个管道运行的活动运行，请选择“操作”列下的“查看活动运行”图标。 此列表视图显示了与每个管道运行对应的活动运行。

| **列名** | **说明** |
| --- | --- |
| 活动名称 | 管道中活动的名称 |
| 活动类型 | 活动类型，如“复制”、“ExecuteDataFlow”或“AzureMLExecutePipeline” |
| 操作 | 可便于查看 JSON 输入信息、JSON 输出信息或使用特定于活动的详细监视体验的图标 | 
| 运行开始时间 | 活动运行的开始日期和时间 (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Duration | 运行持续时间 (HH:MM:SS) |
| 状态 | “失败”、“成功”、“正在进行”或“已取消” |
| Integration Runtime | 其上运行活动的 Integration Runtime |
| 用户属性 | 活动的用户定义属性 |
| 错误 | 活动失败时的运行错误 |
| 运行 ID | 活动运行的 ID |

![用于监视活动运行的列表视图](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>提升要监视的用户属性

将任何管道活动属性提升为用户属性，使之成为你监视的实体。 例如，可以将管道中复制活动的“源”和“目标”属性提升为用户属性。 选择“自动生成”，以为复制活动生成“源”和“目标”用户属性。

![创建用户属性](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> 最多只能将 5 个管道活动属性提升为用户属性。

创建用户属性后，就可以在监视列表视图中监视它们了。 如果复制活动的源是表名称，可以将源表名称作为活动运行列表视图中的列进行监视。

![没有用户属性的活动运行列表](media/monitor-visually/monitor-user-properties-image2.png)

![将用户属性列添加到活动运行列表](media/monitor-visually/monitor-user-properties-image3.png)

![具有用户属性的列的活动运行列表](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>配置列表视图

### <a name="order-and-filter"></a>排序和筛选

切换管道运行是按运行开始时间降序还是升序显示。 使用以下列来筛选管道运行：

| **列名** | **说明** |
| --- | --- |
| 管道名称 | 按管道名称筛选。 |
| 运行开始时间 |  确定显示的管道运行的时间范围。 选项包括快速筛选器选项“过去 24 小时”、“过去一周”和“过去 30 天”，也可以选择自定义日期和时间。 |
| 运行状态 | 按状态筛选运行：“成功”、“失败”、“已排队”、“已取消”或“正在进行”。 |
| 批注 | 按应用于每个管道的标记筛选 |
| 运行次数 | 筛选是否要查看重新运行的管道 |

![筛选选项](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>添加或删除列
右键单击列表视图标题，然后选中要在列表视图中看到的列。

![列选项](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>调整列宽
通过将鼠标悬停在列标题之上，可以增加和减少列表视图中的列宽。

## <a name="rerun-activities-inside-a-pipeline"></a>重新运行管道中的活动

可以重新运行管道中的活动。 依次选择“查看活动运行”和管道中的活动，以从这一点开始重新运行管道。

![查看活动运行](media/monitor-visually/rerun-activities-image1.png)

![选择一个活动运行](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>从失败的活动重新运行

如果活动失败、超时或已取消，可以通过选择“从失败的活动重新运行”，从这一失败的活动重新运行管道。

![重新运行失败的活动](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>查看重新运行历史记录

可以查看列表视图中所有管道运行的重新运行历史记录。

![查看历史记录](media/monitor-visually/rerun-history-image1.png)

还可以查看特定管道运行的重新运行历史记录。

![查看管道运行的历史记录](media/monitor-visually/rerun-history-image2.png)

## <a name="monitor-consumption"></a>监视消耗

通过单击管道运行旁边的消耗图标，可以查看运行消耗的资源。 

![监视消耗](media/monitor-visually/monitor-consumption-1.png)

单击此图标会打开相应管道运行所使用的资源的消耗报告。 

![监视消耗](media/monitor-visually/monitor-consumption-2.png)

可以将这些值插入到 [Azure 定价计算器](https://azure.microsoft.com/pricing/details/data-factory/)中，以估计管道运行的成本。 若要详细了解 Azure 数据工厂定价，请参阅[了解定价](pricing-concepts.md)。

> [!NOTE]
> 定价计算器返回的这些值是估计值。 它并不反映 Azure 数据工厂将向你收取的确切金额 

## <a name="gantt-views"></a>甘特视图

使用甘特视图可快速可视化管道和活动运行。

![甘特图示例](media/monitor-visually/gantt1.png)

可以查看每个管道的甘特视图，也可以按你已在管道上创建的注释/标记进行分组。

![甘特图注释](media/monitor-visually/gantt2.png)

条形图的长度指明管道的持续时间。 还可以选择条形图来查看更多详细信息。

![甘特图持续时间](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>引导式演示
选择左下角的“信息”图标。 然后，选择“引导式导览”来获取有关如何监视管道运行和活动运行的分步说明。

![引导式演示](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>警报

可在数据工厂中发出有关受支持指标的警报。 在“数据工厂监视”页上，依次选择“监视” > “警报和指标”，以开始操作。

![“数据工厂监视”页](media/monitor-visually/start-page.png)

有关此功能的 7 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>创建警报

1.  选择“新建警报规则”，以新建警报。

    ![“新建警报规则”按钮](media/monitor-visually/new-alerts.png)

1.  指定规则名称，然后选择警报严重性。

    ![用于指定规则名称和警报严重性的框](media/monitor-visually/name-and-severity.png)

1.  选择警报条件。

    ![用于指定目标条件的框](media/monitor-visually/add-criteria-1.png)

    ![条件列表](media/monitor-visually/add-criteria-2.png)

1.  配置警报逻辑。 可以针对所选的指标为所有管道和对应的活动创建警报。 还可以选择特定的活动类型、活动名称、管道名称或失败类型。

    ![用于配置警报逻辑的选项](media/monitor-visually/alert-logic.png)

1.  为警报配置电子邮件通知、短信通知、推送通知和语音通知。 为警报通知创建操作组或选择现有操作组。

    ![用于配置通知的选项](media/monitor-visually/configure-notification-1.png)

    ![用于添加通知的选项](media/monitor-visually/configure-notification-2.png)

1.  创建警报规则。

    ![用于创建警报规则的选项](media/monitor-visually/create-alert-rule.png)

## <a name="next-steps"></a>后续步骤

若要了解如何监视和管理管道，请参阅[以编程方式监视和管理管道](https://docs.microsoft.com/azure/data-factory/monitor-programmatically)一文。
