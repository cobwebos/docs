---
title: Azure 流分析解决方案模式
description: 了解 Azure 流分析的常见解决方案模式，如仪表板建设、事件消息、数据存储、引用数据扩充和监视。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 2a449c55a0998f1a114f6aa9d2c067e48cc0cdce
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443666"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Azure 流分析解决方案模式

与 Azure 中的许多其他服务一样，流分析最好与其他服务一起使用，以创建更大的端到端解决方案。 本文讨论简单的 Azure 流分析解决方案和各种体系结构模式。 您可以根据这些模式进行构建以开发更复杂的解决方案。 本文中所述的模式可用于各种方案。 [Azure 解决方案体系结构](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics)中提供了特定于方案的模式的示例。

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>创建流分析作业，以实时仪表板建设体验

借助 Azure 流分析，可以快速建立实时仪表板和警报。 简单的解决方案从事件中心或 IoT 中心引入事件，并[使用流式数据集将 Power BI 仪表板馈送](/power-bi/service-real-time-streaming)。 有关详细信息，请参阅详细教程[利用流分析来分析电话呼叫数据和 Power BI 仪表板中的可视化效果](stream-analytics-manage-job.md)。

![ASA Power BI 仪表板](media/stream-analytics-solution-patterns/pbidashboard.png)

此解决方案只需 Azure 门户几分钟即可生成。 不涉及到任何广泛的编码，并且使用 SQL 语言来表示业务逻辑。

此解决方案模式在浏览器中提供从事件源到 Power BI 仪表板的最低延迟。 Azure 流分析是唯一一种具有此内置功能的 Azure 服务。

## <a name="use-sql-for-dashboard"></a>使用 SQL for 仪表板

Power BI 仪表板提供低延迟，但不能用于生成完整的 Power BI 报表。 常见的报告模式是先将数据输出到 SQL 数据库。 然后使用 Power BI 的 SQL connector 查询 SQL 以获取最新数据。

![ASA SQL 仪表板](media/stream-analytics-solution-patterns/sqldashboard.png)

使用 SQL 数据库可提供更大的灵活性，但会降低延迟。 此解决方案最适用于延迟要求超过一秒的作业。 利用此方法，你可以最大程度地提高 Power BI 功能，以进一步切分报表的数据，以及更多的可视化选项。 你还可以灵活地使用其他面板解决方案，如 Tableau。

SQL 不是高吞吐量数据存储区。 从 Azure 流分析到 SQL 数据库的最大吞吐量目前约为 24 MB/s。 如果解决方案中的事件源以更高的速率生成数据，则需要使用流分析中的处理逻辑将输出速率降低到 SQL。 可以使用诸如筛选、开窗聚合、使用时态联接模式匹配和分析函数等技术。 使用[Azure 流分析输出到 AZURE Sql 数据库](stream-analytics-sql-output-perf.md)中所述的技术，可以进一步优化到 SQL 的输出速率。

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>将实时见解纳入到应用程序中，并提供事件消息

流分析的第二个最常见用途是生成实时警报。 在此解决方案模式下，可以使用流分析中的业务逻辑检测[临时和空间模式](stream-analytics-geospatial-functions.md)或[异常](stream-analytics-machine-learning-anomaly-detection.md)，然后生成警报信号。 但是，与使用流分析作为首选终结点 Power BI 的仪表板解决方案不同，可以使用许多中间数据接收器。 这些接收器包括事件中心、服务总线和 Azure Functions。 作为应用程序生成器，你需要确定最适合你的方案的数据接收器。

必须实现下游事件使用者逻辑，才能在现有的业务工作流中生成警报。 由于可以在 Azure Functions 中实现自定义逻辑，Azure Functions 是执行此集成的最快方法。 有关使用 Azure 函数作为流分析作业的输出的教程，请参阅[从 Azure 流分析作业运行 Azure Functions](stream-analytics-with-azure-functions.md)。 Azure Functions 还支持各种类型的通知，包括文本和电子邮件。 逻辑应用也可用于此类集成，并在流分析和逻辑应用之间使用事件中心。

![ASA 事件消息应用](media/stream-analytics-solution-patterns/eventmessagingapp.png)

