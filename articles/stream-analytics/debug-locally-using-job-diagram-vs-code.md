---
title: 在 Visual Studio Code 中使用作业关系图在本地调试 Azure 流分析查询
description: 本文介绍如何在适用于 Visual Studio Code 的 Azure 流分析扩展中使用作业关系图在本地调试查询。
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: f8885229c8ab6784c6c0bf45487812fcc43f9b22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90903817"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio-code"></a>在 Visual Studio Code 中使用作业关系图在本地调试 Azure 流分析查询

不输出结果或输出意外结果的流式处理作业通常需要进行故障排除。 适用于 Azure 流分析的 Visual Studio Code 扩展集成了作业关系图、指标、诊断日志和中间结果，可帮助快速确定问题根源。 在本地测试查询时，可以使用作业关系图检查每个步骤的中间结果集和指标。

## <a name="debug-a-query-using-job-diagram"></a>使用作业关系图调试查询

可以使用一个 Azure 流分析脚本将输入数据转换为输出数据。 作业关系图显示数据如何从输入源（如事件中心、IoT 中心）经多个查询步骤流向输出接收器。 每个查询步骤映射到在脚本中使用 `WITH` 语句定义的临时结果集。 可以查看每个中间结果集中每个查询步骤的数据和指标，以找出问题的根源。

> [!NOTE]
> 此作业关系图仅显示单个节点中的本地测试的数据和指标。 不应将其用于性能优化和故障排除。

### <a name="start-local-testing"></a>启动本地测试

使用此[快速入门](quick-create-visual-studio-code.md)了解如何使用 Visual Studio Code 创建流分析作业，或者如何[将现有作业导出到本地项目](visual-studio-code-explore-jobs.md)。 系统为导出的作业自动填充输入和输出的凭据。

若要使用本地输入数据测试查询，请按照这些[说明](visual-studio-code-local-run.md)操作。 如果要使用实时输入进行测试，请[配置输入](stream-analytics-add-inputs.md)并转到下一步。 

打开 \.asaql 脚本文件，并选择“在本地运行”。 然后，选择“使用本地输入”或“使用实时输入”。 作业关系图在窗口右侧显示。

### <a name="view-the-output-and-intermediate-result-set"></a>查看输出和中间结果集  

1. 所有作业输出都在 Visual Studio Code 窗口右下方的结果窗口中显示。

   > [!div class="mx-imgBorder"]
   > ![作业输出结果](./media/debug-locally-using-job-diagram-vs-code/job-output-results.png)

2. 选择查询步骤以导航到脚本。 你将自动定向到左侧编辑器中的相应脚本。 中间结果在 Visual Studio Code 窗口右下方的结果窗口中显示。

   > [!div class="mx-imgBorder"]
   > ![作业关系图预览结果](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

### <a name="view-metrics"></a>查看指标

在本部分，你将浏览适用于关系图每个组成部分的指标。

1. 选择 Visual Studio Code 窗口右下方的“结果”选项卡旁边的“指标”选项卡。

2. 从下拉列表中选择“作业”。 可以在图形节点中选择任何空白区域，以导航到作业级别指标。 此视图包含所有指标，这些指标在作业运行时每 10 秒更新一次。 可以选择或取消选择右侧的指标，以在图表中查看它们。

   > [!div class="mx-imgBorder"]
   > ![作业关系图指标](./media/debug-locally-using-job-diagram-vs-code/job-metrics.png)

3. 从下拉列表中选择输入数据源的名称，以查看输入指标。 下方屏幕截图中的输入源名为“quotes”。 有关输入指标的详细信息，请参阅[了解流分析作业监视以及如何监视查询](stream-analytics-monitoring.md)。

   > [!div class="mx-imgBorder"]
   > ![作业关系图输入指标](./media/debug-locally-using-job-diagram-vs-code/input-metrics.png)

4. 从作业关系图中选择一个查询步骤，或者从下拉列表中选择步骤名称，以查看步骤级别指标。 水印延迟是唯一可用的步骤指标。

   > [!div class="mx-imgBorder"]
   > ![步骤指标](./media/debug-locally-using-job-diagram-vs-code/step-metrics.png)

5. 在关系图中或从下拉列表中选择一个输出，以查看与输出相关的指标。 有关输出指标的详细信息，请参阅[了解流分析作业监视以及如何监视查询](stream-analytics-monitoring.md)。 不支持实时输出接收器。

   > [!div class="mx-imgBorder"]
   > ![输出指标](./media/debug-locally-using-job-diagram-vs-code/output-metrics.png)

### <a name="view-diagnostic-logs"></a>查看诊断日志

作业级别诊断日志包含有关输入数据源和输出接收器的诊断信息。 选择输入节点或输出节点时，仅显示相应的日志。 如果选择查询步骤，则不会显示任何日志。 可以在作业级别查找所有日志，还可以按严重性和时间筛选日志。

   > [!div class="mx-imgBorder"]
   > ![诊断日志](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs.png)

   选择一个日志条目以查看完整消息。

   > [!div class="mx-imgBorder"]
   > ![诊断日志消息](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs-message.png)


## <a name="other-job-diagram-features"></a>其他作业关系图功能

可以根据需要从工具栏中选择“停止”或“暂停”。 作业暂停后，可以从最后一个输出中恢复它。

> [!div class="mx-imgBorder"]
> ![停止或暂停作业](./media/debug-locally-using-job-diagram-vs-code/stop-pause-job.png)

选择作业关系图右上方的“作业摘要”，以查看本地作业的属性和配置。

> [!div class="mx-imgBorder"]
> ![本地作业摘要](./media/debug-locally-using-job-diagram-vs-code/job-summary.png)

## <a name="limitations"></a>限制

* 本地运行不支持实时输出接收器。

* 仅在 Windows 操作系统上支持使用 JavaScript 函数在本地运行作业。

* 不支持 C# 自定义代码和 Azure 机器学习功能。 

* 只有云输入选项具有[时间策略](stream-analytics-out-of-order-and-late-events.md)支持，本地输入选项不具备此支持。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Visual Studio Code 创建流分析作业](quick-create-visual-studio-code.md)
* [使用 Visual Studio Code 浏览 Azure 流分析](visual-studio-code-explore-jobs.md)
* [通过 Visual Studio Code 使用示例数据在本地测试流分析查询](visual-studio-code-local-run.md)
* [通过 Visual Studio Code，使用实时输入在本地测试 Azure 流分析作业](visual-studio-code-local-run-live-input.md)
