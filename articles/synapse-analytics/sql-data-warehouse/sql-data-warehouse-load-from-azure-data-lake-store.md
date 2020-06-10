---
title: 从 Azure Data Lake Storage 加载数据的教程
description: 使用 PolyBase 外部表从适用于 Synapse SQL 的 Azure Data Lake Store 加载数据。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/08/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 5935efca138d156507e2e3fefa65d045f618a57b
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020823"
---
# <a name="load-data-from-azure-data-lake-storage-for-synapse-sql"></a>从适用于 Synapse SQL 的 Azure Data Lake Storage 加载数据

本指南概述如何使用 PolyBase 外部表从 Azure Data Lake Store 加载数据。 虽然可以对存储在 Data Lake Storage 中的数据运行 即席查询，但我们建议导入数据以获取最佳性能。

> [!NOTE]  
> 进行加载的一种替代方法是 [COPY 语句](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)（当前提供公共预览版）。  COPY 语句可提供最大灵活性。 若要提供有关 COPY 语句的反馈，请将电子邮件发送到以下通讯组列表：sqldwcopypreview@service.microsoft.com。
>
> [!div class="checklist"]
>
> * 创建需要从 Data Lake Storage 进行加载的数据库对象。
> * 连接到 Data Lake Storage 目录。
> * 将数据加载到数据仓库中。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="before-you-begin"></a>开始之前

开始本教程之前，请下载并安装最新版 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS)。

若要运行本教程，需要：

* SQL 池。 请参阅[创建 SQL 池和查询数据](create-data-warehouse-portal.md)。
* Data Lake Storage 帐户。 请参阅 [Azure Data Lake Storage 入门](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。 对于此存储帐户，需要配置或指定以下凭据之一以进行加载：存储帐户密钥、Azure Directory 应用程序用户或是具有存储帐户的相应 RBAC 角色的 AAD 用户。

## <a name="create-a-credential"></a>创建凭据

使用 AAD 直通进行身份验证时，可以跳过此部分并继续“创建外部数据源”。 使用 AAD 直通时，无需创建或指定数据库范围凭据，但需确保 AAD 用户具有存储帐户的相应 RBAC 角色（存储 Blob 数据读取者、参与者或所有者角色）。 [此处](https://techcommunity.microsoft.com/t5/Azure-SQL-Data-Warehouse/How-to-use-PolyBase-by-authenticating-via-AAD-pass-through/ba-p/862260)概述了更多详细信息。

若要访问 Data Lake Storage 帐户，需要创建数据库主密钥，以便加密凭据密码。 随后创建数据库范围凭据以存储机密。 使用服务主体（Azure Directory 应用程序用户）进行身份验证时，数据库范围凭据会存储 AAD 中设置的服务主体凭据。 对于 Gen2，还可以使用数据库范围凭据存储的存储帐户密钥。

若要使用服务主体连接到 Data Lake Storage，必须先创建 Azure Active Directory 应用程序，创建访问密钥，并授予应用程序访问 Data Lake Storage 帐户的权限。 有关说明，请参阅[使用 Active Directory 验证 Azure Data Lake Storage](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。

使用具有 CONTROL 级别权限的用户登录 SQL 池中，并对数据库执行以下 SQL 语句：

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest

CREATE MASTER KEY;


-- B (for service principal authentication): Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    -- Always use the OAuth 2.0 authorization endpoint (v1)
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- B (for Gen2 storage key authentication): Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;

-- It should look something like this when authenticating using service principal:
CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>创建外部数据源

使用此 [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 命令存储数据的位置。 如果使用 AAD 直通进行身份验证，则不需要 CREDENTIAL 参数。 如果对服务终结点使用托管标识进行身份验证，请按照此[文档](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#azure-synapse-polybase)设置外部数据源。

```sql
-- C (for Gen1): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen1 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<datalakestoregen1accountname>.azuredatalakestore.net',
    CREDENTIAL = ADLSCredential
);

-- C (for Gen2): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen2 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION='abfs[s]://<container>@<AzureDataLake account_name>.dfs.core.windows.net', -- Please note the abfss endpoint for when your account has secure transfer enabled
    CREDENTIAL = ADLSCredential
);
```

## <a name="configure-data-format"></a>配置数据格式

若要从 Data Lake Storage 导入数据，需要指定外部文件格式。 此对象定义在 Data Lake Storage 中写入文件的方式。
有关完整列表，请查看我们的 T-SQL 文档 [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)（创建外部文件格式）

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store missing values as default for datatype.

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

指定数据源和文件格式后，可以开始创建外部表。 外部表是你与外部数据交互的方式。 位置参数可以指定文件或目录。 如果指定目录，将会加载该目录中的所有文件。

```sql
-- D: Create an External Table
-- LOCATION: Folder under the Data Lake Storage root folder.
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
,   DATA_SOURCE = AzureDataLakeStorage
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>外部表注意事项

创建外部表很容易，但有一些细微差别，需要进行讨论。

外部表经过强类型化。 这意味着所引入的每行数据必须满足表架构定义。
如果某个行不符合架构定义，则该行会被拒绝加载。

使用 REJECT_TYPE 和 REJECT_VALUE 可以定义在最终表中必须存在的数据行数或数据的百分比。 在加载过程中，如果达到拒绝值，则加载会失败。 行被拒绝的最常见原因是架构定义不匹配。 例如，当文件中的数据是字符串时，如果错误地为列指定了 int 的架构，则每一行都将无法加载。

Data Lake Storage Gen1 使用基于角色的访问控制 (RBAC) 控制对数据的访问。 也就是说，服务主体必须拥有对位置参数中定义的目录以及最终目录和文件的子项的读取权限。 这样一来，PolyBase 就可以进行身份验证，并加载该数据。

## <a name="load-the-data"></a>加载数据

若要从 Data Lake Storage 加载数据，请使用 [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语句。

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

在本教程中，创建了外部表以定义 Data Lake Storage Gen1 中存储的数据的结构，然后使用了 PolyBase CREATE TABLE AS SELECT 语句将数据加载到数据仓库。

完成了以下操作：
> [!div class="checklist"]
>
> * 创建需要从 Data Lake Storage 加载的数据库对象。
> * 连接到 Data Lake Storage 目录。
> * 将数据加载到数据仓库中。
>

加载数据是使用 Azure Synapse Analytics 开发数据仓库解决方案的第一步。 请查看我们的开发资源。

> [!div class="nextstepaction"]
> [了解如何为数据仓库开发表](sql-data-warehouse-tables-overview.md)
