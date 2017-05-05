---
title: "加载 - Azure Data Lake Store 到 SQL 数据仓库 | Microsoft 文档"
description: "了解如何使用 PolyBase 外部表将数据从 Azure Data Lake Store 加载到 Azure SQL 数据仓库中。"
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 01/25/2017
ms.author: cakarst;barbkess
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: aca0e4cfdcfb3e3ed2e69ad8153b4c965b299806
ms.lasthandoff: 03/15/2017



---
# <a name="load-data-from-azure-data-lake-store-into-sql-data-warehouse"></a>将数据从 Azure Data Lake Store 加载到 SQL 数据仓库中
本文档提供了使用 PolyBase 从 Azure Data Lake Store (ADLS) 将自己的数据加载到 SQL 数据仓库中所需的所有步骤。
虽然能够使用外部表对 ADLS 中存储的数据运行即席查询，但最佳做法是，建议将数据导入 SQL 数据仓库。
估计时间：10 分钟，假定你具备需要完成的先决条件。
>
在本教程中，你将了解如何：

1. 创建要从 Azure Data Lake Store 加载的外部数据库对象。
2. 连接到 Azure Data Lake Store 目录。
3. 将数据载入 Azure SQL 数据仓库。

## <a name="before-you-begin"></a>开始之前
若要运行本教程，需要：

* 要用于服务到服务身份验证的 Azure Active Directory 应用程序。 若要创建，请遵循 [Active directory 身份验证](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)

>[!NOTE] 
> 要从 SQL 数据仓库连接到 Azure Data Lake，需要 Active Directory 应用程序的客户端 ID、密钥和 OAuth2.0 令牌终结点值。 有关如何获取这些值的详细信息可在上面的链接中找到。

* SQL Server Management Studio 或 SQL Server Data Tools，若要下载 SSMS 并进行连接，请参阅[查询 SSMS](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-query-ssms)

* Azure SQL 数据仓库，若要创建一个，请遵循：https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-provision

* 未启用加密的 Azure Data Lake Store。 若要创建一个，请遵循：https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal




## <a name="configure-the-data-source"></a>配置数据源
PolyBase 使用 T-SQL 外部对象来定义外部数据的位置和属性。 外部对象存储在 SQL 数据仓库中，并引用外部存储的数据。


###  <a name="create-a-credential"></a>创建凭据
若要访问 Azure Data Lake Store，需要创建数据库主密钥，以便加密下一步中使用的凭据密码。
然后创建数据库范围的凭据，以存储 AAD 中设置的服务主体凭据。 已使用 PolyBase 连接到 Windows Azure 存储 Blob 的用户请注意，该凭据语法有所不同。
若要连接到 Azure Data Lake Store，必须**先**创建 Azure Active Directory 应用程序，创建访问密钥，并授予应用程序访问 Azure Data Lake 资源的权限。 执行这些步骤的说明位于[此处](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)。

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/en-us/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/en-us/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

```


### <a name="create-the-external-data-source"></a>创建外部数据源
使用此 [CREATE EXTERNAL DATA SOURCE][CREATE EXTERNAL DATA SOURCE] 命令存储数据的位置以及数据的类型。
可以在 Azure 门户和 www.portal.azure.com 中找到 ADL URI。

```sql
-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Store.
-- LOCATION: Provide Azure Data Lake accountname and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<AzureDataLake account_name>.azuredatalakestore.net',
    CREDENTIAL = ADLCredential
);
```



## <a name="configure-data-format"></a>配置数据格式
若要从 ADLS 导入数据，需要指定外部文件格式。 此命令具有格式特定的选项，用于描述数据。
下面是一个常用的文件格式（竖线分隔的文本文件）的示例。
请查看我们的 T-SQL 文档，获取 [CREATE EXTERNAL FILE FORMAT][CREATE EXTERNAL FILE FORMAT] 的完整列表

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store all Missing values as NULL

CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-external-tables"></a>创建外部表
指定数据源和文件格式后，可以开始创建外部表。 外部表是你与外部数据交互的方式。 PolyBase 使用递归目录遍历读取位置参数中指定的目录的所有子目录中的所有文件。 此外，以下示例说明了如何创建对象。 需要自定义语句以便处理 ADLS 中的数据。

```sql
-- D: Create an External Table
-- LOCATION: Folder under the ADLS root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStore
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>外部表注意事项
创建外部表很容易，但有一些细微差别，需要进行讨论。

使用 PolyBase 加载数据时将进行强类型化。 这意味着所引入的每行数据必须满足表架构定义。
如果某个给定行不符合架构定义，则该行将被拒绝加载。

使用 Reject Type 和 Reject Value 可以定义在最终表中必须存在的数据行数或数据的百分比。
在加载过程中，如果达到拒绝值，则加载将失败。 行被拒绝的最常见原因是架构定义不匹配。
例如，当文件中的数据是字符串时，如果错误地为列指定了 int 的架构，则每一行都将无法加载。

Location 指定要从中读取数据的最顶层目录。
在此示例中，如果 /DimProduct/ 下有子目录，PolyBase 将导入这些子目录中的所有数据。

## <a name="load-the-data"></a>加载数据
若要从 Azure Data Lake Store 加载数据，请使用 [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] 语句。 使用 CTAS 加载时将使用已创建的强类型化外部表。

CTAS 将创建新表，并在该表中填充 select 语句的结果。 CTAS 将新表定义为包含与 select 语句结果相同的列和数据类型。 如果选择了外部表中的所有列，则新表将是外部表中的列和数据类型的副本。

在此示例中，我们将从外部表 DimProduct_external 创建名为 DimProduct 的哈希分布表。

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>优化列存储压缩
默认情况下，SQL 数据仓库将表存储为聚集列存储索引。 加载完成后，某些数据行可能未压缩到列存储中。  发生这种情况的原因多种多样。 若要了解详细信息，请参阅[管理列存储索引][manage columnstore indexes]。

若要在加载后优化查询性能和列存储压缩，请重新生成表，以强制列存储索引压缩所有行。

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

有关维护列存储索引的详细信息，请参阅[管理列存储索引][manage columnstore indexes]一文。

## <a name="optimize-statistics"></a>优化统计信息
最好是在加载之后马上创建单列统计信息。 对于统计信息，可以使用多个选项。 例如，如果针对每个列创建单列统计信息，则重新生成所有统计信息可能需要花费很长时间。 如果你知道某些列不会在查询谓词中使用，可以不创建有关这些列的统计信息。

如果决定针对每个表的每个列创建单列统计信息，可以使用 [统计信息][statistics]一文中的存储过程代码示例 `prc_sqldw_create_stats`。

以下示例是创建统计信息的不错起点。 它会针对维度表中的每个列以及事实表中的每个联接列创建单列统计信息。 以后，你随时可以将单列或多列统计信息添加到其他事实表列。


## <a name="achievement-unlocked"></a>大功告成！
你已成功地将数据载入 Azure SQL 数据仓库。 干得不错！

##<a name="next-steps"></a>后续步骤
加载数据是使用 SQL 数据仓库开发数据仓库解决方案的第一步。 请在[表](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-overview)和 [T-SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-loops.md) 中查看我们的开发资源。


<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[manage columnstore indexes]: sql-data-warehouse-tables-index.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CREATE EXTERNAL DATA SOURCE]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Load the full Contoso Retail Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md

