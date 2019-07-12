---
title: 为 Azure SQL 数据仓库设计 ELT 而非 ETL | Microsoft Docs
description: 设计用于将数据加载到 Azure SQL 数据仓库的提取、加载和转换 (ELT) 过程而非 ETL 过程。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 05/10/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: fa688f40f8eb968f2c388601b387e4f584951a91
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595604"
---
# <a name="designing-a-polybase-data-loading-strategy-for-azure-sql-data-warehouse"></a>为 Azure SQL 数据仓库设计 PolyBase 数据加载策略

传统 SMP 数据仓库使用提取、转换和加载 (ETL) 进程加载数据。 Azure SQL 数据仓库是一种大规模并行处理 (MPP) 体系结构，利用了计算和存储资源的可伸缩性和灵活性。 利用提取、加载和转换 (ELT) 过程可以利用 MPP，不再需要加载之前转换数据所需的资源。 虽然 SQL 数据仓库支持许多加载方法，其中包括非 Polybase 选项（如 BCP 和 SQL BulkCopy API），但加载数据的最快最灵活的方式是通过 PolyBase。  PolyBase 是这样一种技术，可以通过 T-SQL 语言访问存储在 Azure Blob 存储或 Azure Data Lake Storage 中的外部数据。

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]


## <a name="what-is-elt"></a>什么是 ELT？

提取、加载和转换 (ELT) 是一个过程，该过程从源系统中提取数据，将其加载到数据仓库中，然后进行转换。 

实现用于 SQL 数据仓库的 PolyBase ELT 的基本步骤如下：

1. 将源数据提取到文本文件中。
2. 将数据移入 Azure Blob 存储或 Azure Data Lake Store。
3. 准备要加载的数据。
4. 使用 PolyBase 将数据载入 SQL 数据仓库临时表。 
5. 转换数据。
6. 将数据插入生产表。


有关加载教程，请参阅[使用 PolyBase 将数据从 Azure Blob 存储加载到 Azure SQL 数据仓库](load-data-from-azure-blob-storage-using-polybase.md)。

有关详细信息，请参阅[加载模式博客](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)。 


## <a name="1-extract-the-source-data-into-text-files"></a>1.将源数据提取到文本文件中

从源系统中取出数据的过程取决于存储位置。  目标是将数据移动到 PolyBase 支持的带分隔符的文本文件中。 

### <a name="polybase-external-file-formats"></a>PolyBase 外部文件格式

PolyBase 从 UTF-8 和 UTF-16 编码的带分隔符文本文件加载数据。 除了带分隔符的文本文件以外，它还可以从 Hadoop 文件格式、RC 文件、ORC 和 Parquet 加载数据。 PolyBase 还可以从 Gzip 和 Snappy 压缩文件加载数据。 PolyBase 目前不支持扩展的 ASCII、固定宽度格式以及 WinZip、JSON 和 XML 等嵌套格式。 如果要从 SQL Server 中导出，可以使用 [bcp 命令行工具](/sql/tools/bcp-utility)，以将数据导出为带分隔符的文本文件。 Parquet 到 SQL DW 数据类型的映射如下：

| **Parquet 数据类型** |                      **SQL 数据类型**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        boolean        |                             bit                              |
|        double         |                            float                             |
|         float         |                             real                             |
|        double         |                            money                             |
|        double         |                          smallmoney                          |
|        string         |                            nchar                             |
|        string         |                           nvarchar                           |
|        string         |                             char                             |
|        string         |                           varchar                            |
|        binary         |                            binary                            |
|        binary         |                          varbinary                           |
|       timestamp       |                             date                             |
|       timestamp       |                        smalldatetime                         |
|       timestamp       |                          datetime2                           |
|       timestamp       |                           DATETIME                           |
|       timestamp       |                             time                             |
|       date        | 1） 加载为 int 和强制转换为日期 </br> 2)[使用 Azure Databricks SQL DW 连接器](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse#load-data-into-azure-sql-data-warehouse)与 </br> spark.conf.set( "spark.sql.parquet.writeLegacyFormat", "true" ) </br> (**更新即将推出**) |
|        decimal        | [使用 Azure Databricks SQL DW 连接器](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse#load-data-into-azure-sql-data-warehouse)与 </br> spark.conf.set( "spark.sql.parquet.writeLegacyFormat", "true" ) </br> (**更新即将推出**) |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2.将数据移入 Azure Blob 存储或 Azure Data Lake Store

若要将数据移入 Azure 存储，可将它移到 [Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)或 [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)。 应将数据存储在任一位置的文本文件中。 PolyBase 可从任一位置加载数据。

可使用来将数据移到 Azure 存储的工具和服务：

- [Azure ExpressRoute](../expressroute/expressroute-introduction.md) 服务可以增强网络吞吐量、性能和可预测性。 ExpressRoute 是通过专用连接将数据路由到 Azure 的服务。 ExpressRoute 连接不通过公共 Internet 路由数据。 与基于公共 Internet 的典型连接相比，这些连接提供更高的可靠性、更快的速度、更低的延迟和更高的安全性。
- [AZCopy 实用工具](../storage/common/storage-moving-data.md)可以通过公共 Internet 将数据移到 Azure 存储。 如果数据小于 10 TB，则很适合使用此工具。 若要使用 AZCopy 定期执行加载操作，请测试网络速度是否在可接受的范围内。 
- [Azure 数据工厂 (ADF)](../data-factory/introduction.md) 提供一个可以安装在本地服务器上的网关。 然后，你可以创建管道，以便将数据从本地服务器移到 Azure 存储。 若要将数据工厂与 SQL 数据仓库配合使用，请参阅[将数据加载到 SQL 数据仓库中](/azure/data-factory/load-azure-sql-data-warehouse)。


## <a name="3-prepare-the-data-for-loading"></a>3.准备要加载的数据

在将存储帐户中的数据载入 SQL 数据仓库之前，可能需要对其进行准备和清理。 可以在数据仍保留在源中、将数据导出到文本文件时或者在数据进入 Azure 存储之后执行数据准备。  最好是在加载过程的早期阶段处理数据。  

### <a name="define-external-tables"></a>定义外部表

在加载数据之前，需要在数据仓库中定义外部表。 PolyBase 使用外部表来定义和访问 Azure 存储中的数据。 外部表和数据库视图相似。 外部表包含表模式，并指向存储在数据仓库外部的数据。 

定义外部表涉及到指定数据源、文本文件的格式和表定义。 下面是需要的 T-SQL 语法主题：
- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql)（创建外部数据源）
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql)

