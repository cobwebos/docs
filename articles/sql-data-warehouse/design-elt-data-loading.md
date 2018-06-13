---
title: 为 Azure SQL 数据仓库设计 ELT 而非 ETL | Microsoft Docs
description: 设计用于将数据加载到 Azure SQL 数据仓库的提取、加载和转换 (ELT) 过程而非 ETL 过程。
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 5ceb8cfd8efea66dbf17b8c522316b9a010e437d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
ms.locfileid: "31799441"
---
# <a name="designing-extract-load-and-transform-elt-for-azure-sql-data-warehouse"></a>为 Azure SQL 数据仓库设计提取、加载和转换 (ELT)

设计用于将数据加载到 Azure SQL 数据仓库的提取、加载和转换 (ELT) 过程而非提取、转换和加载 (ETL) 过程。 本文介绍了将数据移动到 Azure 数据仓库的 ELT 过程的设计方法。

## <a name="what-is-elt"></a>什么是 ELT？

提取、加载和转换 (ELT) 是将数据从源系统移到目标数据仓库的过程。 此过程定期执行（例如每小时或每天），以将新生成的数据移入数据仓库。 将数据从源移入数据仓库的理想方式是开发一个使用 PolyBase 将数据载入 SQL 数据仓库的 ELT 过程。

ELT 是先加载数据，然后转换数据，而提取、转换和加载 (ETL) 是先转换数据，然后加载数据。 执行 ELT 而不是 ETL 可以节省成本，因为在加载数据之前，无需提供自身的资源用于转换数据。 使用 SQL 数据仓库时，ELT 利用 MPP 系统执行转换。

尽管为 SQL 数据仓库实现 ELT 存在许多不同的方式，但基本步骤都是：  

1. 将源数据提取到文本文件中。
2. 将数据移入 Azure Blob 存储或 Azure Data Lake Store。
3. 准备要加载的数据。
2. 使用 PolyBase 将数据载入 SQL 数据仓库临时表。
3. 转换数据。
4. 将数据插入生产表。


有关加载教程，请参阅[使用 PolyBase 将数据从 Azure Blob 存储加载到 Azure SQL 数据仓库](load-data-from-azure-blob-storage-using-polybase.md)。

有关详细信息，请参阅[加载模式博客](http://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/)。 

## <a name="options-for-loading-with-polybase"></a>使用 PolyBase 加载数据的选项

PolyBase 技术可以通过 T-SQL 语言访问数据库外部的数据。 它是将数据载入 SQL 数据仓库的最佳方法。 使用 PolyBase，可将数据从数据源并行直接载入计算节点。 

若要使用 PolyBase 加载数据，可以使用下列任一加载选项。

- 如果数据位于 Azure Blob 存储或 Azure Data Lake Store 中，则 [PolyBase 与 T-SQL](load-data-from-azure-blob-storage-using-polybase.md) 可以发挥作用。 使用此方法可以获得加载过程的最大控制度，不过同时需要定义外部数据对象。 其他方法在你将源表映射到目标表时，在幕后定义这些对象。  若要协调 T-SQL 负载，可以使用 Azure 数据工厂、SSIS 或 Azure Functions。 
- 如果源数据位于本地 SQL Server 或云中的 SQL Server，则 [PolyBase 与 SSIS](/sql/integration-services/load-data-to-sql-data-warehouse) 可以发挥作用。 SSIS 定义源到目标表的映射，同时可协调负载。 如果已有 SSIS 包，可将这些包修改为使用新的数据仓库目标。 
- [PolyBase 与 Azure 数据工厂 (ADF)](sql-data-warehouse-load-with-data-factory.md) 是另一个业务流程工具。  它定义管道并计划作业。 
- [将 PolyBase 与 Azure DataBricks 配合使用](../azure-databricks/databricks-extract-load-sql-data-warehouse.md)，可将数据从 SQL 数据仓库表传输到 Databricks 数据帧，并且/或者可将 Databricks 数据帧中的数据写入 SQL 数据仓库表。

### <a name="polybase-external-file-formats"></a>PolyBase 外部文件格式

PolyBase 从 UTF-8 和 UTF-16 编码的带分隔符文本文件加载数据。 除了带分隔符的文本文件以外，它还可以从 Hadoop 文件格式、RC 文件、ORC 和 Parquet 加载数据。 PolyBase 可以从 Gzip 和 Snappy 压缩文件加载数据。 PolyBase 目前不支持扩展的 ASCII、固定宽度格式以及 WinZip、JSON 和 XML 等嵌套格式。

### <a name="non-polybase-loading-options"></a>非 PolyBase 加载选项
如果数据与 PolyBase 不兼容，可以使用 [bcp](/sql/tools/bcp-utility) 或 [SQLBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx)。 bcp 将数据直接加载到 SQL 数据仓库，而无需经过 Azure Blob 存储，但只适用于小规模的加载。 请注意，这些选项的加载性能明显低于 PolyBase。 


## <a name="extract-source-data"></a>提取源数据

从源系统中取出数据的过程取决于源。  目标是将数据移入带分隔符的文本文件。 如果使用的是 SQL Server，则可以使用 [bcp 命令行工具](/sql/tools/bcp-utility)导出数据。  

## <a name="land-data-to-azure-storage"></a>将数据移入 Azure 存储

若要将数据移入 Azure 存储，可将它移到 [Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)或 [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)。 应将数据存储在任一位置的文本文件中。 Polybase 可从任一位置加载数据。

可使用以下工具和服务将数据移到 Azure 存储。

- [Azure ExpressRoute](../expressroute/expressroute-introduction.md) 服务可以增强网络吞吐量、性能和可预测性。 ExpressRoute 是通过专用连接将数据路由到 Azure 的服务。 ExpressRoute 连接不通过公共 Internet 路由数据。 与基于公共 Internet 的典型连接相比，这些连接提供更高的可靠性、更快的速度、更低的延迟和更高的安全性。
- [AZCopy 实用工具](../storage/common/storage-moving-data.md)可以通过公共 Internet 将数据移到 Azure 存储。 如果数据小于 10 TB，则很适合使用此工具。 若要使用 AZCopy 定期执行加载操作，请测试网络速度是否在可接受的范围内。 
- [Azure 数据工厂 (ADF)](../data-factory/introduction.md) 提供一个可以安装在本地服务器上的网关。 然后，你可以创建管道，以便将数据从本地服务器移到 Azure 存储。 若要将数据工厂与 SQL 数据仓库配合使用，请参阅[将数据加载到 SQL 数据仓库中](/azure/data-factory/load-azure-sql-data-warehouse)。

## <a name="prepare-data"></a>准备数据

在将存储帐户中的数据载入 SQL 数据仓库之前，可能需要对其进行准备和清理。 可以在数据仍保留在源中、将数据导出到文本文件时或者在数据进入 Azure 存储之后执行数据准备。  最好是在加载过程的早期阶段处理数据。  

### <a name="define-external-tables"></a>定义外部表
在加载数据之前，需要在数据仓库中定义外部表。 PolyBase 使用外部表来定义和访问 Azure 存储中的数据。 外部表类似于常规表。 主要区别在于，外部表指向数据仓库外部存储的数据。 

定义外部表涉及到指定数据源、文本文件的格式和表定义。 下面是需要的 T-SQL 语法主题：
- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql)（创建外部数据源）
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [创建外部表](/sql/t-sql/statements/create-external-table-transact-sql)

