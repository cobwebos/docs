---
title: 以可视化方式监视 Azure 数据工厂 | Microsoft Docs
description: 了解如何以可视化方式监视 Azure 数据工厂
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2018
ms.author: shlo
ms.openlocfilehash: e654cc23d6a558469ea238fc5ade82b44562f9a2
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050363"
---
# <a name="visually-monitor-azure-data-factories"></a>以可视化方式监视 Azure 数据工厂
Azure 数据工厂是基于云的数据集成服务，用于在云中创建数据驱动型工作流，以便协调和自动完成数据移动和数据转换。 使用 Azure 数据工厂，可以创建和计划数据驱动型工作流（称为管道），以便从不同的数据存储引入数据，通过各种计算服务（例如 Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics 和 Azure 机器学习）处理/转换数据，将输出数据发布到数据存储（例如 Azure SQL 数据仓库），供商业智能 (BI) 应用程序使用。
在此快速入门教程中，你将学习如何在不需要编写任何代码的情况下以可视化方式监视数据工厂 v2 管道。
如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="monitor-data-factory-v2-pipelines"></a>监视数据工厂 v2 管道

1. 启动 **Microsoft Edge** 或 **Google Chrome** Web 浏览器。 目前，仅 Microsoft Edge 和 Google Chrome Web 浏览器支持数据工厂 UI。
2. 登录到 [Azure 门户](https://portal.azure.com/)。
3. 在 Azure 门户中，导航到所创建的数据工厂边栏选项卡并单击“监视和管理”磁贴。 这将启动 ADF v2 可视化监视体验。

## <a name="list-view-monitoring"></a>列表视图监视

使用简单的列表视图界面监视管道和活动运行。 所有运行都以本地浏览器时区显示。 你可以更改时区，所有日期时间字段都将调整到所选的时区。  

#### <a name="monitoring-pipeline-runs"></a>监视管道运行
以下列表视图展示了数据工厂 v2 管道的每个管道运行。 包括的列：

| **列名称** | **说明** |
| --- | --- |
| 管道名称 | 管道的名称。 |
| 操作 | 可用于查看活动运行的单个操作。 |
| 运行开始时间 | 管道运行的开始日期时间（MM/DD/YYYY，HH:MM:SS AM/PM） |
| Duration | 运行持续时间 (HH:MM:SS) |
| 触发者 | 手动触发、计划触发 |
| 状态 | 已失败、已成功、正在进行 |
| parameters | 管道运行参数（名称-值对） |
| 错误 | 管道运行错误（如果有） |
| 运行 ID | 管道运行的 ID |

![监视管道运行](media/monitor-visually/pipeline-runs.png)

#### <a name="monitoring-activity-runs"></a>监视活动运行
以下列表视图展示了与每个管道运行对应的活动运行。 单击“操作”列下的“活动运行”图标可查看每个管道运行的活动运行。 包括的列：

| **列名称** | **说明** |
| --- | --- |
| 活动名称 | 管道中的活动的名称。 |
| 活动类型 | 活动类型，即复制、HDInsightSpark、HDInsightHive，等等。 |
| 运行开始时间 | 活动运行的开始日期时间（MM/DD/YYYY，HH:MM:SS AM/PM） |
| Duration | 运行持续时间 (HH:MM:SS) |
| 状态 | 已失败、已成功、正在进行 |
| 输入 | 描述了活动输入的 JSON 数组 |
| 输出 | 描述了活动输出的 JSON 数组 |
| 错误 | 活动运行错误（如果有） |

![监视活动运行](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> 需要单击顶部的“刷新”图标来刷新管道和活动运行的列表。 当前不支持自动刷新。
>

![刷新](media/monitor-visually/refresh.png)

## <a name="features"></a>功能

#### <a name="rich-ordering-and-filtering"></a>丰富的排序和筛选

按运行开始时间以降序/升序排列管道运行，并按以下列筛选管道运行：

| **列名称** | **说明** |
| --- | --- |
| 管道名称 | 管道的名称。 选项包括针对“过去 24 小时”、“过去一周”、“过去 30 天”的快速筛选器，还可以选择一个自定义日期时间。 |
| 运行开始时间 | 管道运行的开始日期时间 |
| 运行状态 | 按状态（即，已成功、已失败、正在进行）筛选运行 |

![筛选器](media/monitor-visually/filter.png)

#### <a name="addremove-columns-to-list-view"></a>在列表视图中添加/删除列
右键单击列表视图标题，并选择希望在列表视图中显示的列

![列](media/monitor-visually/columns.png)

#### <a name="reorder-column-widths-in-list-view"></a>重新安排列表视图中的列宽
通过将鼠标指针悬停在列标题上来增大或减小列表视图中的列宽

#### <a name="select-data-factory"></a>选择数据工厂
将鼠标指针悬停在左上角的“数据工厂”图标上。 单击“箭头”图标以查看可以监视的 azure 订阅和数据工厂的列表。

![选择数据工厂](media/monitor-visually/select-datafactory.png)

#### <a name="guided-tours"></a>引导式演示
单击左下角的“信息图标”并单击“引导式演示”来获取有关如何监视管道和活动运行的分步说明。

![引导式演示](media/monitor-visually/guided-tours.png)

#### <a name="feedback"></a>反馈
单击“反馈”图标可向我们提供有关各种功能以及你可能遇到的任何问题的反馈。

![反馈](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>后续步骤

参阅[以编程方式监视和管理管道](https://docs.microsoft.com/azure/data-factory/monitor-programmatically)一文，了解有关监视和管理管道的信息
