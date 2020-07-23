---
title: 使用 Visual Studio Code 中的作业关系图在本地调试 Azure 流分析查询
description: 本文介绍如何使用适用于 Visual Studio Code 的 Azure 流分析扩展中的作业关系图在本地调试查询。
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: c31f3c998df918466e707c95f041592051e8251c
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045308"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio-code"></a>使用 Visual Studio Code 中的作业关系图在本地调试 Azure 流分析查询

输出无结果或意外结果的流式处理作业通常需要进行故障排除。 用于 Azure 流分析的 Visual Studio Code 扩展集成了作业关系图、指标、诊断日志和中间结果，以帮助你快速确定问题的根源。 在本地测试查询时，可以使用作业关系图检查每个步骤的中间结果集和指标。

## <a name="debug-a-query-using-job-diagram"></a>使用作业关系图调试查询

Azure 流分析脚本用于将输入数据转换为输出数据。 作业关系图显示了如何通过多个查询步骤，通过多个查询步骤，将数据从事件中心或 IoT 中心等输入源流向输出接收器。 每个查询步骤都映射到使用语句在脚本中定义的临时结果集 `WITH` 。 您可以查看每个中间结果集中每个查询步骤的数据和指标，以找出问题的根源。

> [!NOTE]
> 此作业关系图只显示单个节点中的本地测试数据和指标。 不应将其用于性能优化和故障排除。

### <a name="start-local-testing"></a>启动本地测试

使用本[快速入门](quick-create-vs-code.md)了解如何使用 Visual Studio Code 创建流分析作业，或将[现有作业导出到本地项目](visual-studio-code-explore-jobs.md)。 为导出的作业自动填充输入和输出的凭据。

如果要测试包含本地输入数据的查询，请遵循这些[说明](visual-studio-code-local-run.md)。 如果要测试实时输入，请将[输入](stream-analytics-add-inputs.md)转到下一步。 

打开* \. script.asaql*脚本文件，并选择 "**本地运行**"。 然后，选择 "**使用本地输入**" 或 "**使用实时输入**"。 作业关系图将显示在窗口的右侧。

### <a name="view-the-output-and-intermediate-result-set"></a>查看输出和中间结果集  

1. 所有作业输出都显示在 "Visual Studio Code" 窗口右下角的 "结果" 窗口中。

   > [!div class="mx-imgBorder"]
   > ![作业输出结果](./media/debug-locally-using-job-diagram-vs-code/job-output-results.png)

2. 选择要导航到脚本的查询步骤。 您将自动定向到左侧编辑器中的相应脚本。 中间结果将显示在 "Visual Studio Code" 窗口右下角的 "结果" 窗口中。

   > [!div class="mx-imgBorder"]
   > ![作业关系图预览结果](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

### <a name="view-metrics"></a>查看指标

本部分介绍可用于关系图的每个部分的指标。

1. 选择 "Visual Studio Code" 窗口右下角的 "**结果**" 选项卡旁边的 "**指标**" 选项卡。

2. 从下拉列表中选择 "**作业**"。 您可以在图形节点中选择任何空白区域以导航到 "作业级别" 指标。 此视图包含所有指标，在作业运行时每10秒更新一次。 您可以选择或取消选择右侧的度量值，以在图表中查看它们。

   > [!div class="mx-imgBorder"]
   > ![作业关系图指标](./media/debug-locally-using-job-diagram-vs-code/job-metrics.png)

3. 从下拉列表中选择输入数据源的名称以查看输入指标。 下面屏幕截图中的输入源称为*引号*。 有关输入指标的详细信息，请参阅[了解流分析作业监视以及如何监视查询](stream-analytics-monitoring.md)。

   > [!div class="mx-imgBorder"]
   > ![作业关系图指标](./media/debug-locally-using-job-diagram-vs-code/input-metrics.png)

4. 从作业关系图中选择一个查询步骤，或从下拉列表中选择步骤名称以查看步骤级指标。 水位线延迟是唯一可用的步骤指标。

   > [!div class="mx-imgBorder"]
   > ![步骤指标](./media/debug-locally-using-job-diagram-vs-code/step-metrics.png)

5. 在关系图中或从下拉列表中选择一个输出，以查看与输出相关的指标。 有关输出指标的详细信息，请参阅[了解流分析作业监视以及如何监视查询](stream-analytics-monitoring.md)。 不支持实时输出接收器。

   > [!div class="mx-imgBorder"]
   > ![输出指标](./media/debug-locally-using-job-diagram-vs-code/output-metrics.png)

### <a name="view-diagnostic-logs"></a>查看诊断日志

作业级诊断日志包含输入数据源和输出接收器的诊断信息。 选择输入节点或输出节点时，只会显示相应的日志。 如果选择查询步骤，则不会显示任何日志。 你可以在作业级别找到所有日志，并且可以按严重性和时间筛选日志。

   > [!div class="mx-imgBorder"]
   > ![诊断日志](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs.png)

   选择一个日志项以查看整个消息。

   > [!div class="mx-imgBorder"]
   > ![诊断日志消息](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs-message.png)


## <a name="other-job-diagram-features"></a>其他作业关系图功能

您可以根据需要从工具栏中选择 "**停止**" 或 "**暂停**"。 暂停作业后，可以从最后一个输出恢复该作业。

> [!div class="mx-imgBorder"]
> ![停止或暂停作业](./media/debug-locally-using-job-diagram-vs-code/stop-pause-job.png)

在作业关系图的右上角选择 "**作业摘要**"，查看本地作业的属性和配置。

> [!div class="mx-imgBorder"]
> ![本地作业摘要](./media/debug-locally-using-job-diagram-vs-code/job-summary.png)

## <a name="limitations"></a>限制

* 本地运行中不支持实时输出接收器。

* 仅在 Windows 操作系统上支持用 JavaScript 函数本地运行作业。

* 不支持 c # 自定义代码和 Azure 机器学习函数。 

* 仅云输入选项具有[时间策略](stream-analytics-out-of-order-and-late-events.md)支持，而本地输入选项不支持。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Visual Studio Code 创建流分析作业](quick-create-vs-code.md)
* [利用 Visual Studio Code 探索 Azure 流分析](visual-studio-code-explore-jobs.md)
* [通过 Visual Studio Code 使用示例数据在本地测试流分析查询](visual-studio-code-local-run.md)
* [通过 Visual Studio Code，使用实时输入在本地测试 Azure 流分析作业](visual-studio-code-local-run-live-input.md)
