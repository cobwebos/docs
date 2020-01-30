---
title: 在 Visual Studio 中使用作业关系图在本地调试 Azure 流分析查询
description: 本文介绍如何使用适用于 Visual Studio 的 Azure 流分析工具中的作业关系图在本地调试查询。
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 106b1f0b765700803d2cd55b5e049fae5be3dfad
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847194"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>在 Visual Studio 中使用作业关系图在本地调试 Azure 流分析查询

不输出结果或意外结果的作业是流式处理查询的常见故障排除方案。 在 Visual Studio 中本地测试查询时，可以使用作业关系图检查每个步骤的中间结果集和指标。 作业关系图可以帮助你在解决问题时迅速找出问题的根源。

## <a name="debug-a-query-using-job-diagram"></a>使用作业关系图调试查询

Azure 流分析脚本用于将输入数据转换为输出数据。 作业关系图显示了数据如何通过多个查询步骤从输入源（事件中心、IoT 中心等）流动，最后再到输出接收器。 每个查询步骤都映射到使用 `WITH` 语句在脚本中定义的临时结果集。 您可以查看每个中间结果集中每个查询步骤的数据和指标，以找出问题的根源。

> [!NOTE]
> 此作业关系图只显示单个节点中的本地测试数据和指标。 不应将其用于性能优化和故障排除。

### <a name="start-local-testing"></a>启动本地测试

使用本[快速入门](stream-analytics-quick-create-vs.md)教程了解如何使用 Visual Studio 创建流分析作业，或将[现有作业导出到本地项目](stream-analytics-vs-tools.md#export-jobs-to-a-project)中。 如果要测试包含本地输入数据的查询，请遵循这些[说明](stream-analytics-live-data-local-testing.md)。 如果要测试实时输入，请转到下一步。

> [!NOTE]
> 如果将作业导出到本地项目并想要针对实时输入流进行测试，则需要再次为所有输入指定凭据。  

从脚本编辑器中选择输入和输出源，并选择 "**本地运行**"。 作业关系图将显示在右侧。

### <a name="view-the-intermediate-result-set"></a>查看中间结果集  

1. 选择要导航到脚本的查询步骤。 系统会自动将您定向到左侧编辑器中的相应脚本。

   ![作业关系图导航脚本](./media/debug-locally-using-job-diagram/navigate-script.png)

2. 选择查询步骤，然后在弹出对话框中选择 "**预览**"。 结果集将显示在底部结果窗口的选项卡中。

   ![作业关系图预览结果](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>查看步骤指标

本部分介绍可用于关系图的每个部分的指标。

#### <a name="input-sources-live-stream"></a>输入源（实时流）

![作业关系图实时输入源](./media/debug-locally-using-job-diagram/live-input.png)

|度量值|Description|
|-|-|
|**TaxiRide**| 输入的名称。|
|**事件中心** | 输入源类型。|
|**事件**|读取的事件数。|
|**囤积的事件源**|需要为事件中心和 IoT 中心输入读取多少条消息。|
|**事件（字节）**|已读取的字节数。|
| **降级的事件**|出现除反序列化之外的问题的事件计数。|
|**前期事件**| 在高水印之前具有应用程序时间戳的事件的数目。|
|**后期事件**| 在高水印后具有应用程序时间戳的事件的数目。|
|**事件源**| 读取的数据单元数。 例如，blob 的数目。|

#### <a name="input-sources-local-input"></a>输入源（本地输入）

![作业关系图本地输入源](./media/debug-locally-using-job-diagram/local-input.png)

|度量值|Description|
|-|-|
|**TaxiRide**| 输入的名称。|
|**行计数**| 从步骤生成的行数。|
|**数据大小**| 此步骤生成的数据的大小。|
|**本地输入**| 使用本地数据作为输入。|

#### <a name="query-steps"></a>查询步骤

![作业关系图查询步骤](./media/debug-locally-using-job-diagram/query-step.png)

|度量值|Description|
|-|-|
|**TripData**|临时结果集的名称。|
|**行计数**| 从步骤生成的行数。|
|**数据大小**| 此步骤生成的数据的大小。|
  
#### <a name="output-sinks-live-output"></a>输出接收器（实时输出）

![作业关系图本地输出接收器](./media/debug-locally-using-job-diagram/live-output.png)

|度量值|Description|
|-|-|
|**regionaggEH**|输出的名称。|
|**事件**|输出到接收器的事件数。|

#### <a name="output-sinks-local-output"></a>输出接收器（本地输出）

![作业关系图本地输出接收器](./media/debug-locally-using-job-diagram/local-output.png)

|度量值|Description|
|-|-|
|**regionaggEH**|输出的名称。|
|**本地输出**| 结果输出到本地文件。|
|**行计数**| 输出到本地文件的行数。|
|**数据大小**| 向本地文件输出的数据的大小。|

### <a name="close-job-diagram"></a>关闭作业关系图

如果不再需要此作业关系图，请选择右上角的 "**关闭**"。 关闭关系图窗口后，需要再次启动本地测试才能看到它。

### <a name="view-job-level-metrics-and-stop-running"></a>查看作业级指标并停止运行

其他作业级别度量值显示在弹出窗口中。 如果要停止作业，请在控制台中按**Ctrl + C** 。

![作业关系图停止作业](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>限制

* 由于身份验证模型限制，不支持 Power BI 和 Azure Data Lake Storage Gen1 输出接收器。

* 只有云输入选项具有[时间策略](stream-analytics-out-of-order-and-late-events.md)支持，本地输入选项不具备此支持。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Visual Studio 创建流分析作业](stream-analytics-quick-create-vs.md)
* [使用 Visual Studio 查看 Azure 流分析作业](stream-analytics-vs-tools.md)
* [使用用于 Visual Studio 的 Azure 流分析工具在本地测试实时数据（预览）](stream-analytics-live-data-local-testing.md)