另一方面，事件中心提供最灵活的集成点。 许多其他服务（如 Azure 数据资源管理器和时序见解）都可以使用事件中心的事件。 服务可从 Azure 流分析直接连接到事件中心接收器，以完成解决方案。 对于此类集成方案，事件中心也是 Azure 上提供的最高吞吐量消息传递代理。

## <a name="dynamic-applications-and-websites"></a>动态应用程序和网站

可以使用 Azure 流分析和 Azure SignalR 服务创建自定义实时可视化效果，如仪表板或地图可视化。 使用 SignalR，可以实时更新和显示动态内容。

![ASA 动态应用](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>通过数据存储将实时见解纳入到应用程序中

如今，大多数 web 服务和 web 应用程序都使用请求/响应模式来提供表示层。 请求/响应模式非常简单，只需使用无状态前端和可缩放的存储（如 Cosmos DB），就可以轻松地使用较短的响应时间进行缩放。

高数据量通常会在基于 CRUD 的系统中产生性能瓶颈。 [事件来源解决方案模式](/azure/architecture/patterns/event-sourcing)用于解决性能瓶颈问题。 从传统的数据存储中提取临时模式和见解也很难且效率低下。 新式大容量数据驱动的应用程序通常采用基于数据流的体系结构。 Azure 流分析，因为用于动态数据的计算引擎是该体系结构中的作为。

![ASA 事件采购应用](media/stream-analytics-solution-patterns/eventsourcingapp.png)

在此解决方案模式下，Azure 流分析将事件处理和聚合到数据存储中。 应用程序层使用传统的请求/响应模式与数据存储区交互。 由于流分析能够实时处理大量事件，因此该应用程序具有高度可扩展性，无需大容量地使用数据存储层。 数据存储层实质上是系统中的具体化视图。 [Azure 流分析输出到 Azure Cosmos DB](stream-analytics-documentdb-output.md)介绍如何将 Cosmos DB 用作流分析输出。

在处理逻辑复杂的实际应用程序中，需要单独升级逻辑的某些部分，可以将多个流分析作业与事件中心一起作为中介事件 broker。

![ASA 复杂事件源应用](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

此模式改善了系统的复原能力和可管理性。 但是，即使流分析保证只进行一次处理，在中间事件中心内也可能出现重复事件的一小部分。 使用 lookback 窗口中的逻辑键将事件重复使用时，下游流分析作业非常重要。 有关事件传递的详细信息，请参阅[事件传递保证](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)引用。

## <a name="use-reference-data-for-application-customization"></a>使用引用数据进行应用程序自定义

Azure 流分析引用数据功能专门用于最终用户自定义，例如警报阈值、处理规则和[现成](geospatial-scenarios.md)。 应用程序层可以接受参数更改，并将其存储在 SQL 数据库中。 流分析作业定期查询数据库中的更改，并使自定义参数可通过引用数据联接进行访问。 有关如何使用引用数据进行应用程序自定义的详细信息，请参阅[SQL 引用数据](sql-reference-data.md)和[引用数据加入](/stream-analytics-query/reference-data-join-azure-stream-analytics)。

此模式还可用于实现一个规则引擎，其中规则的阈值是从引用数据中定义的。 有关规则的详细信息，请参阅[在 Azure 流分析中处理基于阈值的可配置规则](stream-analytics-threshold-based-rules.md)。

![ASA 引用数据应用](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>将机器学习添加到你的实时见解

Azure 流分析的内置异常情况[检测模型](stream-analytics-machine-learning-anomaly-detection.md)是一种简便的方法，可用于向实时应用程序引入机器学习。 有关更广泛的机器学习需求，请参阅[Azure 流分析与 Azure 机器学习评分服务集成](stream-analytics-machine-learning-integration-tutorial.md)。

对于想要将在线培训和评分并入同一流分析管道的高级用户，请参阅此示例，了解如何实现[线性回归](stream-analytics-high-frequency-trading.md)。

![ASA 机器学习应用](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>近乎实时的数据仓库

另一种常见模式是实时数据仓库，也称为流式数据仓库。 除了在应用程序中到达事件中心和 IoT 中心的事件外， [IoT Edge 上运行的 Azure 流分析](stream-analytics-edge.md)可用于满足数据清理、数据缩减、数据存储和转发需求。 在 IoT Edge 上运行的流分析可以适当地处理系统中的带宽限制和连接问题。 SQL 输出适配器可用于输出到 SQL 数据仓库;但是，最大吞吐量限制为 10 MB/s。

![ASA 数据仓库](media/stream-analytics-solution-patterns/datawarehousing.png)

降低吞吐量的一种方法是将事件存档到 Azure Blob 存储，然后将[其导入到具有 Polybase 的 SQL 数据仓库](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md)。 你必须通过按[时间戳](stream-analytics-custom-path-patterns-blob-storage-output.md)并定期导入，将流分析中的输出手动汇聚到 blob 存储，并将数据从 blob 存储导入到 SQL 数据仓库。

在此使用模式下，Azure 流分析用作近乎实时的 ETL 引擎。 新到达的事件将持续转换并存储，以供下游分析服务使用。

![ASA 高吞吐量数据仓库](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>存档实时数据以进行分析

大多数数据科学和分析活动仍处于脱机状态。 可以通过 Azure 流分析将数据存档 Azure Data Lake Store Gen2 output 和 Parquet 输出格式。 此功能消除了将数据直接送进到 Azure Data Lake Analytics、Azure Databricks 和 Azure HDInsight 的摩擦。 在此解决方案中，Azure 流分析用作近乎实时的 ETL 引擎。 你可以使用各种计算引擎来浏览 Data Lake 中的存档数据。

![ASA 脱机分析](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>使用扩充的引用数据

对于 ETL 引擎，数据扩充通常是必需的。 Azure 流分析支持从 SQL 数据库和 Azure Blob 存储的[引用数据](stream-analytics-use-reference-data.md)扩充数据。 可以在 Azure Data Lake 和 SQL 数据仓库中为数据登陆完成数据扩充。

![ASA 脱机分析与数据扩充](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>已存档数据的操作见解

如果将脱机分析模式与接近实时的应用程序模式结合使用，则可以创建一个反馈循环。 反馈循环允许应用程序自动调整数据中的模式。 此反馈循环非常简单，只需要更改警报的阈值，或与重新训练机器学习模型一样复杂。 同一个解决方案体系结构可以应用于在云中运行的 ASA 作业和 IoT Edge。

![ASA insights 操作化](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>如何监视 ASA 作业

Azure 流分析作业可以运行24/7 以实时处理传入事件。 它的运行时间保证对于整个应用程序的运行状况来说是至关重要的。 尽管流分析是行业中唯一提供[99.9% 可用性保证](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/)的流式分析服务，但仍可能会产生一定程度的停机时间。 多年来，流分析引进了指标、日志和作业状态，以反映作业的运行状况。 所有这些服务都通过 Azure Monitor 服务进行处理，并且可以进一步导出到 OMS。 有关详细信息，请参阅[了解流分析作业监视以及如何监视查询](stream-analytics-monitoring.md)。

![ASA 监视](media/stream-analytics-solution-patterns/monitoring.png)

监视以下两个重要事项：

- [作业失败状态](job-states.md)

    首先，您需要确保作业正在运行。 如果没有作业处于 "正在运行" 状态，则不会生成新的度量值或日志。 作业可能会因各种原因而发生故障，其中包括具有高 SU 使用率级别（即资源不足）的情况。

- [水印延迟指标](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    此指标反映了处理管道落后的时间（以秒为单位）。 一些延迟是指固有的处理逻辑。 因此，监视增长趋势比监视绝对值更为重要。 稳定状态延迟应由应用程序设计来解决，而不是通过监视或警报解决。

出现故障时，活动日志和[诊断日志](stream-analytics-job-diagnostic-logs.md)是开始查找错误的最佳位置。

## <a name="build-resilient-and-mission-critical-applications"></a>构建复原和任务关键型应用程序

不管 Azure 流分析的 SLA 保证和运行端到端应用程序的谨慎，都将发生中断。 如果你的应用程序是任务关键型应用程序，则需要为中断做好准备，以便正常恢复。

对于通知应用程序，最重要的一点是检测下一个警报。 你可以选择在恢复时从当前时间重新启动作业，同时忽略过去的警报。 作业开始时间语义是第一个输出时间，而不是第一个输入时间。 输入将向后倒带适当的时间，以确保在指定时间完成第一个输出并正确。 因此，你不会获得部分聚合并意外触发警报。

你还可以选择在过去某个时间段内开始输出。 事件中心和 IoT 中心的保留策略都保留了合理的数据量，以允许从过去进行处理。 其缺点是您最多可以及时了解当前时间，并开始及时生成新警报。 随着时间的推移，数据很快就会丢失其价值，因此，迅速及时了解当前时间很重要。 可以通过两种方法快速进行捕获：

- 在捕获时预配更多资源（SU）。
- 从当前时间重新开始。

从当前重新启动很简单，只是在处理过程中保持空白。 以这种方式重新启动时，警报方案可能是正常的，但对于仪表板方案可能有问题，并且它是存档和数据仓库方案的一个非入门版。

预配更多资源可以加快处理速度，但使处理速率为电涌的影响非常复杂。

- 测试你的作业是否可缩放到更多的 SUs。 并非所有查询都是可缩放的。 需要确保查询已[并行](stream-analytics-parallelization.md)化。

- 请确保上游事件中心或 IoT 中心中有足够的分区，你可以添加更多的吞吐量单位（Tu）来扩展输入吞吐量。 请记住，每个事件中心 TU 以至于耗光输出速率为 2 MB/秒。

- 请确保已在输出接收器中预配了足够的资源（例如 SQL 数据库、Cosmos DB），因此它们不会在输出中限制浪涌，这有时会导致系统锁定。

最重要的一点是预测处理速率变化，在投入生产前测试这些方案，并准备好在故障恢复期间正确缩放处理。

在极大的情况下，传入事件都是延迟的，如果你已将延迟到达的窗口应用到作业，则[可能会删除所有延迟事件](stream-analytics-time-handling.md)。 在开始时，删除事件似乎是一种神秘的行为;但是，考虑流分析是一种实时处理引擎，它预期传入事件接近于时钟时间。 它必须删除违反这些约束的事件。

### <a name="lambda-architectures-or-backfill-process"></a>Lambda 体系结构或回填过程

幸运的是，可以使用以前的数据存档模式来正常处理这些后期事件。 其思路是存档作业会在到达时处理传入事件，并将事件存档到 Azure Blob 中的合适时间存储桶，或 Azure Data Lake Store 与其事件时间。 事件到达的时间并不重要，它永远不会被删除。 它将始终处于适当的时间存储桶中。 在恢复过程中，可以重新处理已存档的事件，并将结果回填到所选的存储。 这类似于实现 lambda 模式的方式。

![ASA 回填](media/stream-analytics-solution-patterns/backfill.png)

回填过程必须使用脱机批处理系统完成，这种系统最可能具有与 Azure 流分析不同的编程模型。 这意味着必须重新实现整个处理逻辑。

对于回填，至少应暂时将更多资源预配到输出接收器，以便处理比稳定状态处理需求更高的吞吐量。

|方案  |立即从现在重启  |从上次停止的时间重新开始 |立即重新启动，并包含存档事件|
|---------|---------|---------|---------|
|**仪表板建设**   |创建间隙    |正常中断时正常    |用于长时间服务中断 |
|**警报**   |可以接受 |正常中断时正常    |无需 |
|**事件采购应用** |可以接受 |正常中断时正常    |用于长时间服务中断 |
|**数据仓库**   |数据丢失  |可以接受 |无需 |
|**脱机分析**  |数据丢失  |可以接受 |无需|

## <a name="putting-it-all-together"></a>汇总

这并不难想象以上提到的所有解决方案模式都可以组合在一个复杂的端到端系统中。 组合系统可以包括仪表板、警报、事件源应用程序、数据仓库和脱机分析功能。

关键是要以可组合模式设计系统，以便可以单独构建、测试、升级和恢复每个子系统。

## <a name="next-steps"></a>后续步骤

你现在已经了解了使用 Azure 流分析的各种解决方案模式。 接下来，你可以进行深入了解并创建第一个流分析作业：

* [使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)。
* [使用 Azure PowerShell 创建流分析作业](stream-analytics-quick-create-powershell.md)。
* [使用 Visual Studio 创建流分析作业](stream-analytics-quick-create-vs.md)。
