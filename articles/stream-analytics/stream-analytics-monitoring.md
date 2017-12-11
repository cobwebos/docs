---
title: "了解流分析作业监视 | Microsoft 文档"
description: "了解流分析作业监视"
keywords: "查询监视器"
services: stream-analytics
documentationcenter: 
author: jseb225
manager: jhubbard
editor: cgronlun
ms.assetid: 5f5cc00f-4a7b-491e-89e1-dbafea46d399
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 7474f45494c6190ffcac354e75458b18f5777fb9
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2017
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>了解流分析作业监视以及如何监视查询

## <a name="introduction-the-monitor-page"></a>简介：“监视”页
Azure 门户提供了可用于监视和排查查询和作业性能问题的关键性能指标。 要查看这些指标，请浏览到感兴趣的想要查看其指标的流分析作业，并查看“概览”页面上的“监视”部分。  

![监视链接](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

此窗口如下所示：

![监视作业仪表板](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>可用于流分析的指标
| 度量值                 | 定义                               |
| ---------------------- | ---------------------------------------- |
| 流单元利用率 %       | 从作业的“比例”选项卡向一个作业分配的流单元利用率。 如果此指标达到 80% 或以上，则很可能会出现事件处理延迟或停止处理的情况。 |
| 输入事件数           | 流分析作业收到的数据量，以事件数来衡量。 这可以用于验证正在发送到输入源的事件。 |
| 输出事件数          | 流分析作业发送到输出目标的数据量，以事件数来衡量。 |
| 无序事件数    | 收到的无序事件的数目，系统根据事件排序策略来删除这些事件，或者为其提供一个经过调整的时间戳。 这可能会受“无序容错时段”设置的影响。 |
| 数据转换错误数 | 流分析作业导致的数据转换错误的数目。 |
| 运行时错误         | 与查询处理相关的错误总数（不包括引入事件或输出结果时发现的错误） |
| 延迟输入事件数      | 延迟到达的事件的数目，系统根据延迟到达容错时段设置的事件排序策略配置删除这些事件，或者调整其时间戳。 |
| 函数请求数      | 对 Azure 机器学习函数（如果存在）的调用数。 |
| 失败的函数请求数 | 失败的 Azure 机器学习函数（如果存在）调用数。 |
| 函数事件数        | 发送到 Azure 机器学习函数（如果存在）的事件数。 |
| 输入事件字节数      | 流分析作业收到的数据量（以字节为单位）。 这可以用于验证正在发送到输入源的事件。 |


## <a name="customizing-monitoring-in-the-azure-portal"></a>在 Azure 门户中自定义监视
可以在“编辑图表”设置中调整图表类型、显示的指标和时间范围。 有关详细信息，请参阅[如何自定义监视](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)。

  ![查询监视器时间关系图](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>最新的输出
用于监视作业的另一个有趣的数据点是最后一个输出的时间，如“概述”页所示。
此时间是作业的最新输出的应用程序时间（即使用事件数据的时间戳的时间）。

## <a name="get-help"></a>获取帮助
如需进一步的帮助，请尝试我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

