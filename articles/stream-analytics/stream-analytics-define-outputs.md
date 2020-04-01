---
title: 了解 Azure 流分析的输出
description: 本文介绍 Azure 流分析提供的数据输出选项，包括用于分析结果的 Power BI。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 4517f85fae278bd8bc15a9586d9dc0202e7dfe56
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475227"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>了解 Azure 流分析的输出

本文介绍适用于 Azure 流分析作业的输出类型。 输出可帮助存储和保存流分析作业的结果。 使用输出数据，可进一步进行业务分析和数据的数据仓储。

设计流分析查询时，使用 [INTO 子句](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics)引用输出的名称。 可针对每个作业使用单个输出，也可通过在查询中提供多个 INTO 子句，针对每个流式处理作业使用多个输出（如果需要）。

要创建、编辑和测试流分析作业输出，可以使用[Azure 门户](stream-analytics-quick-create-portal.md#configure-job-output)[、Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job) [、.NET API、REST](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet) [API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)和[可视化工作室](stream-analytics-quick-create-vs.md)。

某些输出类型支持[分区](#partitioning)。 [输出批次大小](#output-batch-size)因优化吞吐量而异。


## <a name="azure-data-lake-storage-gen-1"></a>Azure 数据存储第 1 代

流分析支持[Azure 数据存储第 1 代](../data-lake-store/data-lake-store-overview.md)。 Azure 数据湖存储是一个企业范围的超大规模存储库，用于大数据分析工作负载。 您可以使用"数据湖存储"存储任何大小、类型和引入速度的数据，以便进行操作和探索性分析。 需要授权流分析才能访问数据湖存储。

流分析的 Azure 数据湖存储输出当前不在 Azure 中国 21Vianet 和 Azure 德国 （T-Systems 国际）区域中提供。

下表列出了属性名称及其说明，以配置数据湖存储第 1 代输出。   

| 属性名称 | 说明 |
| --- | --- |
| 输出别名 | 用于查询的友好名称，用于将查询输出定向到数据湖存储。 |
| 订阅 | 包含 Azure 数据湖存储帐户的订阅。 |
| 帐户名 | 发送输出的数据湖存储帐户的名称。 您会收到订阅中可用的数据湖应用商店帐户的下拉列表。 |
| 路径前缀模式 | 用于在指定的 Data Lake 应用商店帐户中写入文件的文件路径。 您可以指定 [date] 和 [时间] 变量的一个或多个实例：<br /><ul><li>示例 1：folder1/logs/{date}/{time}</li><li>示例 2：folder1/logs/{date}</li></ul><br />创建的文件夹结构的时间戳遵循 UTC 而不是本地时间。<br /><br />如果文件路径模式不包含尾随斜杠 （/），则文件路径中的最后一个模式将被视为文件名前缀。 <br /><br />在这些情况下会创建新文件：<ul><li>在输出架构中进行更改</li><li>作业的外部或内部重新启动</li></ul> |
| 日期格式 | 可选。 如果在前缀路径中使用日期令牌，可以选择组织文件所采用的日期格式。 示例：YYYY/MM/DD |
|时间格式 | 可选。 如果在前缀路径中使用时间令牌，可以指定组织文件所采用的时间格式。 目前唯一支持的值是 HH。 |
| 事件序列化格式 | 输出数据的序列化格式。 支持 JSON、CSV 和 Avro。|
| 编码 | 如果使用 CSV 或 JSON 格式，则必须指定一种编码格式。 目前只支持 UTF-8 这种编码格式。|
| 分隔符 | 仅适用于 CSV 序列化。 流分析支持大量的常见分隔符以对 CSV 数据进行序列化。 支持的值为逗号、分号、空格、制表符和竖线。|
| 格式 | 仅适用于 JSON 序列化。 **线分隔**指定通过将每个 JSON 对象由新行分隔来格式化输出。 如果选择 **"行分隔**"，则一次读取一个对象的 JSON。 整个内容本身将不是有效的 JSON。  **数组**指定输出格式化为 JSON 对象的数组。 仅当作业停止或流分析移动到下个时间段时，才关闭此数组。 一般而言，最好使用分隔行 JSON，因为在继续写入输出文件时，无需任何特殊处理。|
| 身份验证模式 | 您可以使用[托管标识](stream-analytics-managed-identities-adls.md)或用户令牌授权访问数据湖存储帐户。 授予访问权限后，您可以通过更改用户帐户密码、删除此作业的"数据湖存储"输出或删除流分析作业来撤消访问权限。 |

## <a name="sql-database"></a>SQL 数据库

可以将[Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)用作本质上具有关系性的数据或依赖于在关系数据库中托管的内容的应用程序的输出。 流分析作业写入 SQL 数据库中的现有表。 表架构必须与作业输出中的字段及其类型完全匹配。 还可以通过 SQL 数据库输出选项指定[Azure SQL 数据仓库](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)作为输出。 要了解如何提高写入吞吐量，请参阅使用 Azure [SQL 数据库作为输出文章的流分析](stream-analytics-sql-output-perf.md)。

您还可以使用 Azure [SQL 数据库托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)作为输出。 您必须在[Azure SQL 数据库托管实例中配置公共终结点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)，然后在 Azure 流分析中手动配置以下设置。 手动配置以下设置还支持运行 SQL Server 并附加数据库的 Azure 虚拟机。

下表列出了创建 SQL 数据库输出的属性名称及其说明。

| 属性名称 | 说明 |
| --- | --- |
| 输出别名 |在查询中使用的友好名称，用于将查询输出定向到此数据库。 |
| 数据库 | 将输出发送到的数据库的名称。 |
| 服务器名称 | SQL 数据库服务器名称。 对于 Azure SQL 数据库托管实例，需要指定端口 3342。 例如，sampleserver.public.database.windows.net,3342** |
| 用户名 | 对数据库拥有写入访问权限的用户名。 流分析仅支持 SQL 身份验证。 |
| 密码 | 用于连接到数据库的密码。 |
| 表 | 将写入输出的表名称。 表名称区分大小写。 此表的架构应与字段数量以及作业输出生成的字段类型完全匹配。 |
|继承分区方案| 一个用于继承先前查询步骤的分区方案，以启用具有多个表的写入器的完全并行拓扑的选项。 有关详细信息，请参阅从 [Azure 流分析输出到 Azure SQL 数据库](stream-analytics-sql-output-perf.md)。|
|最大批数| 每个批量插入事务一起发送的推荐记录数上限。|

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Blob 存储和 Azure 数据湖第 2 代

Data Lake Storage Gen2 使 Azure 存储成为在 Azure 上构建企业 Data Lake 的基础。 Data Lake Storage Gen2 从开始设计到服务多个 PB 的信息，同时维持数百兆字节的吞吐量，使您能够轻松管理大量数据。数据存储库 Gen2 的一个基本部分是向 Blob 存储添加分层命名空间。

Azure Blob 存储提供了一种经济高效且可缩放的解决方案，用于在云中存储大量非结构化数据。 有关 Blob 存储及其使用情况的简介，请参阅[使用 Azure 门户上载、下载和列出 Blob。](../storage/blobs/storage-quickstart-blobs-portal.md)

下表列出了用于创建 Blob 或 ADLS Gen2 输出的属性名称及其说明。

| 属性名称       | 说明                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| 输出别名        | 查询中使用的友好名称，用于将查询输出定向到此 blob 存储。 |
| 存储帐户     | 存储帐户的名称（正在向该存储帐户发送输出）。               |
| 存储帐户密钥 | 与存储帐户关联的密钥。                              |
| 存储容器   | 对存储在 Azure Blob 服务中的 blob 进行逻辑分组。 将 blob 上传到 Blob 服务时，必须为该 blob 指定一个容器。 |
| 路径模式 | 可选。 用于写入指定容器中的 blob 的文件路径模式。 <br /><br /> 在路径模式中，可以选择使用数据时间变量的一个或多个实例指定 blob 写入的频率： <br /> {date}、{time} <br /><br />可以使用自定义 blob 分区从事件数据中指定一个自定义 {field} 名称来对 blob 进行分区。 字段名称是字母数字，并且可以包含空格、连字符和下划线。 对自定义字段的限制包括以下内容： <ul><li>字段名称不区分大小写。 例如，服务无法区分列“ID”和列“id”。</li><li>不允许嵌套字段。 在作业查询中改用别名来“平展”字段。</li><li>不能使用表达式作为字段名称。</li></ul> <br />通过此功能可以在路径中使用自定义日期/时间格式说明符配置。 一次只能指定一个自定义日期和时间格式，并用 {datetime:\<specifier>} 关键字括起来。 \<specifier> 允许的输入为 yyyy、MM、M、dd、d、HH、H、mm、m、ss 或 s。 可以在路径中多次使用 {datetime:\<specifier>} 关键字以构成自定义的日期/时间配置。 <br /><br />示例： <ul><li>示例 1：cluster1/logs/{date}/{time}</li><li>示例 2：cluster1/logs/{date}</li><li>示例 3：cluster1/{client_id}/{date}/{time}</li><li>示例 4：群集 1/[日期时间：ss]/[myField]，其中查询为：选择数据.myField 作为 myField 从输入;</li><li>示例 5：cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />创建的文件夹结构的时间戳遵循 UTC 而不是本地时间。<br /><br />文件命名采用以下约定： <br /><br />{路径前缀模式}/schemaHashcode_Guid_Number.extension<br /><br />示例输出文件：<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />有关此功能的详细信息，请参阅 [Azure 流分析自定义 blob 输出分区](stream-analytics-custom-path-patterns-blob-storage-output.md)。 |
| 日期格式 | 可选。 如果在前缀路径中使用日期令牌，可以选择组织文件所采用的日期格式。 示例：YYYY/MM/DD |
| 时间格式 | 可选。 如果在前缀路径中使用时间令牌，可以指定组织文件所采用的时间格式。 目前唯一支持的值是 HH。 |
| 事件序列化格式 | 输出数据的序列化格式。 支持 JSON、CSV、Avro 和 Parquet。 |
|最小行（仅限镶木地板）|每个批次的最小行数。 对于 Parquet，每个批处理都将创建一个新文件。 当前默认值为 2，000 行，允许的最大默认值为 10，000 行。|
|最长时间（仅限镶木地板）|每个批次的最大等待时间。 在此时间之后，批处理将写入输出，即使未满足最小行要求也是如此。 当前默认值为 1 分钟，允许的最大值为 2 小时。 如果 Blob 输出具有路径模式频率，则等待时间不能高于分区时间范围。|
| 编码    | 如果使用 CSV 或 JSON 格式，则必须指定一种编码格式。 目前只支持 UTF-8 这种编码格式。 |
| 分隔符   | 仅适用于 CSV 序列化。 流分析支持大量的常见分隔符以对 CSV 数据进行序列化。 支持的值为逗号、分号、空格、制表符和竖线。 |
| 格式      | 仅适用于 JSON 序列化。 **线分隔**指定通过将每个 JSON 对象由新行分隔来格式化输出。 如果选择 **"行分隔**"，则一次读取一个对象的 JSON。 整个内容本身将不是有效的 JSON。 **数组**指定输出格式化为 JSON 对象的数组。 仅当作业停止或流分析移动到下个时间段时，才关闭此数组。 一般而言，最好使用分隔行 JSON，因为在继续写入输出文件时，无需任何特殊处理。 |

使用 blob 存储作为输出时，在以下情况下 blob 中创建一个新文件：

* 如果文件超出了允许的最大块数（目前为 50,000）。 可达到允许的最大块数，但不能达到允许的最大 blob 大小。 例如，如果输出率很高，则可以看到每个块的字节更多，并且文件大小会更大。 输出率较低时，每个块都有较少的数据，且文件大小较小。
* 如果输出中出现架构更改，输出格式也需要固定的架构（CSV 和 Avro）。
* 如果作业重新启动，可选择在外部由用户停止或启动，或在内部进行系统维护或错误恢复。
* 如果对查询进行完全分区，会为每个输出分区创建新文件。
* 如果用户删除存储帐户的文件或容器。
* 如果使用路径前缀模式对输出进行了时间分区，当查询移动到下一个小时后，会使用新的 blob。
* 如果按自定义字段对输出进行分区，则每个分区键都会创建新的 blob（如果不存在）。
* 如果按照自定义字段对输出进行分区（其中分区键基数超过 8000），则可能每个分区键创建一个新的 blob。

## <a name="event-hubs"></a>事件中心

[Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)服务是具有高扩展性的发布 - 订阅事件引入器。 事件中心每秒可收集数百万个事件。 当流分析作业的输出成为另一个流作业的输入时，使用事件中心作为输出。 有关最大消息大小和批次大小优化的信息，请参阅[输出批次大小](#output-batch-size)部分。

需要使用几个参数才能将事件中心内的数据流配置为输出。

| 属性名称 | 说明 |
| --- | --- |
| 输出别名 | 查询中使用的易记名称，用于将查询输出定向到此事件中心。 |
| 事件中心命名空间 | 一组消息处理实体的容器。 创建新的事件中心后，还创建了事件中心命名空间。 |
| 事件中心名称 | 事件中心输出的名称。 |
| 事件中心策略名称 | 共享访问策略，您可以在事件中心的 **"配置"** 选项卡上创建。每个共享访问策略都有一个名称、您设置的权限和访问密钥。 |
| 事件中心策略密钥 | 用于对事件中心命名空间的访问权限进行身份验证的共享访问密钥。 |
| 分区键列 | 可选。 包含事件中心输出的分区键的列。 |
| 事件序列化格式 | 输出数据的序列化格式。 支持 JSON、CSV 和 Avro。 |
| 编码 | 对于 CSV 和 JSON，目前只支持 UTF-8 这种编码格式。 |
| 分隔符 | 仅适用于 CSV 序列化。 流分析支持大量的常见分隔符以对 CSV 格式的数据进行序列化。 支持的值为逗号、分号、空格、制表符和竖线。 |
| 格式 | 仅适用于 JSON 序列化。 **线分隔**指定通过将每个 JSON 对象由新行分隔来格式化输出。 如果选择 **"行分隔**"，则一次读取一个对象的 JSON。 整个内容本身将不是有效的 JSON。 **数组**指定输出格式化为 JSON 对象的数组。  |
| 属性列 | 可选。 需要作为传出消息的用户属性而不是有效负载附加的逗号分隔列。 [输出的自定义元数据属性](#custom-metadata-properties-for-output)部分详细介绍了此功能。 |

## <a name="power-bi"></a>Power BI

您可以使用[Power BI](https://powerbi.microsoft.com/)作为流分析作业的输出，以提供丰富的分析结果可视化体验。 您可以将此功能用于操作仪表板、报表生成和指标驱动报告。

流分析的 Power BI 输出当前不在 Azure 中国 21Vianet 和 Azure 德国（T-Systems 国际）区域提供。

下表列出了属性名称及其说明，以配置 Power BI 输出。

| 属性名称 | 说明 |
| --- | --- |
| 输出别名 |提供用于查询的友好名称，将查询输出定向到此 Power BI 输出。 |
| 组工作区 |要启用与其他 Power BI 用户共享数据，可以在 Power BI 帐户中选择组，或者如果您不想写入组，请选择 **"我的工作区**"。 更新现有组需要对 Power BI 重新进行身份验证。 |
| 数据集名称 |提供您希望 Power BI 输出使用的数据集名称。 |
| 表名称 |在 Power BI 输出的数据集下提供表名称。 目前，流分析作业的 Power BI 输出在 1 个数据集中只能有 1 个表。 |
| 授权连接 | 您需要使用 Power BI 授权来配置输出设置。 将此输出访问权限授予 Power BI 仪表板后，可以通过更改用户帐户密码、删除作业输出或删除流分析作业来撤消访问权限。 | 

有关配置 Power BI 输出和仪表板的演练，请参阅[Azure 流分析和 Power BI](stream-analytics-power-bi-dashboard.md)教程。

> [!NOTE]
> 不要在 Power BI 仪表板中显式创建数据集和表。 当作业启动并且作业开始将输出泵入 Power BI 时，数据集和表将自动填充。 如果作业查询未生成任何结果，则不会创建数据集和表。 如果 Power BI 已具有与此流分析作业中提供的数据集和表同名，则现有数据将被覆盖。
>

### <a name="create-a-schema"></a>创建架构
Azure 流分析为用户创建 Power BI 数据集和表架构（如果用户不存在）。 在所有其他情况下，表可以更新使用新值。 目前，数据集中只能存在一个表。 

Power BI 使用先出 （FIFO） 保留策略。 数据将在表中收集，直到达到 200，000 行。

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>将数据类型从流分析转换为 Power BI
如果输出架构更改，Azure 流分析会在运行时动态更新数据模型。 列名称更改、列类型更改，以及添加或删除列，这些都会进行跟踪。

此表介绍数据类型转换，从[流分析数据类型](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)转换为 Power BI[实体数据模型 （EDM） 类型](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)（如果 Power BI 数据集和表不存在）。

从流分析 | 到 Power BI
-----|-----
bigint | Int64
nvarchar(max) | 字符串
datetime | Datetime
FLOAT | Double
记录数组 | 字符串类型、常量值"IRecord"或"IArray"

### <a name="update-the-schema"></a>更新架构
流分析根据输出中的第一组事件推断数据模型架构。 稍后，如有必要，将更新数据模型架构，以适应可能不适合原始架构的传入事件。

避免查询`SELECT *`，以防止跨行的动态架构更新。 除了潜在的业绩影响外，它还可能导致结果所需要时间的不确定性。 选择需要在 Power BI 仪表板上显示的确切字段。 此外，数据值应与所选数据类型相符。


上一个/当前 | Int64 | 字符串 | Datetime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | 字符串 | 字符串 | Double
Double | Double | 字符串 | 字符串 | Double
字符串 | 字符串 | 字符串 | 字符串 | 字符串 
Datetime | 字符串 | 字符串 |  Datetime | 字符串

## <a name="table-storage"></a>表存储

[Azure 表存储](../storage/common/storage-introduction.md)提供高可用性、大规模可扩展的存储，以便应用程序可以自动扩展以满足用户需求。 表存储是 Microsoft 的 NoSQL 密钥/属性存储，可用于架构上约束较少的结构化数据。 Azure 表存储可用于持久地存储数据，方便进行高效的检索。

下表列出了用于创建表输出的属性名称及其说明。

| 属性名称 | 说明 |
| --- | --- |
| 输出别名 |该名称是在查询中使用的友好名称，用于将查询输出定向到此表存储。 |
| 存储帐户 |存储帐户的名称（正在向该存储帐户发送输出）。 |
| 存储帐户密钥 |与存储帐户关联的访问密钥。 |
| 表名称 |表的名称。 如果表不存在，则创建该表。 |
| 分区键 |包含分区键的输出列的名称。 分区键是某个表中分区的唯一标识符，分区键构成了实体主键的第一部分。 它是最大可为 1 KB 的字符串值。 |
| 行键 |包含行键的输出列的名称。 行键是某个分区中实体的唯一标识符。 行键构成了实体主键的第二部分。 行键是最大可为 1 KB 的字符串值。 |
| 批大小 |批处理操作的记录数。 默认值 (100) 对大部分作业来说都已足够。 有关修改设置的详细信息，请参阅[表批处理操作规范](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.tablebatchoperation)。 |

## <a name="service-bus-queues"></a>服务总线队列

[服务总线队列](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)为一个或多个竞争使用方提供 FIFO 消息传递方式。 通常，接收方按照消息添加到队列中的临时顺序来接收并处理消息。 每条消息仅由一个消息使用方接收并处理。

在[兼容性级别 1.2](stream-analytics-compatibility-level.md)中，Azure 流分析使用[高级消息队列协议 （AMQP）](../service-bus-messaging/service-bus-amqp-overview.md)消息传递协议写入服务总线队列和主题。 AMQP 让你能够使用开放标准协议构建跨平台的混合应用程序。

下表列出了用于创建队列输出的属性名称及其说明。

| 属性名称 | 说明 |
| --- | --- |
| 输出别名 |该名称是在查询中使用的易记名称，用于将查询输出定向到此服务总线队列。 |
| 服务总线命名空间 |一组消息处理实体的容器。 |
| 队列名称 |服务总线队列的名称。 |
| 队列策略名称 |创建队列时，还可以在队列的 **"配置"** 选项卡上创建共享访问策略。每个共享访问策略都有一个名称、您设置的权限和访问密钥。 |
| 队列策略密钥 |用于对服务总线命名空间的访问权限进行身份验证的共享访问密钥。 |
| 事件序列化格式 |输出数据的序列化格式。 支持 JSON、CSV 和 Avro。 |
| 编码 |对于 CSV 和 JSON，目前只支持 UTF-8 这种编码格式。 |
| 分隔符 |仅适用于 CSV 序列化。 流分析支持大量的常见分隔符以对 CSV 格式的数据进行序列化。 支持的值为逗号、分号、空格、制表符和竖线。 |
| 格式 |仅适用于 JSON 类型。 **线分隔**指定通过将每个 JSON 对象由新行分隔来格式化输出。 如果选择 **"行分隔**"，则一次读取一个对象的 JSON。 整个内容本身将不是有效的 JSON。 **数组**指定输出格式化为 JSON 对象的数组。 |
| 属性列 | 可选。 需要作为传出消息的用户属性而不是有效负载附加的逗号分隔列。 [输出的自定义元数据属性](#custom-metadata-properties-for-output)部分详细介绍了此功能。 |
| 系统属性列 | 可选。 需要附加到传出消息而不是附加到有效负载的系统属性和相应列名的键值对。 [服务总线队列和主题输出的系统属性](#system-properties-for-service-bus-queue-and-topic-outputs)部分详细介绍了此功能。  |

分区数[基于服务总线 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分区键是每个分区的唯一整数值。

## <a name="service-bus-topics"></a>服务总线主题
服务总线队列提供从发送方到接收方的一对一通信方法。 [服务总线主题](https://msdn.microsoft.com/library/azure/hh367516.aspx)提供一对多形式的通信。

下表列出了用于创建服务总线主题输出的属性名称及其说明。

| 属性名称 | 说明 |
| --- | --- |
| 输出别名 |该名称是在查询中使用的易记名称，用于将查询输出定向到此服务总线主题。 |
| 服务总线命名空间 |一组消息处理实体的容器。 创建新的事件中心后，还创建了 Service Bus 命名空间。 |
| 主题名称 |主题是消息传送实体，类似于事件中心和队列。 设计用于从设备和服务收集事件流。 在创建主题时，还会为其提供特定的名称。 发送到主题的消息在创建订阅后才会提供给用户，因此请确保主题下存在一个或多个订阅 |
| 主题策略名称 |创建服务总线主题时，还可以在主题的 **"配置"** 选项卡上创建共享访问策略。每个共享访问策略都有一个名称、您设置的权限和访问密钥。 |
| 主题策略密钥 |用于对服务总线命名空间的访问权限进行身份验证的共享访问密钥。 |
| 事件序列化格式 |输出数据的序列化格式。 支持 JSON、CSV 和 Avro。 |
| 编码 |如果使用 CSV 或 JSON 格式，则必须指定一种编码格式。 目前只支持 UTF-8 这种编码格式。 |
| 分隔符 |仅适用于 CSV 序列化。 流分析支持大量的常见分隔符以对 CSV 格式的数据进行序列化。 支持的值为逗号、分号、空格、制表符和竖线。 |
| 属性列 | 可选。 需要作为传出消息的用户属性而不是有效负载附加的逗号分隔列。 [输出的自定义元数据属性](#custom-metadata-properties-for-output)部分详细介绍了此功能。 |
| 系统属性列 | 可选。 需要附加到传出消息而不是附加到有效负载的系统属性和相应列名的键值对。 [服务总线队列和主题输出的系统属性](#system-properties-for-service-bus-queue-and-topic-outputs)部分详细介绍了此功能。 |

分区数[基于服务总线 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分区键是每个分区的唯一整数值。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/)是一种全球分布的数据库服务，在全球范围内提供无限的弹性扩展、丰富的查询和跨架构无关数据模型的自动索引。 要了解用于流分析的 Azure Cosmos DB 容器选项，请参阅[使用 Azure Cosmos DB 的流分析作为输出](stream-analytics-documentdb-output.md)文章。

来自流分析的 Azure Cosmos DB 输出当前不在 Azure 中国 21Vianet 和 Azure 德国（T-Systems 国际）区域中提供。

> [!Note]
> 目前，Azure 流分析仅支持使用 SQL API 连接到 Azure Cosmos DB。
> 尚不支持使用其他 Azure Cosmos DB API。 如果使用其他 API 将 Azure 流分析指向 创建的 Azure Cosmos DB 帐户，则可能无法正确存储数据。

下表描述了用于创建 Azure Cosmos DB 输出的属性。

| 属性名称 | 说明 |
| --- | --- |
| 输出别名 | 用于在流分析查询中引用此输出的别名。 |
| 接收器 | Azure Cosmos DB。 |
| 导入选项 | 选择“从订阅中选择 Cosmos DB”或“手动提供 Cosmos DB 设置”********。
| 帐户 ID | Azure Cosmos DB 帐户的名称或终结点 URI。 |
| 帐户密钥 | Azure Cosmos DB 帐户的共享访问密钥。 |
| 数据库 | Azure Cosmos DB 数据库名称。 |
| 容器名称 | 要使用的容器名称，该名称必须在 Cosmos DB 中存在。 示例：  <br /><ul><li> _我的容器_：必须存在名为"我的容器"的容器。</li>|
| 文档 ID |可选。 输出事件中的字段的名称，该字段用于指定插入或更新操作所基于的主键。

## <a name="azure-functions"></a>Azure Functions
Azure 函数是一种无服务器计算服务，可用于按需运行代码，而无需显式预配或管理基础结构。 它允许您实现由 Azure 或合作伙伴服务中发生的事件触发的代码。 Azure 函数响应触发器的这种功能使其成为 Azure 流分析的自然输出。 此输出适配器使用户能够将流分析连接到 Azure 函数，并运行脚本或代码段以响应各种事件。

流分析的 Azure 函数输出当前不在 Azure 中国 21Vianet 和 Azure 德国（T-Systems 国际）区域中提供。

Azure 流分析通过 HTTP 触发器调用 Azure Functions。 提供具有以下可配置属性的 Azure Functions 输出适配器：

| 属性名称 | 说明 |
| --- | --- |
| 函数应用 |Azure Functions 应用的名称。 |
| 函数 |Azure Functions 应用中的函数的名称。 |
| 键 |若要使用其他订阅中的 Azure 函数，可提供用于访问该函数的密钥。 |
| 最大批大小 |此属性可用于设置将发送到 Azure 函数的每个输出批的最大大小。 输入单元以字节为单位。 默认情况下，此值为 262,144 字节 (256 KB)。 |
| 最大批数  |一个用于指定发送到 Azure Functions 的每个批中的最大事件数的属性。 默认值为 100。 |

Azure 流分析期望从函数应用中针对成功处理的批次提供 HTTP 状态 200。

当 Azure 流分析从 Azure 函数收到 413（“http 请求实体过大”）异常时，它将减小发送到 Azure Functions 的批的大小。 在 Azure 函数代码中，使用此异常以确保 Azure 流分析不会发送过大的批。 另请确保函数中使用的最大批次数和最大批次大小值与在流分析门户中输入的值一致。

> [!NOTE]
> 在测试连接过程中，流分析会将空批发送到 Azure Functions，以测试两者之间的连接是否正常。 确保 Functions 应用处理空批请求，以确保通过连接测试。

另外，如果时间窗口中没有任何事件登录，则不生成任何输出。 因此，不会调用 **computeResult** 函数。 此行为与内置窗口化聚合函数一致。

## <a name="custom-metadata-properties-for-output"></a>输出的自定义元数据属性 

可将查询列作为用户属性附加到传出的消息。 这些列不会进入有效负载。 这些属性以字典形式在输出消息中提供。 键是列名，值是属性字典中的列值。**** 支持除“记录”和“数组”以外的其他所有流分析数据类型。  

支持的输出： 
* 服务总线队列 
* 服务总线主题 
* 事件中心 

以下示例将 `DeviceId` 和 `DeviceStatus` 这两个字段添加到了元数据。 
* 查询：`select *, DeviceId, DeviceStatus from iotHubInput`
* 输出配置：`DeviceId,DeviceStatus`

![属性列](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

以下屏幕截图显示了在事件中心通过[服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer)检查的输出消息属性。

![事件自定义属性](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>服务总线队列和主题输出的系统属性  
可将查询列作为[系统属性](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties)附加到传出的服务总线队列或主题消息。 这些列不会进入有效负载，而是将查询列值填充到相应的 BrokeredMessage [系统属性](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties)中。
支持这些系统属性 - `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`。
这些列的字符串值将分析成相应的系统属性值类型，任何分析失败将被视为数据错误。
此字段以 JSON 对象格式提供。 有关此格式的详细信息如下：
* 用大括号 {} 括住。
* 以键值对的形式编写。
* 键和值必须是字符串。
* 键是系统属性名称，值是查询列名。
* 键和值以冒号分隔。
* 每个键值对以逗号分隔。

这显示了如何使用此属性 |

* 查询：`select *, column1, column2 INTO queueOutput FROM iotHubInput`
* 系统属性列：`{ "MessageId": "column1", "PartitionKey": "column2"}`

这会在服务总线队列消息的 `MessageId` 中设置 `column1` 的值，并在 PartitionKey 中设置 `column2` 的值。

## <a name="partitioning"></a>分区

下表汇总了分区支持，以及每个输出类型的输出写入器数目：

| 输出类型 | 分区支持 | 分区键  | 输出写入器数目 |
| --- | --- | --- | --- |
| Azure Data Lake Store | 是 | 在路径前缀模式中使用 {date} 和 {time} 标记。 选择日期格式，例如 YYYY/MM/DD、DD/MM/YYYY 或 MM-DD-YYYY。 HH 用于时间格式。 | 按照[完全可并行化的查询](stream-analytics-scale-jobs.md)的输入分区。 |
| Azure SQL Database | 是，但需要启用。 | 基于查询中的 PARTITION BY 子句。 | 如果已启用“继承分区”选项，请遵循[完全可并行化的查询](stream-analytics-scale-jobs.md)的输入分区。 若要详细了解在将数据载入 Azure SQL 数据库时如何提高写入吞吐量性能，请参阅[从 Azure 流分析输出到 Azure SQL 数据库](stream-analytics-sql-output-perf.md)。 |
| Azure Blob 存储 | 是 | 在路径模式中使用事件字段中的 {date} 和 {time} 标记。 选择日期格式，例如 YYYY/MM/DD、DD/MM/YYYY 或 MM-DD-YYYY。 HH 用于时间格式。 可以通过单个自定义事件属性 {fieldname} 或 {datetime:\<specifier>} 对 blob 输出进行分区。 | 按照[完全可并行化的查询](stream-analytics-scale-jobs.md)的输入分区。 |
| Azure 事件中心 | 是 | 是 | 按分区对齐方式变化。<br /> 如果事件中心输出的分区键与上游（上一个）查询步骤不相符，写入器的数量与事件中心输出中的分区数量相同。 每个写入器使用 [EventHubSender 类](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet)将事件发送到特定分区。 <br /> 如果事件中心输出的分区键与上游（上一个）查询步骤不相符，写入器的数量与该上一步骤中的分区数量相同。 每个写入器使用 **EventHubClient** 中的 [SendBatchAsync 类](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet)将事件发送到所有输出分区。 |
| Power BI | 否 | 无 | 不适用。 |
| Azure 表存储 | 是 | 任何输出列。  | 按照[完全并行化的查询](stream-analytics-scale-jobs.md)的输入分区。 |
| Azure 服务总线主题 | 是 | 自动选择。 分区数基于[服务总线 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分区键是每个分区的唯一整数值。| 与输出主题中的分区数量相同。  |
| Azure 服务总线队列 | 是 | 自动选择。 分区数基于[服务总线 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分区键是每个分区的唯一整数值。| 与输出队列中的分区数量相同。 |
| Azure Cosmos DB | 是 | 基于查询中的 PARTITION BY 子句。 | 按照[完全并行化的查询](stream-analytics-scale-jobs.md)的输入分区。 |
| Azure Functions | 是 | 基于查询中的 PARTITION BY 子句。 | 按照[完全并行化的查询](stream-analytics-scale-jobs.md)的输入分区。 |

还可以在查询中使用 `INTO <partition count>`子句（请参阅 [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)）来控制输出写入器的数量，这可能有助于实现所需的作业拓扑。 如果输出适配器未分区，则一个输入分区中缺少数据将导致延迟最多可达延迟到达的时间量。 在这种情况下，输出将合并到单个写入器，这可能会导致管道中出现瓶颈。 若要了解有关延迟到达策略的详细信息，请参阅 [Azure 流分析事件顺序注意事项](stream-analytics-out-of-order-and-late-events.md)。

## <a name="output-batch-size"></a>输出批大小
Azure 流分析使用大小可变的批来处理事件和写入到输出。 通常流分析引擎不会一次写入一条消息，而是使用批来提高效率。 当传入和传出事件的速率较高时，流分析将使用更大的批。 输出速率低时，使用较小的批来保证低延迟。

下表阐述了输出批处理的一些注意事项：

| 输出类型 |    最大消息大小 | 批大小优化 |
| :--- | :--- | :--- |
| Azure Data Lake Store | 请参阅[数据湖存储限制](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits)。 | 每个写入操作最高 4 MB。 |
| Azure SQL Database | 可使用最大批计数进行配置。 默认情况下，单个批量插入操作最多可插入 10,000 行，最少可插入 100 行。<br />请参阅 [Azure SQL 限制](../sql-database/sql-database-resource-limits.md)。 |  每个批最初是按照最大批计数批量插入的。 根据 SQL 的可重试错误对半拆分批（直到达到最小批计数）。 |
| Azure Blob 存储 | 请参阅[Azure 存储限制](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)。 | 最大 Blob 块大小为 4 MB。<br />最大 Blob 块计数为 50,000。 |
| Azure 事件中心    | 每条消息 256 KB 或 1 MB。 <br />请参阅[事件中心限制](../event-hubs/event-hubs-quotas.md)。 |    如果输入/输出分区未对齐，则每个事件将单独打包成不超过最大消息大小的 `EventData`，并在批中发送。 如果使用[自定义元数据属性](#custom-metadata-properties-for-output)，也会发生这种情况。 <br /><br />  如果输入/输出分区已对齐，则多个事件将打包成不超过最大消息大小的单个 `EventData` 实例，然后发送。    |
| Power BI | 请参阅[Power BI 休息 API 限制](https://msdn.microsoft.com/library/dn950053.aspx)。 |
| Azure 表存储 | 请参阅[Azure 存储限制](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)。 | 默认为单个事务 100 个实体。 可根据需要将其配置为更小的值。 |
| Azure 服务总线队列    | 在标准层中为每条消息 256 KB，在高级层中为 1MB。<br /> 请参阅[服务总线限制](../service-bus-messaging/service-bus-quotas.md)。 | 对每条消息使用单个事件。 |
| Azure 服务总线主题 | 在标准层中为每条消息 256 KB，在高级层中为 1MB。<br /> 请参阅[服务总线限制](../service-bus-messaging/service-bus-quotas.md)。 | 对每条消息使用单个事件。 |
| Azure Cosmos DB    | 请参阅[Azure 宇宙数据库限制](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits)。 | 批大小和写入频率根据 Azure Cosmos DB 响应动态调整。 <br /> 流分析不会施加预先确定的限制。 |
| Azure Functions    | | 默认批大小为 262,144 字节 (256 KB)。 <br /> 每批的默认事件计数为 100。 <br /> 批大小是可配置的，可在流分析[输出选项](#azure-functions)中增加或减少。

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
