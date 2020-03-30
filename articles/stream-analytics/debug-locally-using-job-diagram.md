---
title: 使用可视化工作室中的作业图在本地调试 Azure 流分析查询
description: 本文介绍如何使用可视化工作室的 Azure 流分析工具中的作业图在本地调试查询。
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 106b1f0b765700803d2cd55b5e049fae5be3dfad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76847194"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>使用可视化工作室中的作业图在本地调试 Azure 流分析查询

输出没有结果或意外结果的作业是流式查询的常见故障排除方案。 您可以在 Visual Studio 中本地测试查询时使用作业图来检查每个步骤的中间结果集和指标。 作业图可以帮助您在排除故障时快速隔离问题的根源。

## <a name="debug-a-query-using-job-diagram"></a>使用作业图调试查询

Azure 流分析脚本用于将输入数据转换为输出数据。 作业图显示数据如何从输入源（事件中心、IoT 中心等）通过多个查询步骤流，最后流到输出接收器。 每个查询步骤都映射到使用`WITH`语句在脚本中定义的临时结果集。 您可以查看每个中间结果集中每个查询步骤的数据和指标，以查找问题的根源。

> [!NOTE]
> 此作业图仅显示单个节点中本地测试的数据和指标。 它不应用于性能调优和故障排除。

### <a name="start-local-testing"></a>开始本地测试

使用此[快速入门](stream-analytics-quick-create-vs.md)了解如何使用 Visual Studio 创建流分析作业或[将现有作业导出到本地项目](stream-analytics-vs-tools.md#export-jobs-to-a-project)。 如果要使用本地输入数据测试查询，请按照这些[说明操作](stream-analytics-live-data-local-testing.md)。 如果要使用实时输入进行测试，则转到下一步。

> [!NOTE]
> 如果将作业导出到本地项目，并且希望针对实时输入流进行测试，则需要再次指定所有输入的凭据。  

从脚本编辑器中选择输入和输出源，然后选择 **"本地运行**"。 作业图显示在右侧。

### <a name="view-the-intermediate-result-set"></a>查看中间结果集  

1. 选择要导航到脚本的查询步骤。 您会自动定向到左侧编辑器中的相应脚本。

   ![作业图导航脚本](./media/debug-locally-using-job-diagram/navigate-script.png)

2. 选择查询步骤，并在弹出对话框中选择 **"预览**"。 结果集显示在底部结果窗口中的选项卡中。

   ![作业图预览结果](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>查看步进指标

在本节中，您将探讨图表每个部分可用的指标。

#### <a name="input-sources-live-stream"></a>输入源（实时流）

![作业图实时输入源](./media/debug-locally-using-job-diagram/live-input.png)

|指标|描述|
|-|-|
|**出租车**| 输入的名称。|
|**事件中心** | 输入源类型。|
|**事件**|读取的事件数。|
|**积压事件源**|事件中心和 IoT 中心输入需要读取多少条消息。|
|**字节中的事件**|已读取的字节数。|
| **降级事件**|除反序列化之外，具有问题的事件计数。|
|**早期活动**| 高水位线之前具有应用程序时间戳的事件数。|
|**后期事件**| 高水位线后具有应用程序时间戳的事件数。|
|**事件源**| 读取的数据单位数。 例如，blob 的数目。|

#### <a name="input-sources-local-input"></a>输入源（本地输入）

![作业图本地输入源](./media/debug-locally-using-job-diagram/local-input.png)

|指标|描述|
|-|-|
|**出租车**| 输入的名称。|
|**行计数**| 从步骤生成的行数。|
|**数据大小**| 从此步骤生成的数据的大小。|
|**本地输入**| 使用本地数据作为输入。|

#### <a name="query-steps"></a>查询步骤

![作业图查询步骤](./media/debug-locally-using-job-diagram/query-step.png)

|指标|描述|
|-|-|
|**行程数据**|临时结果集的名称。|
|**行计数**| 从步骤生成的行数。|
|**数据大小**| 从此步骤生成的数据的大小。|
  
#### <a name="output-sinks-live-output"></a>输出接收器（实时输出）

![作业图本地输出接收器](./media/debug-locally-using-job-diagram/live-output.png)

|指标|描述|
|-|-|
|**地区拉格赫**|输出的名称。|
|**事件**|输出到接收器的事件数。|

#### <a name="output-sinks-local-output"></a>输出接收器（本地输出）

![作业图本地输出接收器](./media/debug-locally-using-job-diagram/local-output.png)

|指标|描述|
|-|-|
|**地区拉格赫**|输出的名称。|
|**本地输出**| 结果输出到本地文件。|
|**行计数**| 输出到本地文件的行数。|
|**数据大小**| 数据输出到本地文件的大小。|

### <a name="close-job-diagram"></a>关闭作业图

如果不再需要作业图，请在右上角选择 **"关闭**"。 关闭关系图窗口后，需要再次启动本地测试才能看到它。

### <a name="view-job-level-metrics-and-stop-running"></a>查看作业级别指标并停止运行

其他作业级别指标显示在弹出控制台中。 如果要停止作业，请按控制台中的**Ctrl+C。**

![作业图停止作业](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>限制

* 由于身份验证模型限制，不支持 Power BI 和 Azure 数据存储库第 1 代输出接收器。

* 只有云输入选项具有[时间策略](stream-analytics-out-of-order-and-late-events.md)支持，本地输入选项不具备此支持。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Visual Studio 创建流分析作业](stream-analytics-quick-create-vs.md)
* [使用 Visual Studio 查看 Azure 流分析作业](stream-analytics-vs-tools.md)
* [使用适用于 Visual Studio 的 Azure 流分析工具在本地测试实时数据（预览版）](stream-analytics-live-data-local-testing.md)
