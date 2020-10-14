---
title: 在 Azure 流分析中缩放机器学习 Studio (经典) 函数
description: 本文介绍如何通过配置分区和流单元来缩放使用机器学习 Studio (经典) 函数的流分析作业。
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2020
ms.openlocfilehash: d2fe8445d41f88852c6c9d4db84f4e1b03183a2e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92015526"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>使用 Azure 机器学习工作室（经典）函数缩放流分析作业

> [!TIP]
> 强烈建议使用 [Azure 机器学习 UDF](machine-learning-udf.md)，而不是 Azure 机器学习工作室（经典）UDF 来提高性能和可靠性。

本文介绍如何有效地缩放使用 Azure 机器学习 Studio (经典) 函数的 Azure 流分析作业。 有关如何缩放流分析作业的常规信息，请参阅文章 [缩放作业](stream-analytics-scale-jobs.md)。

## <a name="what-is-a-studio-classic-function-in-stream-analytics"></a>什么是流分析中的 Studio (经典) 函数？

流分析中的机器学习 Studio (经典) 函数可与流分析查询语言中的常规函数调用一样使用。 但在幕后，这些函数调用实际上是 Studio (经典) Web 服务请求。

可以通过将多个行通过 "批处理" 在同一 web 服务 API 调用中来提高 Studio (经典) web 服务请求的吞吐量。 这种分组称为微型批。 有关详细信息，请参阅 [Azure 机器学习工作室（经典）Web 服务](../machine-learning/classic/consume-web-services.md)。 流分析中的 Studio (经典) 支持处于预览阶段。

## <a name="configure-a-stream-analytics-job-with-studio-classic-functions"></a>使用 Studio (经典) 函数配置流分析作业

有两个参数可用于配置你的流分析作业使用的 Studio (经典) 函数：

* Studio (经典) 函数调用的批大小。
* 为流分析作业预配的流单元 (SU) 数。

若要确定适当的 SU 值，请决定是要优化流分析作业的延迟，还是优化每个 SU 的吞吐量。 可能会始终将 SU 添加到某个作业，以提高适当分区的流分析查询的吞吐量。 增加 SU 会增大运行作业的成本。

确定流分析作业的延迟*容限*。 增大批大小会增加 Studio (经典) 请求和流分析作业的延迟。

增加批大小使流分析作业可以处理具有**相同数量**的 Studio (经典) web 服务请求的**更多事件**。 Studio (经典) web 服务延迟的增加通常是增加批大小。 

在任何给定的情况下，请务必考虑 Studio (经典) web 服务的最经济高效的批大小。 Web 服务请求的默认批大小为 1000。 可以使用[流分析 REST API](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "流分析 REST API") 或[适用于流分析的 PowerShell 客户端](stream-analytics-monitor-and-manage-jobs-use-powershell.md)来更改此默认大小。

确定批大小后，可根据函数每秒需要处理的事件数来设置流单元 (SU) 数量。 有关流式处理单位的详细信息，请参阅[流分析缩放作业](stream-analytics-scale-jobs.md)。

每6个 SUs 获取与 Studio (经典) web 服务的20个并发连接。 但是，1 个 SU 作业和 3 个 SU 作业可建立 20 个并发连接。  

如果应用程序每秒生成 200,000 个事件，并且批大小为 1000，则造成的 Web 服务延迟为 200 毫秒。 此速率意味着每个连接每秒可以向 Studio (经典) web 服务发出5次请求。 通过 20 个连接，流分析作业可在 200 毫秒内处理 20,000 个事件，在 1 秒内可处理 100,000 个事件。

若要每秒处理 200,000 个事件，流分析作业需要 40 个并发连接，也就是 12 个 SU。 下图说明了从流分析作业到工作室 (经典) web 服务终结点的请求–每6个 SUs 最多可以有20个与 Studio (经典) web 服务的并发连接。

![用 Studio (经典) 函数缩放流分析两个作业示例](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "用 Studio (经典) 函数缩放流分析两个作业示例")

一般情况下，“B”代表批大小、“L”代表批大小为 B 时的 Web 服务延迟（以毫秒为单位），“N”个 SU 的流分析作业的吞吐量为******************：

![用 Studio (经典) 函数公式缩放流分析](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "用 Studio (经典) 函数公式缩放流分析")

你还可以在 Studio (经典) web 服务上配置 "最大并发调用数"。 建议将此参数设置为最大值（目前为 200）。

有关此设置的详细信息，请查看 [机器学习 Studio (经典) Web 服务的缩放文章](../machine-learning/classic/create-endpoint.md)。

