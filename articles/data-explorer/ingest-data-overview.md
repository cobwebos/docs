---
title: Azure 数据资源管理器数据引入
description: 了解在 Azure 数据资源管理器中引入（加载）数据的不同方式
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 90387a033a43c627be4ce69a93ee37c5b959732d
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091785"
---
# <a name="azure-data-explorer-data-ingestion"></a>Azure 数据资源管理器数据引入

数据引入是用于从一个或多个源加载数据记录以在 Azure 数据资源管理器中创建或更新表的过程。 引入后，数据即可用于查询。 下图显示了用于在 Azure 数据资源管理器中运行的端到端流，包括数据引入。

![数据流](media/ingest-data-overview/data-flow.png)

Azure 数据资源管理器数据管理服务负责数据引入的，该服务提供以下功能：

1. **拉取数据**：从外部源（事件中心）拉取数据或从 Azure 队列中读取引入请求。

1. **批处理**：批量数据流入相同的数据库和表中，以优化引入吞吐量。

1. **验证**：初步验证和格式转换（如有必要）。

1. **数据操作**：匹配架构、组织、编制索引、编码和压缩数据。

1. **引入流中的持久性点**：在暂时性故障时管理引擎上的引入负载并处理重试。

1. **提交数据引入**：使数据可用于查询。

## <a name="ingestion-methods"></a>引入方法

Azure 数据资源管理器支持多种引入方法，每种方法都有自己的目标方案、优点和缺点。 Azure 数据资源管理器提供用于常见服务的管道和连接器、使用 SDK 的编程引入以及以探索为目的对引擎进行的直接访问。

### <a name="ingestion-using-pipelines-connectors-and-plugins"></a>使用管道、连接器和插件的引入

Azure 数据资源管理器目前支持：

* 事件网格管道，该管道可以使用 Azure 门户中的管理向导进行管理。 有关详细信息，请参阅[将 Azure Blob 引入 Azure 数据资源管理器](ingest-data-event-grid.md)。

* 事件中心管道，该管道可以使用 Azure 门户中的管理向导进行管理。 有关详细信息，请参阅[将数据从事件中心引入到 Azure 数据资源管理器](ingest-data-event-hub.md)。

* Logstash 插件，请参阅[将数据从 Logstash 引入 Azure 数据资源管理器](ingest-data-logstash.md)。

* Kafka 连接器，请参阅[将数据从 Kafka 引入到 Azure 数据资源管理器](ingest-data-kafka.md)。

### <a name="ingestion-using-integration-services"></a>使用集成服务的引入

* Azure 数据工厂 (ADF) 是 Azure 中分析工作负荷的完全托管数据集成服务，可使用[支持的数据存储和格式](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)向/从 Azure 数据资源管理器复制数据。 有关详细信息，请参阅[将数据从 Azure 数据工厂复制到 Azure 数据资源管理器](/azure/data-explorer/data-factory-load-data)。

### <a name="programmatic-ingestion"></a>编程引入

Azure 数据资源管理器提供可用于查询和数据引入的 SDK。 通过在引入期间和之后尽量减少存储事务，编程引入得到优化，可降低引入成本 (COG)。

**可用的 SDK 和开源项目**：

Kusto 提供可与以下项一起用于引入和查询数据的客户端 SDK：

* [Python SDK](/azure/kusto/api/python/kusto-python-client-library)

* [.NET SDK](/azure/kusto/api/netfx/about-the-sdk)

* [Java SDK](/azure/kusto/api/java/kusto-java-client-library)

* [Node SDK](/azure/kusto/api/node/kusto-node-client-library)

