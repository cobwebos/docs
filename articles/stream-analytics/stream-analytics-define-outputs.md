---
title: 了解 Azure 流分析的输出
description: 本文介绍 Azure 流分析提供的数据输出选项，包括用于分析结果的 Power BI。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/31/2019
ms.openlocfilehash: 3b242ff8ee3e635493cd501cf37ffc7c78a57d91
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563316"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>了解 Azure 流分析的输出

本文介绍可用于 Azure 流分析作业的输出类型。 输出可帮助存储和保存流分析作业的结果。 通过使用输出数据, 您可以对数据进行进一步的业务分析和数据仓库。

设计流分析查询时, 请使用[INTO 子句](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics)引用输出的名称。 可以通过在查询中提供多个 INTO 子句来使用每个作业的单个输出或每个流式处理作业的多个输出 (如果需要)。

若要创建、编辑和测试流分析作业输出, 可以使用[Azure 门户](stream-analytics-quick-create-portal.md#configure-job-output)、 [AZURE POWERSHELL](stream-analytics-quick-create-powershell.md#configure-output-to-the-job)、 [.Net API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet)、 [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)和[Visual Studio](stream-analytics-quick-create-vs.md)。

某些输出类型支持[分区](#partitioning)。 [输出批大小](#output-batch-size)会有所不同以优化吞吐量。


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

流分析支持[Azure Data Lake Storage 第1代](../data-lake-store/data-lake-store-overview.md)。 Azure Data Lake Storage 是适用于大数据分析工作负荷的企业级超大规模存储库。 您可以使用 Data Lake Storage 存储任何大小、类型和引入速度的数据, 以便进行操作和探索分析。 流分析需要经过授权才能访问 Data Lake Storage。

流分析 Azure Data Lake Storage 输出当前在 Azure 中国世纪互联和 Azure 德国 (T 系统国际) 区域中不可用。

下表列出了用于配置 Data Lake Storage 第1代输出的属性名称及其说明。   

| 属性名 | 说明 |
| --- | --- |
| 输出别名 | 在查询中使用的友好名称, 用于将查询输出定向到 Data Lake Store。 |
| 订阅 | 包含 Azure Data Lake Storage 帐户的订阅。 |
| 帐户名 | 要向其发送输出的 Data Lake Store 帐户的名称。 你会看到一个下拉列表, 其中列出了你的订阅中可用的 Data Lake Store 帐户。 |
| 路径前缀模式 | 用于在指定的 Data Lake Store 帐户中写入文件的文件路径。 可以指定 {date} 和 {time} 变量的一个或多个实例:<br /><ul><li>示例 1：folder1/logs/{date}/{time}</li><li>示例 2：folder1/logs/{date}</li></ul><br />创建的文件夹结构的时间戳遵循 UTC 而不是本地时间。<br /><br />如果文件路径模式不包含尾部反斜杠 (/), 则文件路径中的最后一个模式将被视为文件名前缀。 <br /><br />在这些情况下会创建新文件：<ul><li>在输出架构中进行更改</li><li>作业的外部或内部重启</li></ul> |
| 日期格式 | 可选。 如果在前缀路径中使用日期令牌，可以选择组织文件所采用的日期格式。 例如：YYYY/MM/DD |
|时间格式 | 可选。 如果在前缀路径中使用时间令牌，可以指定组织文件所采用的时间格式。 目前唯一支持的值是 HH。 |
| 事件序列化格式 | 输出数据的序列化格式。 支持 JSON、CSV 和 Avro。|
| 编码 | 如果使用 CSV 或 JSON 格式，则必须指定一种编码格式。 目前，UTF-8 是仅受支持的编码格式。|
| 分隔符 | 仅适用于 CSV 序列化。 流分析支持大量的常见分隔符以对 CSV 数据进行序列化。 支持的值为逗号、分号、空格、制表符和竖线。|
| 格式 | 仅适用于 JSON 序列化。 “行分隔”指定通过新行分隔各个 JSON 对象，从而格式化输出。 “数组”指定输出会被格式化为 JSON 对象的数组。 仅当作业停止或流分析移动到下个时间段时，才关闭此数组。 一般而言，最好使用分隔行 JSON，因为在继续写入输出文件时，无需任何特殊处理。|
| 身份验证模式 | 你可以使用[托管标识](stream-analytics-managed-identities-adls.md)或用户令牌授权访问你的 Data Lake Storage 帐户。 授予访问权限后, 你可以通过更改用户帐户密码、删除此作业 Data Lake Storage 输出或删除流分析作业, 来撤销访问权限。 |

## <a name="sql-database"></a>SQL 数据库

可以使用[AZURE SQL 数据库](https://azure.microsoft.com/services/sql-database/)作为本质上的关系或依赖于在关系数据库中托管的内容的应用程序的输出。 流分析作业将写入 SQL 数据库中的现有表。 表架构必须与作业输出中的字段及其类型完全匹配。 还可以通过 SQL 数据库输出选项指定[AZURE SQL 数据仓库](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)作为输出。 若要了解提高写入吞吐量的方法, 请参阅将[AZURE SQL 数据库作为输出的流分析](stream-analytics-sql-output-perf.md)一文。

下表列出了用于创建 SQL 数据库输出的属性名称及其说明。

| 属性名 | 说明 |
| --- | --- |
| 输出别名 |在查询中使用的友好名称，用于将查询输出定向到此数据库。 |
| 数据库 | 要将输出发送到的数据库的名称。 |
| 服务器名称 | SQL 数据库服务器名称。 |
| 用户名 | 对数据库具有写入访问权限的用户名。 流分析仅支持 SQL 身份验证。 |
| 密码 | 用于连接到数据库的密码。 |
| 表 | 将写入输出的表名称。 表名称区分大小写。 此表的架构应与作业输出生成的字段数量及其类型完全匹配。 |
|继承分区方案| 用于继承上一个查询步骤的分区方案的选项, 用于对表启用具有多个编写器的完全并行拓扑。 有关详细信息，请参阅从 [Azure 流分析输出到 Azure SQL 数据库](stream-analytics-sql-output-perf.md)。|
|最大批处理计数| 建议的每个大容量插入事务发送的记录数的上限。|

> [!NOTE]
> 流分析中的作业输出支持 Azure SQL 数据库产品/服务, 但尚不支持运行 SQL Server 的 Azure 虚拟机 (附带附加的数据库或 SQL Azure 托管实例)。 这在将来的版本中可能会有所改变。

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Blob 存储和 Azure Data Lake Gen2

在全球范围内, 提供 Azure Data Lake Gen2 的出口作为预览功能提供。 你可以通过在我们的[请求窗体](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u)中提供其他详细信息来请求对预览版的访问权限。

Azure Blob 存储提供了一种经济高效且可缩放的解决方案，用于在云中存储大量非结构化数据。 有关 Blob 存储及其用法的简介, 请参阅[使用 Azure 门户上传、下载和列出 blob](../storage/blobs/storage-quickstart-blobs-portal.md)。

下表列出了用于创建 Blob 输出的属性名称及其说明。

| 属性名       | 说明                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| 输出别名        | 查询中使用的友好名称，用于将查询输出定向到此 blob 存储。 |
| 存储帐户     | 存储帐户的名称（正在向该存储帐户发送输出）。               |
| 存储帐户密钥 | 与存储帐户关联的密钥。                              |
| 存储容器   | 对存储在 Azure Blob 服务中的 blob 进行逻辑分组。 将 blob 上传到 Blob 服务时，必须为该 blob 指定一个容器。 |
| 路径模式 | 可选。 用于写入指定容器中的 blob 的文件路径模式。 <br /><br /> 在路径模式中，可以选择使用数据时间变量的一个或多个实例指定 blob 写入的频率： <br /> {date}、{time} <br /><br />可以使用自定义 blob 分区从事件数据中指定一个自定义 {field} 名称来对 blob 进行分区。 字段名称是字母数字，并且可以包含空格、连字符和下划线。 对自定义字段的限制包括以下内容： <ul><li>字段名称不区分大小写。 例如，服务无法区分列“ID”和列“id”。</li><li>不允许嵌套字段。 在作业查询中改用别名来“平展”字段。</li><li>不能使用表达式作为字段名称。</li></ul> <br />通过此功能可以在路径中使用自定义日期/时间格式说明符配置。 一次只能指定一个自定义日期和时间格式，并用 {datetime:\<specifier>} 关键字括起来。 \<specifier> 允许的输入为 yyyy、MM、M、dd、d、HH、H、mm、m、ss 或 s。 可以在路径中多次使用 {datetime:\<specifier>} 关键字以构成自定义的日期/时间配置。 <br /><br />例如： <ul><li>示例 1：cluster1/logs/{date}/{time}</li><li>示例 2：cluster1/logs/{date}</li><li>示例 3：cluster1/{client_id}/{date}/{time}</li><li>示例 4：cluster1/{datetime:ss}/{myField}，其中查询是：SELECT data.myField AS myField FROM Input;</li><li>示例 5：cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />创建的文件夹结构的时间戳遵循 UTC 而不是本地时间。<br /><br />文件命名采用以下约定： <br /><br />{路径前缀模式}/schemaHashcode_Guid_Number.extension<br /><br />示例输出文件：<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />有关此功能的详细信息，请参阅 [Azure 流分析自定义 blob 输出分区](stream-analytics-custom-path-patterns-blob-storage-output.md)。 |
| 日期格式 | 可选。 如果在前缀路径中使用日期令牌，可以选择组织文件所采用的日期格式。 例如：YYYY/MM/DD |
| 时间格式 | 可选。 如果在前缀路径中使用时间令牌，可以指定组织文件所采用的时间格式。 目前唯一支持的值是 HH。 |
| 事件序列化格式 | 输出数据的序列化格式。 支持 JSON、CSV、Avro 和 Parquet。 |
|最小行数 (仅 Parquet)|每批的最小行数。 对于 Parquet, 每个批处理都将创建一个新文件。 当前默认值为2000行, 允许的最大值为10000行。|
|最长时间 (仅 Parquet)|每批的最长等待时间。 在此之后, 即使未满足最小行要求, 也会将该批写入输出。 当前默认值为1分钟, 允许的最大值为2小时。 如果 blob 输出具有路径模式频率, 则等待时间不能大于分区时间范围。|
| 编码    | 如果使用 CSV 或 JSON 格式，则必须指定一种编码格式。 目前，UTF-8 是仅受支持的编码格式。 |
| 分隔符   | 仅适用于 CSV 序列化。 流分析支持大量的常见分隔符以对 CSV 数据进行序列化。 支持的值为逗号、分号、空格、制表符和竖线。 |
| 格式      | 仅适用于 JSON 序列化。 “行分隔”指定通过新行分隔各个 JSON 对象，从而格式化输出。 “数组”指定输出会被格式化为 JSON 对象的数组。 仅当作业停止或流分析移动到下个时间段时，才关闭此数组。 一般而言，最好使用分隔行 JSON，因为在继续写入输出文件时，无需任何特殊处理。 |

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

[Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)服务是具有高扩展性的发布 - 订阅事件引入器。 事件中心每秒可收集数百万个事件。 当流分析作业的输出成为另一个流式处理作业的输入时, 将事件中心用作输出。

需要使用几个参数才能将事件中心内的数据流配置为输出。

| 属性名 | 说明 |
| --- | --- |
| 输出别名 | 查询中使用的易记名称，用于将查询输出定向到此事件中心。 |
| 事件中心命名空间 | 一组消息处理实体的容器。 创建新的事件中心后，还创建了事件中心命名空间。 |
| 事件中心名称 | 事件中心输出的名称。 |
| 事件中心策略名称 | 共享访问策略，可以在事件中心的“配置”选项卡上创建。每个共享访问策略具有名称、所设权限以及访问密钥。 |
| 事件中心策略密钥 | 用于对事件中心命名空间的访问权限进行身份验证的共享访问密钥。 |
| 分区键列 | 可选。 包含事件中心输出的分区键的列。 |
| 事件序列化格式 | 输出数据的序列化格式。 支持 JSON、CSV 和 Avro。 |
| 编码 | 对于 CSV 和 JSON，目前只支持 UTF-8 这种编码格式。 |
| 分隔符 | 仅适用于 CSV 序列化。 流分析支持一些常用分隔符，以便采用 CSV 格式序列化数据。 支持的值为逗号、分号、空格、制表符和竖线。 |
| 格式 | 仅适用于 JSON 序列化。 “行分隔”指定通过新行分隔各个 JSON 对象，从而格式化输出。 “数组”指定输出会被格式化为 JSON 对象的数组。 仅当作业停止或流分析移动到下个时间段时，才关闭此数组。 一般而言，最好使用分隔行 JSON，因为在继续写入输出文件时，无需任何特殊处理。 |
| 属性列 | 可选。 需要作为传出消息的用户属性而不是有效负载附加的逗号分隔列。 [输出的自定义元数据属性](#custom-metadata-properties-for-output)部分详细介绍了此功能。 |

## <a name="power-bi"></a>Power BI

可以将[Power BI](https://powerbi.microsoft.com/)用作流分析作业的输出, 以便为分析结果提供丰富的可视化体验。 此功能可用于操作仪表板、报表生成和指标驱动型报告。

流分析 Power BI 输出当前在 Azure 中国世纪互联和 Azure 德国 (T 系统国际) 区域中不可用。

下表列出了用于配置 Power BI 输出的属性名称及其说明。

| 属性名 | 说明 |
| --- | --- |
| 输出别名 |提供一个友好名称, 该名称在查询中用于将查询输出定向到此 Power BI 输出。 |
| 组工作区 |若要启用与其他 Power BI 用户共享数据, 可在 Power BI 帐户中选择组, 如果不想写入组, 请选择 **"我的工作区"** 。 更新现有组需要对 Power BI 重新进行身份验证。 |
| 数据集名称 |提供要 Power BI 输出使用的数据集名称。 |
| 表单名称 |在 Power BI 输出的数据集下提供表名称。 目前，流分析作业的 Power BI 输出在 1 个数据集中只能有 1 个表。 |
| 授权连接 | 需要 Power BI 授权才能配置输出设置。 向 Power BI 仪表板授予此输出访问权限后, 你可以通过更改用户帐户密码、删除作业输出或删除流分析作业来撤销访问权限。 | 

有关配置 Power BI 输出和仪表板的演练, 请参阅[Azure 流分析和 Power BI](stream-analytics-power-bi-dashboard.md)教程。

> [!NOTE]
> 请勿在 Power BI 仪表板中显式创建数据集和表。 启动作业时, 会自动填充数据集和表, 作业开始将输出发送到 Power BI。 如果作业查询没有生成任何结果, 则不会创建数据集和表。 如果 Power BI 已有与此流分析作业中提供的数据集和表同名的数据集和表, 则会覆盖现有数据。
>

### <a name="create-a-schema"></a>创建架构
如果用户尚不存在, Azure 流分析将为用户创建 Power BI 数据集和表架构。 在所有其他情况下，表可以更新使用新值。 目前, 数据集中只能存在一个表。 

Power BI 使用先进先出 (FIFO) 保留策略。 数据将在表中收集, 直到达到200000行。

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>将数据类型从流分析转换为 Power BI
如果输出架构更改，Azure 流分析会在运行时动态更新数据模型。 列名称更改、列类型更改，以及添加或删除列，这些都会进行跟踪。

如果 Power BI 数据集和表不存在, 此表将介绍从[流分析数据类型](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)到 Power BI[实体数据模型 (EDM) 类型](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)的数据类型转换。

从流分析 | 到 Power BI
-----|-----
bigint | Int64
nvarchar(max) | String
datetime | Datetime
浮点数 | Double
记录数组 | String 类型、常量值 "IRecord" 或 "IArray"

### <a name="update-the-schema"></a>更新架构
流分析根据输出中的第一组事件推断数据模型架构。 稍后, 如有必要, 将更新数据模型架构以容纳可能不适合原始架构的传入事件。

`SELECT *`避免查询阻止跨行进行动态架构更新。 除了潜在的性能影响, 它可能会导致结果所花费的时间不确定性。 选择需要在 Power BI 仪表板上显示的确切字段。 此外，数据值应与所选数据类型相符。


上一个/当前 | Int64 | String | Datetime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | Double
Double | Double | String | String | Double
String | String | String | String | String 
datetime | String | String |  datetime | String

## <a name="table-storage"></a>表存储

[Azure 表存储](../storage/common/storage-introduction.md)提供了具有高可用性且可大规模缩放的存储，因此应用程序可以自动缩放以满足用户需求。 表存储是 Microsoft 的 NoSQL 键/属性存储, 可用于对架构的约束较少的结构化数据。 Azure 表存储可用于持久地存储数据，方便进行高效的检索。

下表列出了用于创建表输出的属性名称及其说明。

| 属性名 | 说明 |
| --- | --- |
| 输出别名 |该名称是在查询中使用的友好名称，用于将查询输出定向到此表存储。 |
| 存储帐户 |存储帐户的名称（正在向该存储帐户发送输出）。 |
| 存储帐户密钥 |与存储帐户关联的访问密钥。 |
| 表单名称 |表的名称。 如果表不存在，则创建该表。 |
| 分区键 |包含分区键的输出列的名称。 分区键是某个表中分区的唯一标识符，分区键构成了实体主键的第一部分。 它是最大可为 1 KB 的字符串值。 |
| 行键 |包含行键的输出列的名称。 行键是某个分区中实体的唯一标识符。 行键构成了实体主键的第二部分。 行键是最大可为 1 KB 的字符串值。 |
| 批处理大小 |批处理操作的记录数。 默认值 (100) 对大部分作业来说都已足够。 有关修改设置的详细信息，请参阅[表批处理操作规范](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._table_batch_operation)。 |

## <a name="service-bus-queues"></a>服务总线队列

[服务总线队列](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)为一个或多个竞争使用方提供 FIFO 消息传递。 通常, 接收方按消息添加到队列中的临时顺序接收和处理消息。 每条消息仅由一个消息使用方接收并处理。

下表列出了用于创建队列输出的属性名称及其说明。

| 属性名 | 说明 |
| --- | --- |
| 输出别名 |该名称是在查询中使用的易记名称，用于将查询输出定向到此服务总线队列。 |
| 服务总线命名空间 |一组消息处理实体的容器。 |
| 队列名称 |服务总线队列的名称。 |
| 队列策略名称 |创建队列时，还可以在“队列配置”选项卡上创建共享的访问策略。每个共享访问策略具有名称、所设权限以及访问密钥。 |
| 队列策略密钥 |用于对服务总线命名空间的访问权限进行身份验证的共享访问密钥。 |
| 事件序列化格式 |输出数据的序列化格式。 支持 JSON、CSV 和 Avro。 |
| 编码 |对于 CSV 和 JSON，目前只支持 UTF-8 这种编码格式。 |
| 分隔符 |仅适用于 CSV 序列化。 流分析支持一些常用分隔符，以便采用 CSV 格式序列化数据。 支持的值为逗号、分号、空格、制表符和竖线。 |
| 格式 |仅适用于 JSON 类型。 “行分隔”指定通过新行分隔各个 JSON 对象，从而格式化输出。 “数组”指定输出会被格式化为 JSON 对象的数组。 |
| 属性列 | 可选。 需要作为传出消息的用户属性而不是有效负载附加的逗号分隔列。 [输出的自定义元数据属性](#custom-metadata-properties-for-output)部分详细介绍了此功能。 |

分区数[基于服务总线 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分区键是每个分区的唯一整数值。

## <a name="service-bus-topics"></a>服务总线主题
服务总线队列提供从发送方到接收方的一对一通信方法。 [服务总线主题](https://msdn.microsoft.com/library/azure/hh367516.aspx)提供了一对多的通信形式。

下表列出了用于创建服务总线主题输出的属性名称及其说明。

| 属性名 | 说明 |
| --- | --- |
| 输出别名 |在查询中使用的友好名称, 用于将查询输出定向到此服务总线主题。 |
| 服务总线命名空间 |一组消息处理实体的容器。 创建新的事件中心后，还创建了 Service Bus 命名空间。 |
| 主题名称 |主题是消息传送实体，类似于事件中心和队列。 它们旨在从设备和服务收集事件流。 创建主题时, 还会为其提供特定的名称。 除非创建了订阅, 否则发送到主题的消息不可用, 因此请确保主题下存在一个或多个订阅。 |
| 主题策略名称 |在创建服务总线主题时, 还可以在该主题的 "**配置**" 选项卡上创建共享访问策略。每个共享访问策略具有名称、所设权限以及访问密钥。 |
| 主题策略密钥 |用于对服务总线命名空间的访问权限进行身份验证的共享访问密钥。 |
| 事件序列化格式 |输出数据的序列化格式。 支持 JSON、CSV 和 Avro。 |
| 编码 |如果使用 CSV 或 JSON 格式，则必须指定一种编码格式。 目前，UTF-8 是仅受支持的编码格式。 |
| 分隔符 |仅适用于 CSV 序列化。 流分析支持一些常用分隔符，以便采用 CSV 格式序列化数据。 支持的值为逗号、分号、空格、制表符和竖线。 |
| 属性列 | 可选。 需要作为传出消息的用户属性而不是有效负载附加的逗号分隔列。 [输出的自定义元数据属性](#custom-metadata-properties-for-output)部分详细介绍了此功能。 |

分区数[基于服务总线 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分区键是每个分区的唯一整数值。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/)是一种全球分布式数据库服务, 它提供全球范围内无限制的弹性缩放、丰富的查询, 以及针对架构不可知的数据模型的自动索引。 若要了解有关流分析 Azure Cosmos DB 容器选项的信息, 请参阅将[Azure Cosmos DB 作为输出的流分析](stream-analytics-documentdb-output.md)一文。

流分析 Azure Cosmos DB 输出当前在 Azure 中国世纪互联和 Azure 德国 (T 系统国际) 区域中不可用。

> [!Note]
> 目前, Azure 流分析仅支持使用 SQL API 连接到 Azure Cosmos DB。
> 尚不支持使用其他 Azure Cosmos DB API。 如果使用其他 API 将 Azure 流分析指向 创建的 Azure Cosmos DB 帐户，则可能无法正确存储数据。

下表描述了用于创建 Azure Cosmos DB 输出的属性。

| 属性名 | 说明 |
| --- | --- |
| 输出别名 | 用于在流分析查询中引用此输出的别名。 |
| 接收器 | Azure Cosmos DB。 |
| 导入选项 | 选择“从订阅中选择 Cosmos DB”或“手动提供 Cosmos DB 设置”。
| 帐户 ID | Azure Cosmos DB 帐户的名称或终结点 URI。 |
| 帐户密钥 | Azure Cosmos DB 帐户的共享访问密钥。 |
| 数据库 | Azure Cosmos DB 数据库名称。 |
| 容器名称 | 要使用的容器名称, 该名称必须存在于 Cosmos DB 中。 例如：  <br /><ul><li> _MyContainer_:名为 "MyContainer" 的容器必须存在。</li>|
| 文档 ID |可选。 输出事件中的字段的名称，该字段用于指定插入或更新操作所基于的主键。

## <a name="azure-functions"></a>Azure Functions
Azure Functions 是一项无服务器计算服务, 可用于按需运行代码, 而无需显式预配或管理基础结构。 它允许实现由 Azure 或合作伙伴服务中发生的事件触发的代码。 这种 Azure Functions 响应触发器的能力使其成为 Azure 流分析的自然输出。 此输出适配器使用户能够将流分析连接到 Azure Functions, 并运行一个脚本或一段代码来响应各种事件。

流分析 Azure Functions 输出当前在 Azure 中国世纪互联和 Azure 德国 (T 系统国际) 区域中不可用。

Azure 流分析通过 HTTP 触发器调用 Azure Functions。 Azure Functions 输出适配器可用于以下可配置属性:

| 属性名 | 描述 |
| --- | --- |
| Function App |Azure Functions 应用程序的名称。 |
| 函数 |Azure Functions 应用中函数的名称。 |
| Key |如果要使用其他订阅中的 Azure 函数, 可以通过提供访问函数的密钥来实现此目的。 |
| 最大批处理大小 |一种属性, 可用于设置发送到 Azure 函数的每个输出批的最大大小。 输入单元以字节为单位。 默认情况下, 此值为262144字节 (256 KB)。 |
| 最大批处理计数  |一个属性, 该属性允许你指定发送到 Azure Functions 的每个批中的最大事件数。 默认值为 100。 |

当 Azure 流分析收到 Azure 函数发出的 413 ("http 请求实体过大") 异常时, 它将减少其发送到 Azure Functions 的批的大小。 在 Azure Function 代码中，使用此异常以确保 Azure 流分析不会发送过大的批。 此外, 请确保函数中使用的最大批处理计数和大小值与在流分析门户中输入的值一致。

> [!NOTE]
> 在测试连接过程中, 流分析会将空的批发送到 Azure Functions, 以测试两个是否工作之间的连接。 请确保函数应用处理空的批处理请求, 以确保测试连接通过。

此外, 在某个时间范围内没有事件登陆的情况下, 不会生成任何输出。 因此, 不会调用**computeResult**函数。 此行为与内置窗口化聚合函数一致。

## <a name="custom-metadata-properties-for-output"></a>用于输出的自定义元数据属性 

可以将查询列作为用户属性附加到传出消息。 这些列不会进入有效负载。 这些属性以字典形式出现在输出消息中。 *Key*是列名称,*值*是属性字典中的列值。 除了记录和数组, 还支持所有流分析数据类型。  

支持的输出: 
* 服务总线队列 
* 服务总线主题 
* 事件中心 

在下面的示例中, 我们将这两`DeviceId`个`DeviceStatus`字段添加到元数据。 
* 查询：`select *, DeviceId, DeviceStatus from iotHubInput`
* 输出配置:`DeviceId,DeviceStatus`

![属性列](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

以下屏幕截图显示了通过[服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer)在 EventHub 中检查的输出消息属性。

![事件自定义属性](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="partitioning"></a>分区

下表汇总了分区支持，以及每个输出类型的输出写入器数目：

| 输出类型 | 分区支持 | 分区键  | 输出写入器数目 |
| --- | --- | --- | --- |
| Azure Data Lake Store | 是 | 在路径前缀模式中使用 {date} 和 {time} 标记。 选择日期格式, 如 YYYY/MM/DD、DD/MM/YYYY 或 MM-DD。 HH 用于时间格式。 | 按照[完全可并行化的查询](stream-analytics-scale-jobs.md)的输入分区。 |
| Azure SQL 数据库 | 是, 需要启用。 | 基于查询中的 PARTITION BY 子句。 | 如果启用了 "继承分区" 选项, 则会按照[完全可并行化查询](stream-analytics-scale-jobs.md)的输入分区进行。 若要详细了解如何在将数据加载到 Azure SQL 数据库时获得更好的写入吞吐量性能, 请参阅 azure[流分析输出到 AZURE Sql database](stream-analytics-sql-output-perf.md)。 |
| Azure Blob 存储 | 是 | 使用路径模式中的事件字段的 {date} 和 {time} 标记。 选择日期格式, 如 YYYY/MM/DD、DD/MM/YYYY 或 MM-DD。 HH 用于时间格式。 可以通过单个自定义事件属性 {fieldname} 或 {datetime:\<specifier>} 对 blob 输出进行分区。 | 按照[完全可并行化的查询](stream-analytics-scale-jobs.md)的输入分区。 |
| Azure 事件中心 | 是 | 是 | 按分区对齐方式变化。<br /> 如果事件中心输出的分区键与上游 (上一个) 查询步骤相同, 则写入器的数量与事件中心输出中的分区数相同。 每个编写器都使用[EventHubSender 类](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet)将事件发送到特定分区。 <br /> 当事件中心输出的分区键与上游 (上一个) 查询步骤不一致时, 写入器数与该前一步骤中的分区数相同。 每个编写器都使用**EventHubClient**中的[SendBatchAsync 类](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet)将事件发送到所有输出分区。 |
| Power BI | 否 | None | 不适用。 |
| Azure 表存储 | 是 | 任何输出列。  | 按照[完全并行化的查询](stream-analytics-scale-jobs.md)的输入分区。 |
| Azure 服务总线主题 | 是 | 自动选择。 分区数基于[服务总线 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分区键是每个分区的唯一整数值。| 与输出主题中的分区数量相同。  |
| Azure 服务总线队列 | 是 | 自动选择。 分区数基于[服务总线 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分区键是每个分区的唯一整数值。| 与输出队列中的分区数量相同。 |
| Azure Cosmos DB | 是 | 基于查询中的 PARTITION BY 子句。 | 按照[完全并行化的查询](stream-analytics-scale-jobs.md)的输入分区。 |
| Azure Functions | 否 | 无 | 不适用。 |

还可以使用`INTO <partition count>`查询中的 (参见[INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) 子句来控制输出编写器的数目, 这在实现所需的作业拓扑时非常有用。 如果输出适配器未分区，则一个输入分区中缺少数据将导致延迟最多可达延迟到达的时间量。 在这种情况下, 输出将合并到单个编写器, 这可能会导致管道中出现瓶颈。 若要了解有关延迟到达策略的详细信息, 请参阅[Azure 流分析事件顺序注意事项](stream-analytics-out-of-order-and-late-events.md)。

## <a name="output-batch-size"></a>输出批大小
Azure 流分析使用可变大小的批处理来处理事件并写入输出。 通常, 流分析引擎每次不写入一条消息, 并使用批处理来提高效率。 当传入事件和传出事件的速率较高时, 流分析会使用更大的批次。 输出速率低时，使用较小的批来保证低延迟。

下表说明了输出批处理的一些注意事项:

| 输出类型 | 最大消息大小 | 批大小优化 |
| :--- | :--- | :--- |
| Azure Data Lake Store | 请参阅[Data Lake Storage 限制](../azure-subscription-service-limits.md#data-lake-store-limits)。 | 每个写入操作使用最多 4 MB。 |
| Azure SQL 数据库 | 可使用最大批处理计数进行配置。 默认情况下, 每个单次大容量插入最多10000和最多100行。<br />请参阅[AZURE SQL 限制](../sql-database/sql-database-resource-limits.md)。 |  最初批量插入的每个批处理都具有最大批处理计数。 根据 SQL 中的可重试错误, 将 Batch 拆分为半 (截至最小批处理计数)。 |
| Azure Blob 存储 | 请参阅[Azure 存储限制](../azure-subscription-service-limits.md#storage-limits)。 | Blob 块的最大大小为 4 MB。<br />最大 blob bock 计数为50000。 |
| Azure 事件中心  | 每条消息 256 KB 或 1 MB。 <br />请参阅[事件中心限制](../event-hubs/event-hubs-quotas.md)。 |  如果输入/输出分区未对齐, 则每个事件将在`EventData`中单独打包并在最大消息大小的一批中发送。 如果使用了[自定义元数据属性](#custom-metadata-properties-for-output), 也会发生这种情况。 <br /><br />  当输入/输出分区对齐时, 多个事件会打包到一个`EventData`实例中, 最多可达最大消息大小并发送。 |
| Power BI | 请参阅[Power BI REST API 限制](https://msdn.microsoft.com/library/dn950053.aspx)。 |
| Azure 表存储 | 请参阅[Azure 存储限制](../azure-subscription-service-limits.md#storage-limits)。 | 默认值为每个单个事务100个实体。 您可以根据需要将其配置为较小的值。 |
| Azure 服务总线队列   | 对于标准层, 每条消息 256 KB, "高级" 级别为1MB。<br /> 请参阅[服务总线限制](../service-bus-messaging/service-bus-quotas.md)。 | 每条消息使用一个事件。 |
| Azure 服务总线主题 | 对于标准层, 每条消息 256 KB, "高级" 级别为1MB。<br /> 请参阅[服务总线限制](../service-bus-messaging/service-bus-quotas.md)。 | 每条消息使用一个事件。 |
| Azure Cosmos DB   | 请参阅[Azure Cosmos DB 限制](../azure-subscription-service-limits.md#azure-cosmos-db-limits)。 | 批处理大小和写入频率根据 Azure Cosmos DB 的响应进行动态调整。 <br /> 流分析没有预先确定的限制。 |
| Azure Functions   | | 默认批大小为262144字节 (256 KB)。 <br /> 每个批处理的默认事件计数为100。 <br /> 批大小是可配置的，可在流分析[输出选项](#azure-functions)中增加或减少。

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
