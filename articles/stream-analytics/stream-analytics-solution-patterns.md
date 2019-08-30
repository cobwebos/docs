---
title: Azure 流分析解决方案模式
description: 了解 Azure 流分析的不同常见解决方案模式。
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: cbc9ffe9510cf0888e8d8b62ea112b6517117eed
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173039"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Azure 流分析解决方案模式

与 Azure 中的其他许多服务一样，最好是将流分析与其他服务搭配使用，以创建更大的端到端解决方案。 本文介绍简单的 Azure 流分析解决方案和各种体系结构模式。 可以基于这些模式构建项目以开发更复杂的解决方案。 本文所述的模式可用于各种方案。 [Azure 解决方案体系结构](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics)中提供了特定于方案的模式示例。

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>创建流分析作业以驱动实时仪表板体验

使用 Azure 流分析可以快速创建实时仪表板和警报。 一个简单的解决方案就能从事件中心或 IoT 中心引入事件，并[在 Power BI 仪表板中馈送流数据集](/power-bi/service-real-time-streaming)。 有关详细信息，请参阅详细教程[使用流分析来分析通话数据以及在 Power BI 仪表板中可视化结果](stream-analytics-manage-job.md)。

![ASA Power BI 仪表板](media/stream-analytics-solution-patterns/pbidashboard.png)

只需在 Azure 门户中花费几分钟时间即可构建此解决方案。 无需编写大量的代码，并可以使用 SQL 语言来表达业务逻辑。

使用此解决方案模式时，从事件源到浏览器中的 Power BI 仪表板的延迟最低。 Azure 流分析是唯一一个具有此内置功能的 Azure 服务。

## <a name="use-sql-for-dashboard"></a>对仪表板使用 SQL

Power BI 仪表板提供较低的延迟，但无法用于生成完备的 Power BI 报表。 常见报告模式是先将数据输出到 SQL 数据库。 然后使用 Power BI 的 SQL 连接器在 SQL 中查询最新数据。

![ASA SQL 仪表板](media/stream-analytics-solution-patterns/sqldashboard.png)

使用 SQL 数据库可以获得更大的灵活性，低价是延迟略有提高。 此解决方案最适合延迟要求超过一秒的作业。 此方法可以最大程度地发挥 Power BI 的功能，以进一步细分报表数据，并提供多得多的可视化选项。 此外，你可以灵活地使用其他仪表板解决方案，例如 Tableau。

SQL 不是一种高吞吐量的数据存储。 从 Azure 流分析到 SQL 数据库的最大吞吐量目前大约为 24 MB/秒。 如果解决方案中的事件源以更高的速率生成数据，则你需要使用流分析中的处理逻辑来降低到 SQL 的输出速率。 可以结合时态联接和分析函数使用筛选、开窗聚合、模式匹配等技术。 可以使用[到 Azure SQL 数据库的 Azure 流分析输出](stream-analytics-sql-output-perf.md)中所述的技术进一步优化到 SQL 的输出速率。

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>使用事件消息传送将实时见解整合到应用程序中

流分析的第二种最常见用途是生成实时警报。 在此解决方案模式中，可以使用流分析中的业务逻辑来检测[时态和空间模式](stream-analytics-geospatial-functions.md)或[异常](stream-analytics-machine-learning-anomaly-detection.md)，然后生成警报信号。 但是，与其中的流分析使用 Power BI 作为首选终结点的仪表板解决方案不同，可以使用许多的中间数据接收器。 这些接收器包括事件中心、服务总线和 Azure Functions。 应用程序构建者需要确定哪个数据接收器最适合自己的方案。

必须实现下游事件使用者逻辑才能在现有的业务工作流中生成警报。 由于可以在 Azure Functions 中实现自定义逻辑，Azure Functions 是执行这种集成的最快方式。 在[从 Azure 流分析作业运行 Azure Functions](stream-analytics-with-azure-functions.md) 中可以找到有关使用 Azure 函数作为流分析作业输出的教程。 Azure Functions 还支持各种类型的通知，包括短信和电子邮件。 逻辑应用也可用于此类集成，它在流分析与逻辑应用之间使用事件中心。

![ASA 事件消息传送应用](media/stream-analytics-solution-patterns/eventmessagingapp.png)

