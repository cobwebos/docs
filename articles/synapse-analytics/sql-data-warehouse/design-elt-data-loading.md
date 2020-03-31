---
title: 设计 ELT 而不是 ETL
description: 在 Azure 突触分析中为 SQL 分析实施灵活的数据加载策略
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/19/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 0111f3edc3ab7a681e22153828d1bb430a14e57b
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348989"
---
# <a name="data-loading-strategies-for-data-warehousing"></a>数据仓库的数据加载策略

传统的 SMP 数据仓库通过提取、转换和加载 (ETL) 过程来加载数据。 Azure Synapse 分析中的 SQL 池具有大量并行处理 （MPP） 体系结构，可利用计算和存储资源的可扩展性和灵活性。 利用提取、加载和转换 (ELT) 过程可以利用 MPP，不再需要加载之前转换数据所需的资源。 虽然 SQL 池支持许多加载方法，包括流行的 SQL Server 选项（如 BCP 和 SQL BulkCopy API），但加载数据的最快、最可扩展的方式是通过 PolyBase 外部表和[COPY 语句](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)（预览）。 使用 PolyBase 和 COPY 语句，您可以通过 T-SQL 语言访问存储在 Azure Blob 存储或 Azure 数据湖存储中的外部数据。 为了在加载时具有最大的灵活性，我们建议使用 COPY 语句。

> [!NOTE]  
> COPY 语句目前提供公共预览版。 要提供反馈，请发送电子邮件至以下通讯组列表： sqldwcopypreview@service.microsoft.com。


> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]


## <a name="what-is-elt"></a>什么是 ELT？

提取、加载和转换 (ELT) 是指将数据从源系统提取并加载到数据仓库然后再进行转换的过程。 

实现 ELT 的基本步骤是：

1. 将源数据提取到文本文件中。
2. 将数据移入 Azure Blob 存储或 Azure Data Lake Store。
3. 准备要加载的数据。
4. 使用 PolyBase 或 COPY 命令将数据加载到暂存表中。 
5. 转换数据。
6. 将数据插入生产表。


有关 PolyBase 加载教程，请参阅[使用 PolyBase 从 Azure Blob 存储加载数据](load-data-from-azure-blob-storage-using-polybase.md)。

有关详细信息，请参阅[加载模式博客](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)。 


## <a name="1-extract-the-source-data-into-text-files"></a>1. 将源数据提取到文本文件中

从源系统中取出数据的过程取决于存储位置。  目标是将数据移入 PolyBase 和 COPY 支持的带分隔符文本文件或 CSV 文件。 

### <a name="polybase-and-copy-external-file-formats"></a>PolyBase 和 COPY 外部文件格式

使用 PolyBase 和 COPY 语句，可以从 UTF-8 和 UTF-16 编码的带分隔符文本文件或 CSV 文件加载数据。 除了带分隔符文本文件或 CSV 文件以外，它还可以从 ORC 和 Parquet 等 Hadoop 文件格式加载数据。 PolyBase 和 COPY 语句还可以从 Gzip 和 Snappy 压缩文件加载数据。 不支持扩展的 ASCII、固定宽度格式，以及 WinZip 或 XML 等嵌套格式。 如果是从 SQL Server 导出，则可使用 [bcp 命令行工具](/sql/tools/bcp-utility?view=azure-sqldw-latest)将数据导出到带分隔符的文本文件中。 

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. 将数据放入 Azure Blob 存储或 Azure 数据湖存储

要将数据位于 Azure 存储中，可以将其移动到[Azure Blob 存储](../../storage/blobs/storage-blobs-introduction.md)或[Azure 数据湖存储 Gen2](../../data-lake-store/data-lake-store-overview.md)。 应将数据存储在任一位置的文本文件中。 PolyBase 和 COPY 语句可从任一位置加载数据。

可使用来将数据移到 Azure 存储的工具和服务：

- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) 服务可以增强网络吞吐量、性能和可预测性。 ExpressRoute 是通过专用连接将数据路由到 Azure 的服务。 ExpressRoute 连接不通过公共 Internet 路由数据。 与基于公共 Internet 的典型连接相比，这些连接提供更高的可靠性、更快的速度、更低的延迟和更高的安全性。
- [AZCopy 实用工具](../../storage/common/storage-choose-data-transfer-solution.md)可以通过公共 Internet 将数据移到 Azure 存储。 如果数据小于 10 TB，则很适合使用此工具。 若要使用 AZCopy 定期执行加载操作，请测试网络速度是否在可接受的范围内。 
- [Azure 数据工厂 (ADF)](../../data-factory/introduction.md) 提供一个可以安装在本地服务器上的网关。 然后，你可以创建管道，以便将数据从本地服务器移到 Azure 存储。 要将数据工厂与 SQL 分析一起使用，请参阅[加载 SQL 分析的数据](../../data-factory/load-azure-sql-data-warehouse.md)。


## <a name="3-prepare-the-data-for-loading"></a>3. 准备加载数据

您可能需要在加载之前准备和清理存储帐户中的数据。 可以在数据仍保留在源中、将数据导出到文本文件时或者在数据进入 Azure 存储之后执行数据准备。  最好是在加载过程的早期阶段处理数据。  

### <a name="define-external-tables"></a>定义外部表

