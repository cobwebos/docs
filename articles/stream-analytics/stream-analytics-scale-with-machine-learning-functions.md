---
title: 在 Azure 流分析中缩放机器学习函数
description: 本文介绍如何通过配置分区和流单元缩放使用机器学习函数，的流分析作业。
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 4e2c99274df90d232754c862f3174b5767392cec
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82929037"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>使用 Azure 机器学习工作室（经典）函数缩放流分析作业

> [!TIP]
> 强烈建议使用[Azure 机器学习 udf](machine-learning-udf.md)而不是 Azure 机器学习 Studio （经典） udf 来提高性能和可靠性。

本文介绍如何有效缩放使用 Azure 机器学习函数的 Azure 流分析作业。 有关在一般情况下如何缩放流分析作业的信息，请参阅文章[缩放作业](stream-analytics-scale-jobs.md)。

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>流分析中的 Azure 机器学习函数是什么？

流分析中的机器学习函数可像流分析查询语言中的常规函数调用那样使用。 但在幕后，函数调用实际上是 Azure 机器学习 Web 服务请求。

可以通过在同一个 Web 服务 API 调用中“批处理”多个行来提高机器学习 Web 服务请求的吞吐量。 这种分组称为微型批。 有关详细信息，请参阅[Azure 机器学习 Studio （经典） Web 服务](../machine-learning/studio/consume-web-services.md)。 流分析中对 Azure 机器学习 Studio （经典）的支持处于预览阶段。

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>使用机器学习函数配置流分析作业

可通过两个参数来配置流分析作业使用的机器学习函数：

* 机器学习函数调用的批大小。
* 为流分析作业预配的流单元 (SU) 数。

若要确定适当的 SU 值，请决定是要优化流分析作业的延迟，还是优化每个 SU 的吞吐量。 可能会始终将 SU 添加到某个作业，以提高适当分区的流分析查询的吞吐量。 增加 SU 会增大运行作业的成本。

确定流分析作业的延迟*容限*。 增加批大小会增加 Azure 机器学习请求的延迟和流分析作业的延迟。

增加批大小可让流分析作业使用**相同数量**的机器学习 Web 服务请求来处理**更多的事件**。 机器学习 Web 服务延迟增大与批大小增大之间通常呈亚线性关系。 

在任意给定情况下，必须考虑为机器学习 Web 服务选择最经济高效的批大小。 Web 服务请求的默认批大小为 1000。 可以使用[流分析 REST API](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "流分析 REST API") 或[适用于流分析的 PowerShell 客户端](stream-analytics-monitor-and-manage-jobs-use-powershell.md)来更改此默认大小。

确定批大小后，可根据函数每秒需要处理的事件数来设置流单元 (SU) 数量。 有关流式处理单位的详细信息，请参阅[流分析缩放作业](stream-analytics-scale-jobs.md)。

每 6 个 SU 可与机器学习 Web 服务建立 20 个并发连接。 但是，1 个 SU 作业和 3 个 SU 作业可建立 20 个并发连接。  

如果应用程序每秒生成 200,000 个事件，并且批大小为 1000，则造成的 Web 服务延迟为 200 毫秒。 这种速率意味着，每个连接在每秒内可向机器学习 Web 服务发出 5 个请求。 通过 20 个连接，流分析作业可在 200 毫秒内处理 20,000 个事件，在 1 秒内可处理 100,000 个事件。

若要每秒处理 200,000 个事件，流分析作业需要 40 个并发连接，也就是 12 个 SU。 下图显示了从流分析作业到机器学习 Web 服务终结点的请求：每 6 个 SU 最多具有 20 个机器学习 Web 服务的并发连接。

![使用机器学习函数两作业缩放流分析的示例](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "使用机器学习函数两作业缩放流分析的示例")

一般情况下，“B”代表批大小、“L”代表批大小为 B 时的 Web 服务延迟（以毫秒为单位），“N”个 SU 的流分析作业的吞吐量为******************：

![使用机器学习函数公式缩放流分析](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "使用机器学习函数公式缩放流分析")

还可以在机器学习 Web 服务中配置“最大并发调用数”。 建议将此参数设置为最大值（目前为200）。