另一方面，事件中心提供最灵活的集成点。 其他许多服务（例如 Azure 数据资源管理器和时序见解）可以使用来自事件中心的事件。 服务可以直接从 Azure 流分析连接到事件中心接收器，以完成解决方案。 对于此类集成方案，事件中心也是 Azure 上提供的最高吞吐量消息传送代理。

## <a name="dynamic-applications-and-websites"></a>动态应用程序和网站

可以使用 Azure 流分析和 Azure SignalR 服务创建自定义的实时可视化效果，例如仪表板或地图可视化效果。 使用 SignalR 可以更新 Web 客户端和实时显示动态内容。

![ASA 动态应用](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>通过数据存储将实时见解整合到应用程序中

当今的大多数 Web 服务和 Web 应用程序都使用请求/响应模式来为呈现层提供服务。 请求/响应模式很容易构建，可以使用无状态前端和可缩放存储（例如 Cosmos DB）以较短的响应时间对其进行缩放。

高数据量通常会在基于 CRUD 的系统中产生性能瓶颈。 [事件寻源解决方案模式](/azure/architecture/patterns/event-sourcing)可用于解决性能瓶颈。 此外，从传统的数据存储中提取时态模式和见解也比较困难，且效率低下。 大量数据驱动的新式应用程序通常采用基于数据流的体系结构。 作为动态数据的计算引擎，Azure 流分析是该体系结构中的关键所在。

![ASA 事件寻源应用](media/stream-analytics-solution-patterns/eventsourcingapp.png)

在此解决方案模式中，事件由 Azure 流分析处理并聚合到数据存储中。 应用层使用传统的请求/响应模式来与数据存储交互。 由于流分析能够实时处理大量事件，因此应用程序具有高度可伸缩性，无需大量占用数据存储层。 数据存储层在本质上是系统中的具体化视图。 [到 Azure Cosmos DB 的 Azure 流分析输出](stream-analytics-documentdb-output.md)介绍了如何将 Cosmos DB 用作流分析输出。

在处理逻辑比较复杂，且需要单独升级逻辑的特定部分的实际应用程序中，可将多个流分析作业作为中间事件代理与事件中心组合到一起。

![ASA 复杂事件寻源应用](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

此模式改善了系统的复原能力和可管理性。 但是，尽管流分析保证只处理一次，但重复事件仍可能会进入中间事件中心（这种情况很少见）。 下游流分析作业非常在回查窗口中使用逻辑键删除重复事件。 有关事件传递的详细信息，请参阅[事件传递保证](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)参考文章。

## <a name="use-reference-data-for-application-customization"></a>使用引用数据进行应用程序自定义

Azure 流分析引用数据功能是专门为警报阈值、处理规则和[地理围栏](geospatial-scenarios.md)等最终用户自定义操作设计的。 应用层可以接受参数更改，并将其存储在 SQL 数据库中。 流分析作业定期查询数据库中的更改，并使得自定义参数可通过引用数据联接操作进行访问。 有关如何使用引用数据进行应用程序自定义的详细信息，请参阅 [SQL 引用数据](sql-reference-data.md)和[引用数据联接](/stream-analytics-query/reference-data-join-azure-stream-analytics)。

此模式还可用于实现从引用数据定义规则阈值的规则引擎。 有关规则的详细信息，请参阅[在 Azure 流分析中处理基于阈值的可配置规则](stream-analytics-threshold-based-rules.md)。

![ASA 引用数据应用](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>将机器学习添加到实时见解

Azure 流分析的内置[异常情况检测模型](stream-analytics-machine-learning-anomaly-detection.md)是将机器学习引入实时应用程序的简便方法。 如需满足更广泛的机器学习需求，请参阅 [Azure 流分析与 Azure 机器学习评分服务的集成](stream-analytics-machine-learning-integration-tutorial.md)。

对于想要将联机训练和评分整合到同一流分析管道的高级用户，请参阅有关如何使用[线性回归](stream-analytics-high-frequency-trading.md)实现此目的的示例。

![ASA 机器学习应用](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>近实时数据仓库

另一种常见模式是实时数据仓库，也称为流数据仓库。 除了从应用程序传入事件中心和 IoT 中心的事件以外，还可使用 [IoT Edge 上运行的 Azure 流分析](stream-analytics-edge.md)来满足数据清理、数据化减、数据存储和转发需求。 在 IoT Edge 上运行的流分析可以正常处理系统中的带宽限制和连接问题。 SQL 输出适配器可用于将数据输出到 SQL 数据仓库，但是，最大吞吐量限制为 10 MB/秒。

![ASA 数据仓库](media/stream-analytics-solution-patterns/datawarehousing.png)

改善吞吐量（但会在一定程度上增大延迟）的一种方法是将事件存档到 Azure Blob 存储中，然后[使用 Polybase 将其导入到 SQL 数据仓库](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md)。 必须通过[按时间戳存档数据](stream-analytics-custom-path-patterns-blob-storage-output.md)并定期导入，将从流分析发往 blob 存储的输出，以及从 blob 存储发往 SQL 数据仓库的输入拼结到一起。

在此使用模式中，Azure 流分析用作近实时 ETL 引擎。 新抵达的事件将持续转换并存储，以供下游分析服务使用。

![ASA 高吞吐量数据仓库](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>存档实时数据用于分析

大部分数据科学和分析活动仍脱机进行。 Azure 流分析可以通过 Azure Data Lake Store Gen2 输出和 Parquet 输出格式存档数据。 此功能消除了将数据直接馈送到 Azure Data Lake Analytics、Azure Databricks 和 Azure HDInsight 所造成的冲突。 在此解决方案中，Azure 流分析用作近实时 ETL 引擎。 可以使用各种计算引擎来探索 Data Lake 中存档的数据。

![ASA 脱机分析](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>使用引用数据进行扩充

ETL 引擎通常需要数据扩充。 Azure 流分析支持使用 SQL 数据库和 Azure Blob 存储中的[引用数据](stream-analytics-use-reference-data.md)扩充数据。 可以扩充进入 Azure Data Lake 和 SQL 数据仓库的数据。

![使用数据扩充进行 ASA 脱机分析](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>从存档的数据操作化见解

如果将脱机分析模式与近实时应用程序模式相结合，则可以创建一个反馈循环。 该反馈循环可让应用程序自动调整数据的更改模式。 此反馈循环可以十分简单，只需更改警报的阈值；也可以十分复杂，需要重新训练机器学习模型。 可将同一个解决方案体系结构应用到在云中和 IoT Edge 中运行的 ASA 作业。

![ASA 见解操作化](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>如何监视 ASA 作业

可以不间断地运行 Azure 流分析作业，以实时连续处理传入的事件。 流分析的运行时间保证对于整个应用程序的运行状况而言至关重要。 尽管流分析是行业中唯一提供 [99.9% 可用性保证](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/)的流分析服务，但你仍可能会遇到一定程度的停机时间。 多年来，流分析陆续引进了指标、日志和作业状态来反映作业的运行状况。 所有这些信息都可以在 Azure Monitor 服务中查看，并可以进一步导出到 OMS。 有关详细信息，请参阅[了解流分析作业监视以及如何监视查询](stream-analytics-monitoring.md)。

![ASA 监视](media/stream-analytics-solution-patterns/monitoring.png)

需要监视两项关键信息：

- [作业失败状态](job-states.md)

    首先且最重要的是，需要确保该作业正在运行。 如果作业不是处于运行状态，则不会生成新的指标或日志。 作业可能因各种原因而更改为失败状态，包括 SU 利用率较高（即资源不足）。

- [水印延迟指标](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    此指标反映处理管道滞后于挂钟时间的程度（秒）。 某些延迟归因于固有的处理逻辑。 因此，监视趋势的增长比监视绝对值重要得多。 应该通过应用程序设计而不是监视或警报来解决稳定态延迟。

发生失败时，活动日志和[诊断日志](stream-analytics-job-diagnostic-logs.md)是开始查找错误的最佳位置。

## <a name="build-resilient-and-mission-critical-applications"></a>构建可复原的任务关键型应用程序

尽管 Azure 流分析提供 SLA 保证，也不管你是如何谨慎地运行端到端应用程序，中断仍会不时地发生。 对于任务关键型应用程序，需要为中断做好准备，以便到时能够正常恢复。

对于警报应用程序，最重要的一点是检测下一条警报。 可以选择从恢复时的当前时间重启作业，并忽略以往的警报。 作业启动时间语义是根据第一个输出时间而不是第一个输入时间确定的。 输入将后退适当的一段时间，以保证指定时间的第一个输出完成且正确。 因此，你不会收到部分聚合并意外触发警报。

还可以选择从过去的某个时间段启动输出。 事件中心和 IoT 中心的保留策略会保留合理的数据量，以便能够从过去的时间进行处理。 要权衡的利弊是能够以最多的速度赶上当前时间，并及时开始生成新警报。 数据很快就会失去其价值，因此，快速赶上当前时间非常重要。 可通过两种方式快速赶上进度：

- 赶上进度时预配更多的资源 (SU)
- 从当前时间重启。

从当前时间重启会很简单，弊端是处理期间会留下间隙。 以这种方式重启对于警报方案可能不会有问题，但在仪表板方案中可能会有问题，而对于存档和数据仓库方案，它根本不会成功。

预配更多资源可以加速处理，处理速度激增造成的影响非常复杂。

- 请测试作业是否可缩放到更多的 SU。 并非所有查询都可缩放。 需确保查询已[并行化](stream-analytics-parallelization.md)。

- 确保上游事件中心或 IoT 中心内有足够的分区，以便可以添加更多的吞吐量单位 (TU) 来增大输入吞吐量。 请记住，每个事件中心 TU 可以应对的最大输出速率为 2 MB/秒。

- 确保在输出接收器（例如 SQL 数据库、Cosmos DB）中预配足够的资源，以免对输出激增造成限制（这有时会导致系统锁定）。

最重要的是预测处理速率的变化，在投入生产之前测试这些方案，并准备好在故障恢复期间正确缩放处理能力。

在极端情况下，如果传入的事件全部延迟，并且你对作业应用了延迟抵达时限，则[可能会删除所有延迟的事件](stream-analytics-time-handling.md)。 最初，删除事件看上去像是一种诡异的行为；但是，在考虑到流分析是一种实时处理引擎的情况下，预期事件传入时间与挂钟时间接近。 它必须删除违反这些约束的事件。

### <a name="lambda-architectures-or-backfill-process"></a>Lambda 体系结构或回填过程

幸运的是，前面的数据存档模式可用于正常处理这些滞后的事件。 思路是，存档作业会在抵达时处理传入事件，并在事件时间将事件存档到 Azure Blob 或 Azure Data Lake Store 中适当的时间桶。 事件在多晚的时间抵达并不重要，它永远不会被删除。 它始终会进入适当的时间桶。 在恢复过程中，可以重新处理已存档的事件，并将结果回填到所选的存储。 这类似于实现 lambda 模式的方式。

![ASA 回填](media/stream-analytics-solution-patterns/backfill.png)

必须使用脱机批处理系统完成回填过程，该系统的编程模型很可能与 Azure 流分析不同。 这意味着，必须重新实现整个处理逻辑。

对于回填，至少应暂时在输出接收器中预配更多资源来处理比稳定态处理更高的吞吐量，这一点仍很重要。

|方案  |仅从现在重启  |从上次停止时间重启 |从现在重启，并使用存档的事件回填|
|---------|---------|---------|---------|
|**仪表板**   |产生间隙    |容许短时间的中断    |在长时间中断时使用 |
|**警报**   |可接受 |容许短时间的中断    |不必要 |
|**事件寻源应用** |可接受 |容许短时间的中断    |在长时间中断时使用 |
|**数据仓库**   |数据丢失  |可接受 |不必要 |
|**脱机分析**  |数据丢失  |可接受 |不必要|

## <a name="putting-it-all-together"></a>汇总

不难想象，可将上述所有解决方案模式一起组合到一个复杂的端到端系统中。 该组合系统可以包括仪表板、警报、事件寻源应用程序、数据仓库和脱机分析功能。

关键是在可组合的模式中设计系统，以便可以单独构建、测试、升级和恢复每个子系统。

## <a name="next-steps"></a>后续步骤

现在，你已了解使用 Azure 流分析的各种解决方案模式。 接下来，你可以进行深入了解并创建第一个流分析作业：

* [使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)。
* [使用 Azure PowerShell 创建流分析作业](stream-analytics-quick-create-powershell.md)。
* [使用 Visual Studio 创建流分析作业](stream-analytics-quick-create-vs.md)。
