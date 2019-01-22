---
title: Azure 数据资源管理器数据引入
description: 了解在 Azure 数据资源管理器中引入（加载）数据的不同方式
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2019
ms.openlocfilehash: 8d5fc1c579fd09f1a71d63dce4d1673ef5a8652b
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354614"
---
# <a name="azure-data-explorer-data-ingestion"></a>Azure 数据资源管理器数据引入

数据引入是用于从一个或多个源加载数据记录以在 Azure 数据资源管理器中创建或更新表的过程。 引入后，数据即可用于查询。 下图显示了用于在 Azure 数据资源管理器中运行的端到端流，包括数据引入。

![数据流](media/ingest-data-overview/data-flow.png)

Azure 数据资源管理器数据管理服务负责数据引入的，该服务提供以下功能：

1. **数据提取**：从外部源（事件中心）拉取数据或从 Azure 队列中读取引入请求。

1. **批处理**：批数据流入相同的数据库和表中，以优化引入吞吐量。

1. **验证**：初步验证和格式转换（若需要）。

1. **数据处理**：匹配架构、组织、编制索引、编码和压缩数据。

1. **引入流中的持久性点**：在暂时性故障时管理引擎上的引入负载并处理重试。

1. **提交数据引入**：使数据可用于查询。

## <a name="ingestion-methods"></a>引入方法

Azure 数据资源管理器支持多种引入方法，每种方法都有自己的目标方案、优点和缺点。 Azure 数据资源管理器提供用于常见服务的管道和连接器、使用 SDK 的编程引入以及以探索为目的对引擎进行的直接访问。

### <a name="ingestion-using-pipelines"></a>使用管道的引入

Azure 数据资源管理器当前支持事件中心管道，该管道可以使用 Azure 门户中的管理向导进行管理。 有关详细信息，请参阅[快速入门：将数据从事件中心引入到 Azure 数据资源管理器](ingest-data-event-hub.md)。

### <a name="ingestion-using-connectors-and-plugins"></a>使用连接器和插件的引入

* Azure 数据资源管理器支持 Logstash 插件。 有关详细信息，请参阅 [Azure 数据资源管理器的 Logstash 输出插件](https://github.com/Azure/logstash-output-kusto/blob/master/README.md)。

* Azure 数据资源管理器支持 Kafka 连接器。 有关详细信息，请参阅[快速入门：将数据从 Kafka 引入到 Azure 数据资源管理器](ingest-data-kafka.md)

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

对于具有基于事件中心等消息传递服务的现有基础结构的组织，使用连接器可能是最合适的解决方案。 排队引入适合大数据量。

## <a name="supported-data-formats"></a>受支持的数据格式

对于“从查询引入”以外的所有引入方法，请设置适当的数据格式，使 Azure 数据资源管理器能够对其进行分析。 支持的数据格式包括：

* CSV、TSV、PSV、SCSV、SOH
* JSON（行分隔，多行）、Avro
* ZIP 和 GZIP 

> [!NOTE]
> 正在引入数据时，根据目标表列推断数据类型。 如果记录不完整或者无法将字段解析为所需的数据类型，则将使用 NULL 值填充相应的表列。

## <a name="ingestion-recommendations-and-limitations"></a>引入建议和限制

* 引入数据的有效保留策略衍生自数据库的保留策略。 请参阅[保留策略](/azure/kusto/concepts/retentionpolicy)获取详细信息。 引入数据需要“表引入器”或“数据库引入器”权限。
* 引入支持的最大文件大小为 5 GB。 建议引入 100 MB 到 1 GB 的文件。

## <a name="schema-mapping"></a>架构映射

架构映射有助于将源数据字段绑定到目标表列。

* [CSV 映射](/azure/kusto/management/mappings?branch=master#csv-mapping)（可选）适用于所有基于序号的格式。 可以使用引入命令参数执行该功能，或者[在表中预创建](/azure/kusto/management/tables?branch=master#create-ingestion-mapping)并从引入命令参数进行引用。
* [JSON 映射](/azure/kusto/management/mappings?branch=master#json-mapping)（必需）和 [Avro 映射](/azure/kusto/management/mappings?branch=master#avro-mapping)（必需）可以使用引入命令参数来执行，或者[在表中预创建](/azure/kusto/management/tables#create-ingestion-mapping)并从引入命令参数进行引用。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [快速入门：将数据从事件中心引入到 Azure 数据资源管理器](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [快速入门：将数据从 Kafka 引入到 Azure 数据资源管理器](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [快速入门：使用 Azure 数据资源管理器 Python 库引入数据](python-ingest-data.md)

> [!div class="nextstepaction"]
> [快速入门：使用 Azure 数据资源管理器 Node 库引入数据](node-ingest-data.md)

> [!div class="nextstepaction"]
> [快速入门：使用 Azure 数据资源管理器 .NET Standard SDK（预览版）引入数据](net-standard-ingest-data.md)