如果使用 PolyBase，则需要在加载之前在数据仓库中定义外部表。 COPY 语句不需要外部表。 PolyBase 使用外部表来定义和访问 Azure 存储中的数据。 外部表和数据库视图相似。 外部表包含表模式，并指向存储在数据仓库外部的数据。 

定义外部表涉及到指定数据源、文本文件的格式和表定义。 所需的 T-SQL 语法主题为：
- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest)
- [创建外部表](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest)

加载 Parquet 时，SQL 数据类型映射为：

| **Parquet 数据类型** | **SQL 数据类型** |
| :-------------------: | :---------------: |
|        tinyint        |      tinyint      |
|       smallint        |     smallint      |
|          int          |        int        |
|        bigint         |      bigint       |
|        boolean        |        bit        |
|        double         |       FLOAT       |
|         FLOAT         |       real        |
|        double         |       money       |
|        double         |    smallmoney     |
|        字符串         |       nchar       |
|        字符串         |     nvarchar      |
|        字符串         |       char        |
|        字符串         |      varchar      |
|        binary         |      binary       |
|        binary         |     varbinary     |
|       timestamp       |       date        |
|       timestamp       |   smalldatetime   |
|       timestamp       |     datetime2     |
|       timestamp       |     datetime      |
|       timestamp       |       time        |
|         date          |       date        |
|        Decimal        |      Decimal      |

有关创建外部对象的示例，请参阅加载教程中的[创建外部表](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data)步骤。

### <a name="format-text-files"></a>设置文本文件的格式

使用 PolyBase 时，定义的外部对象需要使文本文件中的行与外部表和文件格式定义相符。 文本文件的每一行中的数据必须与表定义相符。
设置文本文件的格式：

- 如果数据来自非关系源，则需要将其转换为行与列。 不管数据来自关系源还是非关系源，都必须转换数据，使之与数据预期要载入到的表的列定义相符。 
- 设置文本文件中的数据格式，以便与目标表中的列和数据类型对齐。 外部文本文件与数据仓库表中的数据类型不相符会导致在加载期间拒绝行。
- 使用终止符分隔文本文件中的字段。  请务必使用源数据中不包含的字符或字符序列。 使用通过 [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql) 指定的终止符。


## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. 使用 PolyBase 或 COPY 语句加载数据

这是将数据加载到临时表中的最佳做法。 利用临时表，可以处理错误，且不会干扰生产表。 暂存表还使您能够在将数据插入生产表中之前使用 SQL 池 MPP 进行数据转换。 使用 COPY 载入临时表时，需要预先创建表。

### <a name="options-for-loading-with-polybase-and-copy-statement"></a>使用 PolyBase 和 COPY 语句加载数据的选项

若要使用 PolyBase 加载数据，可以使用下列任一加载选项：

- 如果数据位于 Azure Blob 存储或 Azure Data Lake Store 中，则 [PolyBase 与 T-SQL](load-data-from-azure-blob-storage-using-polybase.md) 可以发挥作用。 使用此方法可以获得加载过程的最大控制度，不过同时需要定义外部数据对象。 其他方法在你将源表映射到目标表时，在幕后定义这些对象。  若要协调 T-SQL 负载，可以使用 Azure 数据工厂、SSIS 或 Azure Functions。 
- 如果源数据位于本地 SQL Server 或云中的 SQL Server，则 [PolyBase 与 SSIS](/sql/integration-services/load-data-to-sql-data-warehouse) 可以发挥作用。 SSIS 定义源到目标表的映射，同时可协调负载。 如果已有 SSIS 包，可将这些包修改为使用新的数据仓库目标。 
- [Azure 数据工厂 (ADF) 的 PolyBase 和 COPY 语句](../../data-factory/load-azure-sql-data-warehouse.md)是另一个业务流程工具。  它定义管道并计划作业。 
- [具有 Azure 数据砖块的 PolyBase](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md)将数据从表传输到数据砖块数据帧和/或使用 PolyBase 将数据从数据块数据帧写入表。

### <a name="other-loading-options"></a>其他加载选项

除 PolyBase 和 COPY 语句以外，还可以使用 [bcp](/sql/tools/bcp-utility?view=azure-sqldw-latest) 或 [SQLBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx)。 bcp 直接加载到数据库而不经过 Azure Blob 存储，并且仅用于小负载。 请注意，这些选项的加载性能低于 PolyBase 和 COPY 语句。 


## <a name="5-transform-the-data"></a>5. 转换数据

在数据已进入临时表时，请执行工作负荷所需的转换。 然后将数据移到生产表。


## <a name="6-insert-the-data-into-production-tables"></a>6. 将数据插入生产表中

插入到 ...SELECT 语句将数据从暂存表移动到永久表。 

设计 ETL 过程时，请尝试针对一个较小的测试示例运行该过程。 尝试将表中的 1000 行提取到某个文件，将该文件移到 Azure，然后将其载入临时表。 


## <a name="partner-loading-solutions"></a>合作伙伴加载解决方案

我们的很多合作伙伴都提供加载解决方案。 若要获取更多解决方案，请参阅我们的[解决方案合作伙伴](sql-data-warehouse-partner-business-intelligence.md)的列表。 


## <a name="next-steps"></a>后续步骤

有关加载指南，请参阅[加载数据的指南](guidance-for-loading-data.md)。
