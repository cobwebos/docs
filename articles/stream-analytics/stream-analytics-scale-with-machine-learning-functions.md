---
title: 在 Azure 流分析中缩放机器学习函数
description: 本文介绍如何通过配置分区和流单元缩放使用机器学习函数，的流分析作业。
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 3d478c2421066c8347622f9064c479bb8255b112
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621745"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-functions"></a>缩放 Stream Analytics 作业使用 Azure 机器学习工作室函数

本文介绍如何有效地扩展 Azure Stream Analytics 作业使用 Azure 机器学习函数。 有关在一般情况下如何缩放流分析作业的信息，请参阅文章[缩放作业](stream-analytics-scale-jobs.md)。

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>流分析中的 Azure 机器学习函数是什么？

流分析中的机器学习函数可像流分析查询语言中的常规函数调用那样使用。 但是，在后台，这些函数调用是实际的 Azure 机器学习 Web 服务请求。

可以通过"批处理"多个行集中到相同的 web 服务 API 调用来提高的机器学习 web 服务请求的吞吐量。 此分组称为最小的批。 有关详细信息，请参阅[Azure 机器学习工作室 Web 服务](../machine-learning/studio/consume-web-services.md)。 Stream Analytics 中的 Azure 机器学习工作室的支持处于预览状态。

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>使用机器学习函数配置流分析作业

有两个参数以配置 Stream Analytics 作业使用的机器学习函数：

* 机器学习函数调用的批处理大小。
* 流式处理单位 (Su) 预配为 Stream Analytics 作业数。

若要确定相应的值的 Su，决定是否想要优化的 Stream Analytics 作业延迟或每个 SU 的吞吐量。 可能会始终将 Su 添加到作业以增加分区良好的 Stream Analytics 查询的吞吐量。 额外的 Su 不要增加运行作业的成本。

确定滞后时间*容差*Stream Analytics 作业。 Azure 机器学习服务请求的延迟和 Stream Analytics 作业的延迟，增加批大小将增加。

增加批大小支持 Stream Analytics 作业处理**更多事件**与**数量**的机器学习 web 服务请求。 增加机器学习 web 服务延迟呈通常增加批大小。 

请务必考虑机器学习 web 服务在任何给定的情况下最经济有效的批大小。 Web 服务请求的默认批大小为 1000年。 您可以使用此默认大小更改[Stream Analytics REST API](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "Stream Analytics REST API")或[适用于 Stream Analytics 的 PowerShell 客户端](stream-analytics-monitor-and-manage-jobs-use-powershell.md)。

一旦您已决定在批大小，可以设置基于该函数需要的事件数的流式处理单位 (Su) 数每秒处理。 有关流式处理单位的详细信息，请参阅[流分析缩放作业](stream-analytics-scale-jobs.md)。

每 6 个 Su 获取机器学习 web 服务的 20 个并发连接。 但是，1 个 SU 作业和 3 个 SU 作业可获得 20 个并发连接。  

如果你的应用程序生成每秒 200,000 个事件，并且批大小为 1000年，生成的 web 服务延迟为 200 毫秒。 此速率意味着，每个连接可以五个请求到机器学习 web 服务每秒。 通过 20 个连接，Stream Analytics 作业可以在一秒内处理 20000 个事件在 200 毫秒内的和 100,000 个事件。

若要处理每秒 200,000 个事件，Stream Analytics 作业需要 40 个并发连接，也就是 12 个 su。 下图显示了从流分析作业到机器学习 Web 服务终结点的请求：每 6 个 SU 最多具有 20 个机器学习 Web 服务的并发连接。