* [REST API](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**编程引入方法**：

* 通过 Azure 数据资源管理器数据管理服务引入数据（高吞吐量和可靠引入）：

    [**批量引入**](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample)（由 SDK 提供）：客户端将数据上传到 Azure Blob 存储（由 Azure 数据资源管理器数据管理服务指定）并向 Azure 队列发布通知。 建议使用批量引入实现大容量、可靠和低成本的数据引入。

* 直接将数据引入到 Azure 数据资源管理器引擎（最适合用于探索和原型制作）：

  * **内联引入**：包含带内数据的控制命令 (.ingest inline) 用于临时测试目的。

  * **从查询引入**：指向查询结果的控制命令 (.set、.set-or-append、.set-or-replace) 用于生成报表或小型临时表。

  * **从存储引入**：在外部存储数据（例如，Azure Blob 存储）的控制命令 (.ingest into) 支持高效的批量引入数据。

**不同方法的延迟情况**：

| 方法 | Latency |
| --- | --- |
| **内联引入** | 即时 |
| **从查询引入** | 查询时间 + 处理时间 |
| **从存储引入** | 下载时间 + 处理时间 |
| **排队引入** | 批处理时间 + 处理时间 |
| |

处理时间取决于数据大小，通常不超过几秒钟。 批处理时间默认为 5 分钟。

## <a name="choosing-the-most-appropriate-ingestion-method"></a>选择最合适的引入方法

在开始引入数据之前，应该思考以下问题。

* 我的数据存放在什么位置？ 
* 采用的数据格式是什么，可以更改吗？ 
* 要查询哪些必填字段？ 
* 预期的数据量和速度是多少？ 
* 预期有多少事件类型（反映为表数量）？ 
* 事件架构的预期更改频率是多少？ 
* 有多少个节点将生成数据？ 
* 源操作系统是什么？ 
* 有哪些延迟要求？ 
* 可以使用现有的托管引入管道吗？ 

对于具有基于消息服务（如事件中心和 IoT 中心）的现有基础结构的组织，使用连接器可能是最合适的解决方案。 排队引入适合大数据量。

## <a name="supported-data-formats"></a>受支持的数据格式

对于“从查询引入”以外的所有引入方法，请设置适当的数据格式，使 Azure 数据资源管理器能够对其进行分析。 
* 支持的数据格式包括： TXT、CSV、TSV、TSVE、PSV、SCSV、SOH、JSON （行分隔、多行）、Avro 和 Parquet。 
* 支持 ZIP 和 GZIP 压缩。

> [!NOTE]
> 正在引入数据时，根据目标表列推断数据类型。 如果记录不完整或者无法将字段解析为所需的数据类型，则将使用 NULL 值填充相应的表列。

## <a name="ingestion-recommendations-and-limitations"></a>引入建议和限制

* 引入数据的有效保留策略衍生自数据库的保留策略。 请参阅[保留策略](/azure/kusto/concepts/retentionpolicy)获取详细信息。 引入数据需要“表引入器”或“数据库引入器”权限。
* 引入支持的最大文件大小为 5 GB。 建议引入 100 MB 到 1 GB 的文件。

## <a name="schema-mapping"></a>架构映射

架构映射有助于将源数据字段绑定到目标表列。

* [CSV 映射](/azure/kusto/management/mappings?branch=master#csv-mapping)（可选）适用于所有基于序号的格式。 可以使用引入命令参数执行该功能，或者[在表中预创建](/azure/kusto/management/tables?branch=master#create-ingestion-mapping)并从引入命令参数进行引用。
* 可以使用引入命令参数执行 [JSON 映射](/azure/kusto/management/mappings?branch=master#json-mapping)（强制）和 [Avro 映射](/azure/kusto/management/mappings?branch=master#avro-mapping)（强制）。 可以[在表上预先创建](/azure/kusto/management/tables#create-ingestion-mapping)它们并从引入命令参数引用。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将数据从事件中心引入到 Azure 数据资源管理器](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [使用事件网格订阅将数据引入到 Azure 数据资源管理器](ingest-data-event-grid.md)

> [!div class="nextstepaction"]
> [将数据从 Kafka 引入到 Azure 数据资源管理器](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [使用 Azure 数据资源管理器 Python 库引入数据](python-ingest-data.md)

> [!div class="nextstepaction"]
> [使用 Azure 数据资源管理器 Node 库引入数据](node-ingest-data.md)

> [!div class="nextstepaction"]
> [使用 Azure 数据资源管理器 .NET Standard SDK（预览版）引入数据](net-standard-ingest-data.md)

> [!div class="nextstepaction"]
> [将数据从 Logstash 引入 Azure 数据资源管理器](ingest-data-logstash.md)