有关创建外部对象的示例，请参阅加载教程中的[创建外部表](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data)步骤。

### <a name="format-text-files"></a>设置文本文件的格式

定义外部对象后，需要使文本文件中的行与外部表和文件格式定义相符。 文本文件的每一行中的数据必须与表定义相符。
设置文本文件的格式：

- 如果数据来自非关系源，则需要将其转换为行与列。 不管数据来自关系源还是非关系源，都必须转换数据，使之与数据预期要载入到的表的列定义相符。 
- 设置本文件中数据的格式，使之与 SQL 数据仓库目标表中的列和数据类型相符。 外部文本文件与数据仓库表中的数据类型不相符会导致在加载期间拒绝行。
- 使用终止符分隔文本文件中的字段。  请务必使用源数据中不包含的字符或字符序列。 使用通过 [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql) 指定的终止符。


## <a name="4-load-the-data-into-sql-data-warehouse-staging-tables-using-polybase"></a>4.使用 PolyBase 将数据载入 SQL 数据仓库临时表

这是将数据加载到临时表中的最佳做法。 利用临时表，可以处理错误，且不会干扰生产表。 将数据插入生产表之前，还可以通过临时表使用 SQL 数据仓库 MPP 来运行数据转换。

### <a name="options-for-loading-with-polybase"></a>使用 PolyBase 加载数据的选项

若要使用 PolyBase 加载数据，可以使用下列任一加载选项：

- 如果数据位于 Azure Blob 存储或 Azure Data Lake Store 中，则 [PolyBase 与 T-SQL](load-data-from-azure-blob-storage-using-polybase.md) 可以发挥作用。 使用此方法可以获得加载过程的最大控制度，不过同时需要定义外部数据对象。 其他方法在你将源表映射到目标表时，在幕后定义这些对象。  若要协调 T-SQL 负载，可以使用 Azure 数据工厂、SSIS 或 Azure Functions。 
- 如果源数据位于本地 SQL Server 或云中的 SQL Server，则 [PolyBase 与 SSIS](/sql/integration-services/load-data-to-sql-data-warehouse) 可以发挥作用。 SSIS 定义源到目标表的映射，同时可协调负载。 如果已有 SSIS 包，可将这些包修改为使用新的数据仓库目标。 
- [PolyBase 与 Azure 数据工厂 (ADF)](sql-data-warehouse-load-with-data-factory.md) 是另一个业务流程工具。  它定义管道并计划作业。 
- [使用 Azure DataBricks 的 PolyBase](../azure-databricks/databricks-extract-load-sql-data-warehouse.md)将数据从 SQL 数据仓库表传输到 Databricks 数据帧和/或将数据从 Databricks 数据帧写入到使用 PolyBase 的 SQL 数据仓库表。

### <a name="non-polybase-loading-options"></a>非 PolyBase 加载选项

如果数据与 PolyBase 不兼容，可以使用 [bcp](/sql/tools/bcp-utility) 或 [SQLBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx)。 bcp 将数据直接加载到 SQL 数据仓库，而无需经过 Azure Blob 存储，但只适用于小规模的加载。 请注意，这些选项的加载性能明显低于 PolyBase。 


## <a name="5-transform-the-data"></a>5.转换数据

在数据已进入临时表时，请执行工作负荷所需的转换。 然后将数据移到生产表。


## <a name="6-insert-the-data-into-production-tables"></a>6.将数据插入生产表

INSERT INTO ...SELECT 语句将数据从临时表移到永久表。 

设计 ETL 过程时，请尝试针对一个较小的测试示例运行该过程。 尝试将表中的 1000 行提取到某个文件，将该文件移到 Azure，然后将其载入临时表。 


## <a name="partner-loading-solutions"></a>合作伙伴加载解决方案

我们的很多合作伙伴都提供加载解决方案。 有关详细信息，请参阅我们的[解决方案合作伙伴](sql-data-warehouse-partner-business-intelligence.md)列表。 


## <a name="next-steps"></a>后续步骤

有关加载指南，请参阅[加载数据的指南](guidance-for-loading-data.md)。