![使用机器学习函数 2 缩放流分析的作业示例](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Scale Stream Analytics with Machine Learning Functions two job example")

一般情况下，“B”代表批大小、“L”代表批大小为 B 时的 Web 服务延迟（以毫秒为单位），“N”个 SU 的流分析作业的吞吐量为：

![使用机器学习函数公式缩放流分析](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "使用机器学习函数公式缩放流分析")

此外可以在机器学习 web 服务配置最大并发调用。 建议将此参数设置为最大值 (当前是 200)。

有关此设置的详细信息，请参阅[机器学习 Web 服务的缩放文章](../machine-learning/studio/scaling-webservice.md)。

## <a name="example--sentiment-analysis"></a>示例 – 情绪分析
以下示例包括具有情绪分析机器学习函数的流分析作业，如[流分析机器学习集成教程](stream-analytics-machine-learning-integration-tutorial.md)所述。

查询是简单的、已完全分区的查询，后跟**情绪**函数，如以下示例所示：

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

让我们看创建 Stream Analytics 作业，每 10,000 个推文的速率的推文的执行情绪分析的第二个所需的配置。

使用 1 个 SU，可以此 Stream Analytics 作业处理的流量？ 作业可以跟上使用默认批大小 1000年的输入。 默认的延迟，情绪分析机器学习 web 服务 （默认批大小为 1000年） 创建不超过一秒的延迟。

流分析作业的**整体**或端到端延迟通常是几秒钟。 请仔细了解此流分析作业，*特别是*机器学习函数调用数。 批大小为 1000年，吞吐量为 10,000 个事件向 web 服务将大约 10 个请求。 即使使用一个 SU，也有足够的并发连接可以容纳此输入流量。

如果输入事件率增加 100 倍，而流分析作业需要每秒处理 1000000 条推文。 有两个选项来完成增加的规模：

1. 增加批大小。
2. 分区处理并行中的事件的输入流。

如果使用第一个选项，作业延迟将增加  。

使用第二个选项，必须预配更多的 Su 来使更多并发机器学习 web 服务请求。 此更多的 Su，增加作业**成本**。

让我们看看每个批次大小缩放使用以下的延迟度量值：

| Latency | 批大小 |
| --- | --- |
| 200 毫秒 | 1000 个事件批次或更低 |
| 250 毫秒 | 5,000 事件批 |
| 300 毫秒 | 10,000 事件批 |
| 500 毫秒 | 25000 事件批 |

1. 使用第一个选项（不  预配更多的 SU）。 批大小可能会增加到 25000  。 增加批大小以这种方式将允许作业处理 1000000 个事件具有 20 个并发连接到机器学习 web 服务 （的延迟为 500 毫秒，每个调用）。 所以，由于要向机器学习 Web 服务发出情绪函数请求，流分析作业的额外延迟将从 200 毫秒增加到 500 毫秒   。 然而，批大小**不能**无限机器学习 web 服务要求请求有效负载大小为 4 MB 或更小，增加和后 100 秒操作 web 服务请求超时。
1. 如果使用第二个选项，批大小仍为 1000，Web 服务延迟为 200 毫秒，每 20 个 Web 服务的并发连接每秒能够处理 1000 * 20 * 5 个事件，即 100,000 个事件。 因此，若要每秒处理 1000000 个事件，作业需要 60 个 SU。 与第一个选项相比，流分析作业将产生更多 Web 服务批处理请求，从而增加成本。

下表介绍了不同 SU 和批大小（以每秒事件数为单位）的流分析作业的吞吐量。

| 批大小（ML 延迟） | 500（200 毫秒） | 1000（200 毫秒） | 5,000（250 毫秒） | 10,000（300 毫秒） | 25,000（500 毫秒） |
| --- | --- | --- | --- | --- | --- |
| **1 个 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **3 个 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **6 个 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **12 个 SU** |5,000 |10,000 |40,000 |60,000 |100,000 |
| **18 个 SU** |7,500 |15,000 |60,000 |90,000 |150,000 |
| **24 个 SU** |10,000 |20,000 |80,000 |120,000 |200,000 |
| **…** |… |… |… |… |… |
| **60 个 SU** |25,000 |50,000 |200,000 |300,000 |500,000 |

到目前为止，应该清楚了解流分析中机器学习函数的工作原理。 可能还知道流分析作业从数据源中“提取”数据，并且每次“提取”都会返回一批供流分析作业处理的事件。 这种“提取”模型如何影响机器学习 Web 服务请求？

通常情况下，我们为机器学习函数设置的批大小不会被每个流分析作业“提取”而返回的事件数整除。 如果发生这种情况，机器学习 Web 服务将通过“部分”批处理调用。 使用部分批处理可避免产生拉取请求合并事件中的其他作业延迟开销。

## <a name="new-function-related-monitoring-metrics"></a>与函数相关的新监视指标
在流分析作业的“监视”区域，新增了三个与函数相关的指标。 它们是**函数请求**，**函数事件**并**FAILED FUNCTION REQUESTS**下, 图中所示。

![使用机器学习函数指标缩放流分析](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "使用机器学习函数指标缩放流分析")

它们的定义如下：

**函数请求数**：函数请求的数量。

**函数事件数**：函数请求数中的事件数。

**失败的函数请求数**：失败的函数请求数量。

## <a name="key-takeaways"></a>要点

若要缩放使用机器学习函数 Stream Analytics 作业，请考虑以下因素：

1. 输入的事件速率。
2. 正在运行的 Stream Analytics 作业 （和机器学习 web 服务请求的批大小） 允许的延迟。
3. 预配的 Stream Analytics Su 和机器学习 web 服务请求 （其他与函数相关成本） 的数量。

用作示例的是完全分区的流分析查询。 如果需要更复杂的查询，[Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)是一项绝佳资源，可以获取流分析团队的额外帮助。

## <a name="next-steps"></a>后续步骤
若要了解流分析的更多内容，请参阅：

* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
