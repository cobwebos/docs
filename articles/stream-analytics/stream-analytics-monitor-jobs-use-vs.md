---
title: 使用 Visual Studio 监视和管理 Azure 流分析作业
description: 本文介绍如何使用 Visual Studio 监视和管理 Azure 流分析作业。
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: 9cc65fc5119bedc816f825b7aae6120f4bca3b12
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39717388"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-visual-studio"></a>使用 Visual Studio 监视和管理流分析作业

本文演示如何在 Visual Studio 中监视流分析作业。 针对 Visual Studio 的 Azure 流分析工具可提供类似于 Azure 门户的监视体验，而无需离开 IDE。 将作业从 Script.asaql 提交到 Azure 后即可立即开始监视作业，或者可以监视现有作业，无需考虑它们的创建方式。 

## <a name="job-summary"></a>作业摘要

“作业摘要”和“作业指标”提供作业快照。 可一目了然地了解作业状态和事件信息。]

<img src="./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-job-summary-metrics.png" alt="Stream Analytics job summary and job metrics" width="300px"/> 


## <a name="job-metrics"></a>作业指标

可以折叠“作业摘要”并单击“作业指标”选项卡，查看含有重要指标的关系图。 选中和取消选中指标类型，将其添加到关系图或从图中删除。

![流分析指标](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-metrics.png)


## <a name="error-monitoring"></a>错误监视

此外，还可以通过单击“错误”选项卡监视错误。

![流分析错误](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-errors.png)


## <a name="get-support"></a>获取支持
如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。 

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [使用 Visual Studio 创建 Azure 流分析作业](stream-analytics-quick-create-vs.md)
* [安装针对 Visual Studio 的 Azure 流分析工具](stream-analytics-tools-for-visual-studio-install.md) 