有关创建外部对象的示例，请参阅加载教程中的[创建外部表](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data)步骤。

### <a name="format-text-files"></a>设置文本文件的格式

定义外部对象后，需要使文本文件中的行与外部表和文件格式定义相符。 文本文件的每一行中的数据必须与表定义相符。

设置文本文件的格式：

- 如果数据来自非关系源，则需要将其转换为行与列。 不管数据来自关系源还是非关系源，都必须转换数据，使之与数据预期要载入到的表的列定义相符。 
- 设置本文件中数据的格式，使之与 SQL 数据仓库目标表中的列和数据类型相符。 外部文本文件与数据仓库表中的数据类型不相符会导致在加载期间拒绝行。
- 使用终止符分隔文本文件中的字段。  请务必使用源数据中不包含的字符或字符序列。 使用通过 [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql) 指定的终止符。

## <a name="load-to-a-staging-table"></a>加载到临时表
若要将数据提取到数据仓库，最好是先将数据载入临时表。 使用临时表可以处理错误且不干扰生产表，同时可避免针对生产表运行回滚操作。 将数据插入生产表之前，还可以通过临时表使用 SQL 数据仓库来运行转换。

若要使用 T-SQL 执行加载，请运行 [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md) T-SQL 语句。 此命令将 select 语句的结果插入新表。 如果该语句从外部表选择了项，则会导入外部数据。 

在以下示例中，ext.Date 是一个外部表。 所有行将导入名为 dbo.Date 的新表。

```sql
CREATE TABLE [dbo].[Date]
WITH
( 
    CLUSTERED COLUMNSTORE INDEX
)
AS SELECT * FROM [ext].[Date]
;
```

## <a name="transform-the-data"></a>转换数据
在数据已进入临时表时，请执行工作负荷所需的转换。 然后将数据移到生产表。

## <a name="insert-data-into-production-table"></a>将数据插入生产表

INSERT INTO ...SELECT 语句将数据从临时表移到永久表。 

设计 ETL 过程时，请尝试针对一个较小的测试示例运行该过程。 尝试将表中的 1000 行提取到某个文件，将该文件移到 Azure，然后将其载入临时表。 

## <a name="partner-loading-solutions"></a>合作伙伴加载解决方案
我们的很多合作伙伴都提供加载解决方案。 有关详细信息，请参阅我们的[解决方案合作伙伴](sql-data-warehouse-partner-business-intelligence.md)列表。 

## <a name="next-steps"></a>后续步骤
有关加载指南，请参阅[加载数据的指南](guidance-for-loading-data.md)。


