---
title: 使用 Azure 流分析和 Azure ML 函数缩放作业 | Microsoft Docs
description: 了解在使用 Azure 机器学习函数时，如何正确缩放流分析作业（分区、SU 数量等）。
keywords: ''
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: 47ce7c5e-1de1-41ca-9a26-b5ecce814743
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: dd6effab3ba0b411131414bd757ffe8cc54e49d2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-functions"></a>使用 Azure 机器学习函数缩放流分析作业
设置流分析作业，并通过它运行某些示例数据，这通常很简单。 但当我们需要运行数据量更大的相同作业时，该怎么办？ 需要了解如何配置流分析作业，以便可以缩放它。 本文档关注使用机器学习函数缩放流分析作业的特殊方面。 有关在一般情况下如何缩放流分析作业的信息，请参阅文章[缩放作业](stream-analytics-scale-jobs.md)。

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>流分析中的 Azure 机器学习函数是什么？
流分析中的机器学习函数可像流分析查询语言中的常规函数调用那样使用。 但是在后台，函数调数用实际上是 Azure 机器学习 Web 服务请求数。 机器学习 Web 服务支持在相同的 Web 服务 API 调用中“批处理”多个行（这称为“微批处理”），从而提高整体吞吐量。 请参阅以下文章，了解详细信息：[流分析中的 Azure 机器学习函数](https://blogs.technet.microsoft.com/machinelearning/2015/12/10/azure-ml-now-available-as-a-function-in-azure-stream-analytics/)和 [Azure 机器学习 Web 服务](../machine-learning/studio/consume-web-services.md)。

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>使用机器学习函数配置流分析作业
在配置流分析作业的机器学习函数时，需要考虑两个参数：机器学习函数调用数的批大小和为流分析作业预配的流式处理单位 (SU)。 若要确定这些参数的相应值，请首先确定延迟和吞吐量，即流分析作业延迟和每个 SU 的吞吐量。 虽然额外的 SU 会增加运行作业的成本，但可能会始终将 SU 添加到某个作业，以增加分区良好的流分析查询吞吐量。

因此，请务必确定运行流分析作业的延迟*公差*。 运行 Azure 机器学习服务请求产生的额外延迟会随着批大小的增加而自然增加，这会恶化流分析作业延迟现象。 另一方面，增加批大小支持流分析作业使用相同数量的机器学习 Web 服务请求处理 * 更多事件。 增加机器学习 Web 服务延迟通常与增加批大小呈子线性关系，所以在任何给定的情况下，请务必考虑机器学习 Web 服务的最经济有效的批大小。 Web 服务请求的默认批大小为 1000，可使用[流分析 REST API](https://msdn.microsoft.com/library/mt653706.aspx "流分析 REST API") 或[用于流分析的 PowerShell 客户端](stream-analytics-monitor-and-manage-jobs-use-powershell.md "用于流分析的 PowerShell 客户端")修改大小。

确定批大小后，可根据函数每秒需要处理的事件数来确定流式处理单位 (SU) 数量。 有关流式处理单位的详细信息，请参阅[流分析缩放作业](stream-analytics-scale-jobs.md)。

一般情况下，除 1 个 SU 作业和 3 个 SU 作业可获得 20 个并发连接外，每 6 个 SU 也具有 20 个机器学习 Web 服务的并发连接。  例如，如果输入数据率为每秒 200,000 个事件，并且批大小仍为默认的 1000，则对于具有 1000 个事件的小型批，生成的 Web 服务的延迟为 200 毫秒。 这意味着每个连接在一秒内可以请求 5 次机器学习 Web 服务。 通过 20 个连接，流分析作业可在 200 毫秒内处理 20,000 个事件，因而在 1 秒内可处理 100,000 个事件。 因此，要每秒处理 200,000 个事件，流分析作业需要 40 个并发连接，也就是 12 个 SU。 下图显示了从流分析作业到机器学习 Web 服务终结点的请求：每 6 个 SU 最多具有 20 个机器学习 Web 服务的并发连接。

![使用机器学习函数 2 缩放流分析的作业示例](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Scale Stream Analytics with Machine Learning Functions two job example")

一般情况下，“B”代表批大小、“L”代表批大小为 B 时的 Web 服务延迟（以毫秒为单位），“N”个 SU 的流分析作业的吞吐量为：

![使用机器学习函数公式缩放流分析](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "使用机器学习函数公式缩放流分析")

另外需要考虑的可能是机器学习 Web 服务端上的“最大并发调用数”，建议将其设置为最大值（当前是 200）。

有关此设置的详细信息，请参阅[机器学习 Web 服务的缩放文章](../machine-learning/studio/scaling-webservice.md)。

## <a name="example--sentiment-analysis"></a>示例 – 情绪分析
以下示例包括具有情绪分析机器学习函数的流分析作业，如[流分析机器学习集成教程](stream-analytics-machine-learning-integration-tutorial.md)所述。

查询是已完全分区的简单查询，后跟情绪函数，如下所示：

    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery

请考虑以下方案：若要每秒内产生 10000 条推文的吞吐量，则必须创建执行推文（事件）情绪分析的流分析作业。 如果使用 1 个 SU，此流分析作业可以处理该流量吗？ 如果使用默认值 1000 的批大小，作业应该能够与输入保持一致。 进一步而言，添加的机器学习函数应生成不大于一秒的延迟，这是情绪分析机器学习 Web 服务（默认批大小为 1000）的常规默认延迟。 流分析作业的**整体**或端到端延迟通常是几秒钟。 请仔细了解此流分析作业，*特别是*机器学习函数调用数。 如果批大小为 1000，则 10,000 个事件的吞吐量将向 Web 服务发送大约 10 个请求。 即使使用 1 个 SU，也有足够的并发连接可以容纳此输入流量。

但如果输入事件率增加 100 倍，而流分析作业需要每秒处理 1000000 条推文，这该怎么办？ 有两个选项：

1. 增加批大小，或者
2. 为输入流分区，平行处理事件

如果使用第一个选项，作业延迟将增加。

如果使用第二个选项，则需要预配更多 SU，因而生成更多的并发机器学习 Web 服务请求。 这意味着作业成本将增加。

假定具有 1000 个事件的批的情绪分析机器学习 Web 服务的延迟小于或等于 200 毫秒、具有 5,000 个事件的批的延迟是 250 毫秒、具有 10,000 个事件的批的延迟是 300 毫秒，或者具有 25,000 个事件的批的延迟是 500 毫秒。

1. 如果使用第一个选项，（**并非**预配更多 SU），批大小可能会增加到 **25000**。 这反过来支持作业处理 1,000,000 个事件，并且机器学习 Web 服务具有 20 个并发连接（每个调用的延迟为 500 毫秒）。 所以，由于要向机器学习 Web 服务发出情绪函数请求，流分析作业的额外延迟将从 200 毫秒增加到 500 毫秒。 然而，批大小不得无限制增加，因为机器学习 Web 服务要求 Web 服务请求在 100 秒操作超时后，请求的有效负载大小为 4 MB 或更小。
2. 如果使用第二个选项，批大小仍为 1000，Web 服务延迟为 200 毫秒，每 20 个 Web 服务的并发连接每秒能够处理 1000 * 20 * 5 个事件，即 100,000 个事件。 因此，若要每秒处理 1000000 个事件，作业需要 60 个 SU。 与第一个选项相比，流分析作业将产生更多 Web 服务批处理请求，从而增加成本。

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

通常情况下，我们为机器学习函数设置的批大小不会被每个流分析作业“提取”而返回的事件数整除。 如果发生这种情况，机器学习 Web 服务将通过“部分”批处理调用。 这样做是为了在合并提取与提取之间的事件时不会产生其他的作业延迟开销。

## <a name="new-function-related-monitoring-metrics"></a>与函数相关的新监视指标
在流分析作业的“监视”区域，新增了三个与函数相关的指标。 它们是“函数请求数”、“函数事件数”和“失败的函数请求数”，如下图所示。

![使用机器学习函数指标缩放流分析](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "使用机器学习函数指标缩放流分析")

它们的定义如下：

**函数请求数**：函数请求的数量。

**函数事件数**：函数请求数中的事件数。

**失败的函数请求数**：失败的函数请求数量。

## <a name="key-takeaways"></a>要点
要点总结起来就是，为了使用机器学习函数缩放流分析作业，必须考虑以下项目：

1. 输入事件速率
2. 运行流分析作业允许延迟（因而影响了机器学习 Web 服务的批大小）
3. 预配的流分析 SU 和机器学习 Web 服务请求数（与函数相关的额外成本）

用作示例的是完全分区的流分析查询。 如果需要更复杂的查询，[Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)是一项绝佳资源，可以获取流分析团队的额外帮助。

## <a name="next-steps"></a>后续步骤
若要了解流分析的更多内容，请参阅：

* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