有关此设置的详细信息，请参阅[机器学习 Web 服务的缩放文章](../machine-learning/studio/create-endpoint.md)。

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

让我们了解创建一个流分析作业所需的配置。该作业以每秒处理 10,000 个推文的速率对推文执行情绪分析。

此流分析作业使用 1 个 SU 是否能够处理这种流量？ 使用默认批大小 1000，该作业应该能够跟上输入速度。 使用情绪分析机器学习 Web 服务（默认批大小为 1000）的默认延迟时，延迟不超过 1 秒。

流分析作业的**整体**或端到端延迟通常是几秒钟。 请仔细了解此流分析作业，*特别是*机器学习函数调用数。 如果批大小为 1000，则 10,000 个事件的吞吐量将向 Web 服务发送大约 10 个请求。 即使使用一个 SU，也有足够的并发连接可以容纳此输入流量。

如果输入事件率增加 100 倍，而流分析作业需要每秒处理 1000000 条推文。 有两个选项来完成增加的规模：

1. 增加批大小。
2. 将输入流分区以并行处理事件。

如果使用第一个选项，作业延迟将增加****。

如果使用第二个选项，则必须预配更多的 SU 才能发出更多的机器学习 Web 服务并发请求。 增加 SU 也会增大作业**成本**。

让我们了解如何使用每个批大小的以下延迟度量值进行缩放：

| 延迟 | 批大小 |
| --- | --- |
| 200 毫秒 | 包含 1000 个或更少事件的批 |
| 250 毫秒 | 包含 5,000 个事件的批 |
| 300 毫秒 | 包含 1,000 个事件的批 |
| 500 毫秒 | 包含 25,000 个事件的批 |

1. 使用第一个选项（不**** 预配更多的 SU）。 批大小可能会增加到 25000****。 以这种方式增加批大小可让作业处理 1,000,000 个事件，并且可与机器学习 Web 服务建立 20 个并发连接（每个调用的延迟为 500 毫秒）。 所以，由于要向机器学习 Web 服务发出情绪函数请求，流分析作业的额外延迟将从 200 毫秒增加到 500 毫秒********。 然而，批大小**不可**无限制增加，因为机器学习 Web 服务要求 Web 服务请求在 100 秒操作超时后，请求的有效负载大小为 4 MB 或更小。
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
| **60 个SU** |25,000 |50,000 |200,000 |300,000 |500,000 |

到目前为止，应该清楚了解流分析中机器学习函数的工作原理。 可能还知道流分析作业从数据源中“提取”数据，并且每次“提取”都会返回一批供流分析作业处理的事件。 这种“提取”模型如何影响机器学习 Web 服务请求？

通常情况下，我们为机器学习函数设置的批大小不会被每个流分析作业“拉取”返回的事件数整除。 如果发生这种情况，机器学习 Web 服务将通过“部分”批处理调用。 使用部分批可避免在合并拉取间的事件时造成其他作业延迟开销。

## <a name="new-function-related-monitoring-metrics"></a>与函数相关的新监视指标
在流分析作业的“监视”区域，新增了三个与函数相关的指标。 它们是**函数请求**、**函数事件**和**失败的函数请求**，如下图所示。

![使用机器学习函数指标缩放流分析](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "使用机器学习函数指标缩放流分析")

它们的定义如下：

**函数请求数**：函数请求的数量。

**函数事件数**：函数请求数中的事件数。

**失败的函数请求数**：失败的函数请求数量。

## <a name="key-takeaways"></a>关键结论

若要使用机器学习函数缩放流分析作业，请考虑以下因素：

1. 输入事件速率。
2. 运行流分析作业允许延迟（因而影响了机器学习 Web 服务的批大小）。
3. 预配的流分析 SU 和机器学习 Web 服务请求数（与函数相关的额外成本）。

用作示例的是完全分区的流分析查询。 如果需要更复杂的查询，[Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)是一项绝佳资源，可以获取流分析团队的额外帮助。

## <a name="next-steps"></a>后续步骤
若要了解流分析的更多内容，请参阅：

* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
