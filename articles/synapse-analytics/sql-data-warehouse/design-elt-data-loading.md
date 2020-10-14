---
title: 不要使用 ETL，应使用设计 ETL
description: 对 Azure Synapse Analytics 中的 Synapse SQL 池实施灵活的数据加载策略
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/13/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: af3eb828e1fd2c4aa14467e5afc18f1b5a0b7fa1
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047703"
---
# <a name="data-loading-strategies-for-synapse-sql-pool"></a>针对 Synapse SQL 池的数据加载策略

传统的 SMP SQL 池通过提取、转换和加载 (ETL) 过程来加载数据。 Azure Synapse Analytics 中的 Synapse SQL 池具有大规模并行处理 (MPP) 体系结构，可以利用计算和存储资源的可伸缩性和灵活性。

可以通过提取、加载和转换 (ELT) 过程来利用 MPP，无需在加载数据之前投入资源来转换数据。

虽然 SQL 池支持包括常用 SQL Server 选项（例如 [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 和 [SqlBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)）在内的许多加载方法，但最快且最具可伸缩性的数据加载方法是使用 PolyBase 外部表和 [COPY 语句](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)（预览版）。

使用 PolyBase 和 COPY 语句，可以通过 T-SQL 语言访问存储在 Azure Blob 存储或 Azure Data Lake Store 中的外部数据。 为了在加载时获得最大的灵活性，建议使用 COPY 语句。


## <a name="what-is-elt"></a>什么是 ELT？

提取、加载和转换 (ELT) 是指将数据从源系统提取并加载到 SQL 池然后再进行转换的过程。

实现 ELT 的基本步骤如下：

1. 将源数据提取到文本文件中。
2. 将数据移入 Azure Blob 存储或 Azure Data Lake Store。
3. 准备要加载的数据。
4. 使用 PolyBase 或 COPY 命令将数据载入临时表。
5. 转换数据。
6. 将数据插入生产表。

有关加载教程，请参阅[从 Azure Blob 存储加载数据](load-data-from-azure-blob-storage-using-polybase.md)。

## <a name="1-extract-the-source-data-into-text-files"></a>1.将源数据提取到文本文件中

从源系统中取出数据的过程取决于存储位置。 目标是将数据移入支持的带分隔符的文本文件或 CSV 文件。

### <a name="supported-file-formats"></a>支持的文件格式

使用 PolyBase 和 COPY 语句，可以从 UTF-8 和 UTF-16 编码的带分隔符文本文件或 CSV 文件加载数据。 除了带分隔符文本文件或 CSV 文件以外，它还可以从 ORC 和 Parquet 等 Hadoop 文件格式加载数据。 PolyBase 和 COPY 语句还可以从 Gzip 和 Snappy 压缩文件加载数据。

不支持扩展的 ASCII、固定宽度格式以及 WinZip 或 XML 等嵌套格式。 如果要从 SQL Server 中导出，可以使用 [bcp 命令行工具](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)，以将数据导出为带分隔符的文本文件。

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2.将数据移入 Azure Blob 存储或 Azure Data Lake Store

若要将数据移入 Azure 存储，可将它移到 [Azure Blob 存储](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)或 [Azure Data Lake Store Gen2](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。 应将数据存储在任一位置的文本文件中。 PolyBase 和 COPY 语句可从任一位置加载数据。

可使用以下工具和服务将数据移到 Azure 存储：

- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 服务可以增强网络吞吐量、性能和可预测性。 ExpressRoute 是通过专用连接将数据路由到 Azure 的服务。 ExpressRoute 连接不通过公共 Internet 路由数据。 与基于公共 Internet 的典型连接相比，这些连接提供更高的可靠性、更快的速度、更低的延迟和更高的安全性。
- [AZCopy 实用工具](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)可以通过公共 Internet 将数据移到 Azure 存储。 如果数据小于 10 TB，则很适合使用此工具。 若要使用 AZCopy 定期执行加载操作，请测试网络速度是否在可接受的范围内。
- [Azure 数据工厂 (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 提供一个可以安装在本地服务器上的网关。 然后，你可以创建管道，以便将数据从本地服务器移到 Azure 存储。 若要将数据工厂与 SQL 池配合使用，请参阅[加载 SQL 池数据](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。

## <a name="3-prepare-the-data-for-loading"></a>3.准备要加载的数据

在加载存储帐户中的数据之前，可能需要对其进行准备和清理。 可以在数据仍保留在源中、将数据导出到文本文件时或者在数据进入 Azure 存储之后执行数据准备。  最好是在加载过程的早期阶段处理数据。  

### <a name="define-the-tables"></a>定义表

使用 COPY 语句时，必须先在 SQL 池中定义要加载到的表。

如果使用 PolyBase，则需在加载之前在 SQL 池中定义外部表。 PolyBase 使用外部表来定义和访问 Azure 存储中的数据。 外部表类似于数据库视图。 外部表包含表架构，并指向在 SQL 池外部存储的数据。

定义外部表涉及到指定数据源、文本文件的格式和表定义。 相关的 T-SQL 语法参考文章如下：

- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

加载 Parquet 文件时，使用以下 SQL 数据类型映射：

|                         Parquet 类型                         |   Parquet 逻辑类型（批注）   |  SQL 数据类型   |
| :----------------------------------------------------------: | :-----------------------------------: | :--------------: |
|                           BOOLEAN                            |                                       |       bit        |
|                     BINARY/BYTE_ARRAY                      |                                       |    varbinary     |
|                            DOUBLE                            |                                       |      FLOAT       |
|                            FLOAT                             |                                       |       real       |
|                            INT32                             |                                       |       int        |
|                            INT64                             |                                       |      bigint      |
|                            INT96                             |                                       |    datetime2     |
|                     FIXED_LEN_BYTE_ARRAY                     |                                       |      binary      |
|                            BINARY                            |                 UTF8                  |     nvarchar     |
|                            BINARY                            |                STRING                 |     nvarchar     |
|                            BINARY                            |                 ENUM                  |     nvarchar     |
|                            BINARY                            |                 UUID                  | uniqueidentifier |
|                            BINARY                            |                DECIMAL                |     Decimal      |
|                            BINARY                            |                 JSON                  |  nvarchar(MAX)   |
|                            BINARY                            |                 BSON                  |  varbinary(max)  |
|                     FIXED_LEN_BYTE_ARRAY                     |                DECIMAL                |     Decimal      |
|                          BYTE_ARRAY                          |               INTERVAL                |  varchar(max),   |
|                            INT32                             |             INT(8, true)              |     smallint     |
|                            INT32                             |            INT(16,   true)            |     smallint     |
|                            INT32                             |             INT(32, true)             |       int        |
|                            INT32                             |            INT(8,   false)            |     tinyint      |
|                            INT32                             |            INT(16, false)             |       int        |
|                            INT32                             |           INT(32,   false)            |      bigint      |
|                            INT32                             |                 DATE                  |       date       |
|                            INT32                             |                DECIMAL                |     Decimal      |
|                            INT32                             |            TIME (MILLIS)             |       time       |
|                            INT64                             |            INT(64,   true)            |      bigint      |
|                            INT64                             |           INT(64, false  )            |  decimal(20,0)   |
|                            INT64                             |                DECIMAL                |     Decimal      |
|                            INT64                             |         TIME (MICROS/NANOS)         |       time       |
|                            INT64                             | TIMESTAMP   (MILLIS / MICROS / NANOS) |    datetime2     |
| [复杂类型](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23lists&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=6Luk047sK26ijTzfvKMYc%2FNu%2Fz0AlLCX8lKKTI%2F8B5o%3D&reserved=0) |                 列表                  |   varchar(max)   |
| [复杂类型](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23maps&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=FiThqXxjgmZBVRyigHzfh5V7Z%2BPZHjud2IkUUM43I7o%3D&reserved=0) |                  MAP                  |   varchar(max)   |



有关创建外部对象的示例，请参阅[创建外部表](https://docs.microsoft.com/azure/synapse-analytics/sql/develop-tables-external-tables?tabs=sql-pool)。

### <a name="format-text-files"></a>设置文本文件的格式

使用 PolyBase 时，定义的外部对象需要使文本文件中的行与外部表和文件格式定义相符。 文本文件的每一行中的数据必须与表定义相符。
设置文本文件的格式：

- 如果数据来自非关系源，则需要将其转换为行与列。 不管数据来自关系源还是非关系源，都必须转换数据，使之与数据预期要载入到的表的列定义相符。
- 设置本文件中数据的格式，使之与目标表中的列和数据类型相符。 外部文本文件与 SQL 池表中的数据类型不相符会导致系统在加载期间拒绝行。
- 使用终止符分隔文本文件中的字段。  请务必使用源数据中不包含的字符或字符序列。 使用通过 [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 指定的终止符。

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4.使用 PolyBase 或 COPY 语句加载数据

最佳做法是将数据载入临时表。 使用临时表可以处理错误且不干扰生产表。 将数据插入生产表之前，还可以通过临时表使用 SQL 池并行处理体系结构进行数据转换。

### <a name="options-for-loading"></a>用于加载的选项

若要加载数据，可以使用下列任一加载选项：

- [COPY 语句](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)是推荐使用的加载实用工具，因为它可以无缝且灵活地加载数据。 该语句具有许多 PolyBase 不提供的其他加载功能。 
- [具有 T-SQL 的 PolyBase](load-data-from-azure-blob-storage-using-polybase.md) 要求定义外部数据对象。
- [Azure 数据工厂 (ADF) 的 PolyBase 和 COPY 语句](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)是另一个业务流程工具。  它定义管道并计划作业。
- 源数据位于 SQL Server 时，[具有 SSIS 的 PolyBase](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 运行良好。 SSIS 定义源到目标表的映射，同时可协调负载。 如果已有 SSIS 包，可将这些包修改为使用新的数据仓库目标。
- [将 PolyBase 与 Azure DataBricks 配合使用](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)，可使用 PolyBase 将数据从表传输到 Databricks 数据帧，并且/或者可将 Databricks 数据帧中的数据写入表。

### <a name="other-loading-options"></a>其他加载选项

除 PolyBase 和 COPY 语句以外，还可以使用 [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 或 [SqlBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。 bcp 可将数据直接加载到数据库而无需经过 Azure Blob 存储，但此方法只适用于小规模的加载。

> [!NOTE]
> 这些选项的加载性能低于 PolyBase 和 COPY 语句。

## <a name="5-transform-the-data"></a>5.转换数据

在数据已进入临时表时，请执行工作负荷所需的转换。 然后将数据移到生产表。

## <a name="6-insert-the-data-into-production-tables"></a>6.将数据插入生产表

INSERT INTO ...SELECT 语句将数据从临时表移到永久表。

设计 ETL 过程时，请尝试针对一个较小的测试示例运行该过程。 尝试将表中的 1000 行提取到某个文件，将该文件移到 Azure，然后将其载入临时表。

## <a name="partner-loading-solutions"></a>合作伙伴加载解决方案

我们的很多合作伙伴都提供加载解决方案。 有关详细信息，请参阅我们的[解决方案合作伙伴](sql-data-warehouse-partner-business-intelligence.md)列表。

## <a name="next-steps"></a>后续步骤

有关加载指南，请参阅[加载数据的指南](guidance-for-loading-data.md)。
