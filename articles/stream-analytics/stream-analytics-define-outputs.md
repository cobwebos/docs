---
title: Azure 流分析的输出
description: 本文介绍可用于 Azure 流分析的数据输出选项。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: ade5972226a735ce5d093890091536baf02f5eff
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89068977"
---
# <a name="outputs-from-azure-stream-analytics"></a>Azure 流分析的输出

Azure 流分析作业由输入、查询和输出构成。 可以向多个输出类型发送转换的数据。 本文列出了支持的流分析输出。 设计流分析查询时，使用 [INTO 子句](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics)引用输出的名称。 你可以使用每个作业的单个输出，或每个流式处理作业的多个输出 (如果你需要它们) 通过将多个 INTO 子句添加到查询中。

若要创建、编辑和测试流分析作业输出，可使用 [Azure 门户](stream-analytics-quick-create-portal.md#configure-job-output)、[Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job)、[.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet)、[REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output) 和 [Visual Studio](stream-analytics-quick-create-vs.md)。

部分输出类型支持[分区](#partitioning)，并且[输出批大小](#output-batch-size)可变化以优化吞吐量。 下表显示了每种输出类型支持的功能：

| 输出类型 | 分区 | 安全性 | 
|-------------|--------------|----------|
|[Azure Data Lake Storage Gen 1](azure-data-lake-storage-gen1-output.md)|是|Azure Active Directory 用户 </br> MSI|
|[Azure SQL 数据库](sql-database-output.md)|是，但需要启用。|SQL 用户身份验证 </br> MSI (预览) |
|[Azure Synapse Analytics（预览）](azure-synapse-analytics-output.md)|否|SQL 用户身份验证|
|[Blob 存储和 Azure Data Lake 第2代](blob-storage-azure-data-lake-gen2-output.md)|是|MSI </br> 访问密钥|
|[Azure 事件中心](event-hubs-output.md)|是|访问密钥|
|[Power BI](power-bi-output.md)|否|Azure Active Directory 用户 </br> MSI|
|[Azure 表存储](table-storage-output.md)|是|帐户密钥|
|[Azure 服务总线队列](service-bus-queues-output.md)|是|访问密钥|
|[Azure 服务总线主题](service-bus-topics-output.md)|是|访问密钥|
|[Azure Cosmos DB](azure-cosmos-db-output.md)|是|访问密钥|
|[Azure Functions](azure-functions-output.md)|是|访问密钥|

## <a name="partitioning"></a>分区

流分析支持除 Power BI 之外的所有输出的分区。 有关分区键和输出编写器的数目的详细信息，请参阅你感兴趣的特定输出类型的文章。 在上一节中链接了所有输出文章。  

可以使用 (查看查询中的) 子句来控制输出编写器的数目 `INTO <partition count>` ，这在实现所需的作业拓扑时非常有用。 [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 如果输出适配器未分区，则在一个输入分区中缺少数据将导致延迟达到延迟时间。 在这种情况下，输出将合并到单个写入器，这可能会导致管道中出现瓶颈。 若要了解有关延迟到达策略的详细信息，请参阅 [Azure 流分析事件顺序注意事项](stream-analytics-out-of-order-and-late-events.md)。

## <a name="output-batch-size"></a>输出批大小

所有输出都支持批处理，但仅部分支持批处理大小。 Azure 流分析使用大小可变的批来处理事件和写入到输出。 通常流分析引擎不会一次写入一条消息，而是使用批来提高效率。 当传入和传出事件的速率较高时，流分析将使用更大的批。 输出速率低时，使用较小的批来保证低延迟。

## <a name="parquet-output-batching-window-properties"></a>Parquet 输出批处理窗口属性

使用 Azure 资源管理器模板部署或 REST API 时，两个批处理窗口属性为：

1. *timeWindow*

   每批的最长等待时间。 该值应该是 Timespan 的字符串。 例如，"00:02:00" 表示两分钟。 在此之后，即使未满足最小行要求，也会将该批写入输出。 默认值为1分钟，允许的最大值为2小时。 如果 blob 输出具有路径模式频率，则等待时间不能超出分区时间范围。

2. *sizeWindow*

   每批的最小行数。 对于 Parquet，每个批处理都会创建一个新的文件。 当前默认值为 2000 行，允许的最大值为 10000 行。

这些批处理窗口属性仅受 API 版本 **2017-04-01-预览版**的支持。 下面是 REST API 调用的 JSON 有效负载的示例：

```json
"type": "stream",
      "serialization": {
        "type": "Parquet",
        "properties": {}
      },
      "timeWindow": "00:02:00",
      "sizeWindow": "2000",
      "datasource": {
        "type": "Microsoft.Storage/Blob",
        "properties": {
          "storageAccounts" : [
          {
            "accountName": "{accountName}",
            "accountKey": "{accountKey}",
          }
          ],
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>
> [快速入门：使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
