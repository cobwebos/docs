---
title: 从 Azure Data Lake Storage 加载数据的教程
description: 使用 COPY 语句加载 Synapse SQL Azure Data Lake Storage 的数据。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 06/07/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: fcebf66dba2fc13457ca359b81565fc5870032c9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213289"
---
# <a name="load-data-from-azure-data-lake-storage-for-synapse-sql"></a>从适用于 Synapse SQL 的 Azure Data Lake Storage 加载数据

本指南概述了如何使用[COPY 语句](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)从 Azure Data Lake Storage 中加载数据。 有关跨所有身份验证方法使用 COPY 语句的快速示例，请访问以下文档：[使用 SYNAPSE SQL 安全地加载数据](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples)。

> [!NOTE]  
> 若要在 COPY 语句上提供反馈或报告问题，请将电子邮件发送到以下通讯组列表： sqldwcopypreview@service.microsoft.com 。
>
> [!div class="checklist"]
>
> * 创建要从 Azure Data Lake Storage 加载数据的目标表。
> * 创建 COPY 语句，以便将数据加载到数据仓库中。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="before-you-begin"></a>开始之前

开始本教程之前，请下载并安装最新版 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS)。

若要运行本教程，需要：

* SQL 池。 请参阅[创建 SQL 池和查询数据](create-data-warehouse-portal.md)。
* Data Lake Storage 帐户。 请参阅 [Azure Data Lake Storage 入门](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。 对于此存储帐户，你将需要配置或指定以下要加载的凭据之一：存储帐户密钥、共享访问签名（SAS）密钥、Azure 目录应用程序用户或将相应的 RBAC 角色提供给存储帐户的 AAD 用户。

## <a name="create-the-target-table"></a>创建目标表

连接到 SQL 池并创建要加载到的目标表。 在此示例中，我们将创建一个产品维度表。

```sql
-- A: Create the target table
-- DimProduct
CREATE TABLE [dbo].[DimProduct]
(
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    DISTRIBUTION = HASH([ProductKey]),
    CLUSTERED COLUMNSTORE INDEX
    --HEAP
);
```


## <a name="create-the-copy-statement"></a>创建 COPY 语句

连接到 SQL 池并运行 COPY 语句。 有关示例的完整列表，请访问以下文档：[使用 SYNAPSE SQL 安全地加载数据](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples)。

```sql
-- B: Create and execute the COPY statement

COPY INTO [dbo].[DimProduct] 
--The column list allows you map, omit, or reorder input file columns to target table columns. 
--You can also specify the default value when there is a NULL value in the file.
--When the column list is not specified, columns will be mapped based on source and target ordinality
(
    ProductKey default -1 1,
    ProductLabel default 'myStringDefaultWhenNull' 2,
    ProductName default 'myStringDefaultWhenNull' 3
)
--The storage account location where you data is staged
FROM 'https://storageaccount.blob.core.windows.net/container/directory/'
WITH 
(
   --CREDENTIAL: Specifies the authentication method and credential access your storage account
   CREDENTIAL (IDENTITY = '', SECRET = '')
   --FILE_TYPE: Specifies the file type in your storage account location
   FILE_TYPE = 'CSV',
   --FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text (CSV) file
   FIELDTERMINATOR = '|',
   --ROWTERMINATOR: Marks the end of a record in the file
   ROWTERMINATOR = '0x0A',
   --FIELDQUOTE: Specifies the delimiter for data of type string in a delimited text (CSV) file
   FIELDQUOTE = '',
   ENCODING = 'UTF8',
   DATEFORMAT = 'ymd',
   --MAXERRORS: Maximum number of reject rows allowed in the load before the COPY operation is canceled
   MAXERRORS = 10,
   --ERRORFILE: Specifies the directory where the rejected rows and the corresponding error reason should be written
   ERRORFILE = '/errorsfolder',
) OPTION (LABEL = 'COPY: ADLS tutorial');
```

## <a name="optimize-columnstore-compression"></a>优化列存储压缩

默认情况下，表定义为聚集列存储索引。 加载完成后，某些数据行可能未压缩到列存储中。  发生这种情况的原因多种多样。 若要了解详细信息，请参阅[管理列存储索引](sql-data-warehouse-tables-index.md)。

若要在加载后优化查询性能和列存储压缩，请重新生成表，以强制列存储索引压缩所有行。

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>优化统计信息

最好是在加载之后马上创建单列统计信息。 对于统计信息，可以使用多个选项。 例如，如果针对每个列创建单列统计信息，则重新生成所有统计信息可能需要花费很长时间。 如果知道某些列不会在查询谓词中使用，可以不创建有关这些列的统计信息。

如果决定针对每个表的每个列创建单列统计信息，可以使用 [statistics](sql-data-warehouse-tables-statistics.md)（统计信息）一文中的存储过程代码示例 `prc_sqldw_create_stats`。

以下示例是创建统计信息的不错起点。 它会针对维度表中的每个列以及事实表中的每个联接列创建单列统计信息。 以后，随时可以将单列或多列统计信息添加到其他事实表列。

## <a name="achievement-unlocked"></a>大功告成！

已成功地将数据加载到数据仓库中。 干得不错！

## <a name="next-steps"></a>后续步骤
加载数据是使用 Azure Synapse Analytics 开发数据仓库解决方案的第一步。 请查看我们的开发资源。

> [!div class="nextstepaction"]
> [了解如何为数据仓库开发表](sql-data-warehouse-tables-overview.md)

有关加载示例和参考的详细信息，请参阅以下文档：
- [COPY 语句参考文档](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax)
- [复制每个身份验证方法的示例](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples)
- [复制单个表的快速入门](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql)