## <a name="example--sentiment-analysis"></a>示例 – 情绪分析
下面的示例包含一个流分析作业，其中包含情绪分析工作室 (经典) 函数，如 [流分析机器学习 Studio (经典) 集成教程](stream-analytics-machine-learning-integration-tutorial.md)中所述。

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

此流分析作业使用 1 个 SU 是否能够处理这种流量？ 使用默认批大小 1000，该作业应该能够跟上输入速度。 默认的批大小为) 1000 (经典) web 服务 (的情绪 analysis Studio 的默认延迟时间不超过秒。

流分析作业的 **总延迟** 或端到端延迟通常只有几秒钟。 更详细地了解此流分析作业， *特别* 是 Studio (经典) 函数调用。 如果批大小为 1000，则 10,000 个事件的吞吐量将向 Web 服务发送大约 10 个请求。 即使使用一个 SU，也有足够的并发连接可以容纳此输入流量。

如果输入事件率增加 100 倍，而流分析作业需要每秒处理 1000000 条推文。 有两个选项来完成增加的规模：

1. 增加批大小。
2. 将输入流分区以并行处理事件。

如果使用第一个选项，作业延迟将增加****。

使用第二个选项时，必须预配更多的 SUs，使其拥有更多的并发工作室 (经典) web 服务请求。 增加 SU 也会增大作业**成本**。

让我们了解如何使用每个批大小的以下延迟度量值进行缩放：

| 延迟 | 批大小 |
| --- | --- |
| 200 毫秒 | 包含 1000 个或更少事件的批 |
| 250 毫秒 | 包含 5,000 个事件的批 |
| 300 毫秒 | 包含 1,000 个事件的批 |
| 500 毫秒 | 包含 25,000 个事件的批 |

1. 使用第一个选项（不**** 预配更多的 SU）。 批大小可能会增加到 25000****。 以这种方式增加批大小会使作业能够处理1000000事件，其中20个并发连接到工作室 (经典) web 服务 (，延迟为每个调用) 500 毫秒。 因此，由于情绪函数对 Studio (经典) web 服务请求的函数请求，流分析作业的额外延迟将从 **200 ms** 增加到 **500 毫秒**。 但是，批大小 **不能** 无限增加，因为 Studio (经典) web 服务要求请求的负载大小为 4 MB 或更小，web 服务请求在100秒操作后超时。
1. 如果使用第二个选项，批大小仍为 1000，Web 服务延迟为 200 毫秒，每 20 个 Web 服务的并发连接每秒能够处理 1000 * 20 * 5 个事件，即 100,000 个事件。 因此，若要每秒处理 1,000,000 个事件，作业需要 60 个 SU。 与第一个选项相比，流分析作业会提出更多 Web 服务批处理请求，从而导致成本增加。

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

到目前为止，您应该已经充分了解了流分析中的经典) 函数 (的工作原理。 可能还知道流分析作业从数据源中“拉取”数据，并且每次“拉取”都会返回一批供流分析作业处理的事件。 此拉取模型如何影响 Studio (经典) web 服务请求？

通常，我们为 Studio (经典) 函数设置的批大小不会被每个流分析作业 "pull" 返回的事件数整除。 发生这种情况时，将通过 "部分" 批处理调用 Studio (经典) web 服务。 使用部分批可避免在合并拉取间的事件时造成其他作业延迟开销。

## <a name="new-function-related-monitoring-metrics"></a>函数相关的新监控指标
在流分析作业的“监视”区域中，已添加了其他三个的函数相关的指标。 它们是“函数请求数”、“函数事件数”和“失败的函数请求数”，如下图所示**** **** ****。

![用 Studio (经典) 函数指标缩放流分析](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "用 Studio (经典) 函数指标缩放流分析")

它们的定义如下：

**函数请求数**：函数请求的数量。

**函数事件数**：函数请求数中的事件数。

**失败的函数请求数**：失败的函数请求数量。

## <a name="key-takeaways"></a>关键点

若要使用 Studio (经典) 函数缩放流分析作业，请考虑以下因素：

1. 输入事件速率。
2. 正在运行的流分析作业的容许延迟时间 (，因此 (经典) web services 请求) 的批大小。
3. 预配流分析 SUs 和 Studio (经典) web 服务请求的数量， (其他与函数相关的成本) 。

以完全分区的流分析查询为例。 如果需要更复杂的查询，[有关 Azure 流分析的 Microsoft Q&A 问题页面](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html)是一项绝佳资源，可以获取流分析团队的额外帮助。

## <a name="next-steps"></a>后续步骤
若要了解流分析的更多内容，请参阅：

* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
