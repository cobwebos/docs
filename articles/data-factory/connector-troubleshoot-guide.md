---
title: 排查 Azure 数据工厂连接器问题
description: 了解如何排查 Azure 数据工厂中的连接器问题。
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/09/2020
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: 50f76d9b4f3061e6e9a1e4a0b510146dbded422a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199012"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>排查 Azure 数据工厂连接器问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探讨 Azure 数据工厂中的连接器的常用故障排除方法。
  

## <a name="azure-blob-storage"></a>Azure Blob 存储

### <a name="error-code--azurebloboperationfailed"></a>错误代码：AzureBlobOperationFailed

- **消息**：`Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **原因：** Blob 存储操作遇到问题。

- **建议**：检查详细信息中的错误。 参阅 Blob 帮助文档： https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes 。 如果需要帮助，请联系存储团队。


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>错误代码：AzureBlobServiceNotReturnExpectedDataLength

- **消息**：`Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>错误代码：AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **消息**：`Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>错误代码：AzureStorageOperationFailedConcurrentWrite

- **消息**：`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>错误消息：请求过大

- **症状**：将数据复制到使用默认写入批大小的 Azure Cosmos DB 时，遇到错误“请求过大”。 **

- **原因：** Cosmos DB 将单个请求的大小限制为 2 MB。 公式为请求大小 = 单个文档大小 * 写入批大小。 如果文档过大，默认行为会导致请求过大。 可以优化写入批大小。

- **解决方法**：在复制活动接收器中，减小“写入批大小”值（默认值为 10000）。

### <a name="error-message-unique-index-constraint-violation"></a>错误消息：唯一索引约束冲突

- **症状**：将数据复制到 Cosmos DB 时遇到以下错误：

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **原因：** 有两个可能的原因：

    - 如果使用“插入”作为写入行为，则此错误表示源数据包含具有相同 ID 的行/对象。 

    - 如果使用“更新插入”作为写入行为，并设置了容器的另一个唯一键，则此错误表示源数据中的行/对象使用了定义的唯一键的不同 ID，但使用了该键的相同值。 

- **解决方法**： 

    - 对于原因 1，请将“更新插入”设置为写入行为。 
    - 对于原因 2，请确保每个文档使用定义的唯一键的不同值。

### <a name="error-message-request-rate-is-large"></a>错误消息：请求速率太大

- **症状**：将数据复制到 Cosmos DB 时遇到以下错误：

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **原因：** 使用的请求单位大于 Cosmos DB 中配置的可用 RU。 在[此处](../cosmos-db/request-units.md#request-unit-considerations)了解 Cosmos DB 如何计算 RU。

- **解决方法**：下面是两种解决方法：

    1. **增加容器 RU**，使之大于 Cosmos DB 中的值，这可以提高复制活动的性能，不过会增大 Cosmos DB 的费用。 

    2. 将 **writeBatchSize** 减至更小的值（例如 1000），并将 **parallelCopies** 设置为更小的值（例如 1），这会导致复制运行性能比当前更糟，但不会增大 Cosmos DB 的费用。

### <a name="column-missing-in-column-mapping"></a>列映射中缺少列

- **症状**：导入用于列映射的 Cosmos DB 的架构时缺少某些列。 

- **原因：** ADF 从前 10 个 Cosmos DB 文档推断架构。 如果某些列/属性在这些文档中没有值，则它们不会被 ADF 检测到，因此也就不会显示。

- **解决方法**：可按如下所示优化查询，以强制在结果集中显示带有空值的列：（假设前 10 个文档中缺少“impossible”列）。 或者，可以手动添加要映射的列。

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>错误消息：读取器的 GuidRepresentation 为 CSharpLegacy

- **症状**：从包含 UUID 字段的 Cosmos DB MongoAPI/MongoDB 复制数据时遇到以下错误：

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **原因：** 可通过两种方式表示 BSON 中的 UUID - UuidStardard 和 UuidLegacy。 默认使用 UuidLegacy 来读取数据。 如果 MongoDB 中的 UUID 数据是 UuidStandard，则会出现错误。

- **解决方法**：在 MongoDB 连接字符串中，添加选项 "**uuidRepresentation=standard**"。 有关详细信息，请参阅 [MongoDB 连接字符串](connector-mongodb.md#linked-service-properties)。
            

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code--adlsgen2operationfailed"></a>错误代码：AdlsGen2OperationFailed

- **消息**：`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **原因：** ADLS Gen2 引发了指明操作失败的错误。

- **建议**：检查 ADLS Gen2 引发的详细错误消息。 如果是由暂时性失败导致的，请重试。 如果需要进一步的帮助，请联系 Azure 存储支持，并提供错误消息中的请求 ID。

- **原因：** 如果错误消息包含“被禁止”，则你使用的服务主体或托管标识可能没有足够的权限来访问 ADLS Gen2。

- **建议**：参阅帮助文档： https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication 。

- **原因：** 当错误消息包含“InternalServerError”时，错误是由 ADLS Gen2 返回的。

- **建议**：这可能是由暂时性失败导致的，请重试。 如果问题持续存在，请联系 Azure 存储支持，并提供错误消息中的请求 ID。


### <a name="error-code--adlsgen2invalidurl"></a>错误代码：AdlsGen2InvalidUrl

- **消息**：`Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>错误代码：AdlsGen2InvalidFolderPath

- **消息**：`The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>错误代码：AdlsGen2OperationFailedConcurrentWrite

- **消息**：`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code--adlsgen2timeouterror"></a>错误代码：AdlsGen2TimeoutError

- **消息**：`Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>错误消息：远程服务器返回了一个错误：（403）禁止访问

- **症状**：复制活动失败，出现以下错误： 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **原因**：一个可能的原因是你使用的服务主体或托管标识无权访问某些文件夹/文件。

- **解决方法**：授予需要复制的所有文件夹和子文件夹的相应权限。 请参阅[此文档](connector-azure-data-lake-store.md#linked-service-properties)。

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>错误消息：无法使用服务主体获取访问令牌。 ADAL 错误： service_unavailable

- **症状**：复制活动失败，出现以下错误：

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **原因**：如果 Azure Active Directory 拥有的服务令牌服务器（STS）不可用，例如，太忙而无法处理请求，则返回 HTTP 错误503。 

- **解决方法**：在几分钟后重新运行复制活动。
                  

## <a name="azure-sql-data-warehouseazure-sql-databasesql-server"></a>Azure SQL 数据仓库/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>错误代码： SqlFailedToConnect

- **消息**：`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **原因**：如果错误消息包含 "SqlException"，SQL 数据库将引发错误，指示某些特定操作失败。

- **建议**：请按照此参考文档中的 SQL 错误代码进行搜索，以了解更多详细信息： https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors 。 如需进一步的帮助，请联系 Azure SQL 支持。

- **原因**：如果错误消息包含 "具有 IP 地址的客户端 ..."不允许访问服务器 "，并且你正在尝试连接到 Azure SQL 数据库，这通常是由 Azure SQL 数据库防火墙问题导致的。

- **建议**：在 Azure SQL Server 防火墙配置中，启用 "允许 Azure 服务和资源访问此服务器" 选项。 参考文档：https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure。


### <a name="error-code--sqloperationfailed"></a>错误代码： SqlOperationFailed

- **消息**：`A database operation failed. Please search error to get more details.`

- **原因**：如果错误消息包含 "SqlException"，SQL 数据库将引发错误，指示某些特定操作失败。

- **建议**：如果不清楚 SQL 错误，请尝试将数据库更改为最新兼容级别 "150"。 它可能会引发最新版本的 SQL 错误。 请参阅详细信息文档： https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#backwardCompat 。
        若要解决 SQL 问题，请按此参考文档中的 SQL 错误代码进行搜索，以了解更多详细信息： https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors 。 如果需要更多帮助，请联系 Azure SQL 支持。

- **原因**：如果错误消息包含 "PdwManagedToNativeInteropException"，则通常是由于 source 和 sink 列大小不匹配造成的。

- **建议**：检查源列和接收器列的大小。 如果需要更多帮助，请联系 Azure SQL 支持。

- **原因**：如果错误消息包含 "InvalidOperationException"，通常是由于输入数据无效导致的。

- **建议**：若要确定哪个行遇到了问题，请在复制活动上启用容错功能，该功能可将有问题的行重定向到存储，以便进一步调查。 参考文档：https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance。


### <a name="error-code--sqlunauthorizedaccess"></a>错误代码： SqlUnauthorizedAccess

- **消息**：`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **原因**：凭据不正确，或登录帐户无法访问 SQL 数据库。

- **建议**：检查登录帐户是否有足够的权限访问 SQL 数据库。


### <a name="error-code--sqlopenconnectiontimeout"></a>错误代码： SqlOpenConnectionTimeout

- **消息**：`Open connection to database timeout after '%timeoutValue;' seconds.`

- **原因**： SQL 数据库暂时性失败。

- **建议**：请重试以更新连接超时值较大的链接服务连接字符串。


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>错误代码： SqlAutoCreateTableTypeMapFailed

- **消息**：`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **原因**：自动创建表无法满足源要求。

- **建议**：更新 "映射" 中的列类型，或在目标服务器中手动创建接收器表。


### <a name="error-code--sqldatatypenotsupported"></a>错误代码： SqlDataTypeNotSupported

- **消息**：`A database operation failed. Check the SQL errors.`

- **原因**：如果在 SQL 源上出现问题，并且错误与 SqlDateTime 溢出有关，则数据值超过逻辑类型范围（1/1/1753 12:00:00 AM-12/31/9999 11:59:59 PM）。

- **建议**：将类型强制转换为源 SQL 查询中的字符串，或复制活动列映射中的类型将列类型更改为 "string"。

- **原因**：如果在 SQL 接收器上出现问题，并且错误与 SqlDateTime 溢出相关，则数据值将超出接收器表中允许的范围。

- **建议**：在接收器表中将相应的列类型更新为 "datetime2" 类型。


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>错误代码： SqlInvalidDbStoredProcedure

- **消息**：`The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **原因**：指定的存储过程无效。 这可能是因为存储过程不返回任何数据。

- **建议**：通过 SQL 工具验证存储过程。 请确保存储过程可以返回数据。


### <a name="error-code--sqlinvaliddbquerystring"></a>错误代码： SqlInvalidDbQueryString

- **消息**：`The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **原因**：指定的 SQL 查询无效。 这可能是因为查询不返回任何数据。

- **建议**：通过 sql 工具验证 sql 查询。 确保查询可以返回数据。


### <a name="error-code--sqlinvalidcolumnname"></a>错误代码： SqlInvalidColumnName

- **消息**：`Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **原因**：找不到列。 可能存在配置错误。

- **建议**：验证查询中的列、数据集中的 "structure" 和活动中的 "映射"。


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>错误代码： SqlColumnNameMismatchByCaseSensitive

- **消息**：`Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>错误代码： SqlBatchWriteTimeout

- **消息**：`Timeouts in SQL write operation.`

- **原因**： SQL 数据库暂时性失败。

- **建议**：请重试。 如果问题重现，请联系 Azure SQL 支持。


### <a name="error-code--sqlbatchwritetransactionfailed"></a>错误代码： SqlBatchWriteTransactionFailed

- **消息**：`SQL transaction commits failed`

- **原因**：如果异常详细信息持续通知事务超时，则集成运行时和数据库之间的网络延迟高于默认阈值（30秒）。

- **建议**：将 "连接超时" 值等于120或更高版本的 Sql 链接服务连接字符串更新，然后重新运行该活动。

- **原因**：如果异常详细信息间歇性地告诉 sqlconnection 出现损坏，则可能是暂时性的网络故障或 SQL 数据库端问题

- **建议**：请重试活动，并查看 SQL 数据库端指标。


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>错误代码： SqlBulkCopyInvalidColumnLength

- **消息**：`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **原因**： SQL 大容量复制失败，因为从 bcp 客户端收到的列长度无效。

- **建议**：若要确定哪个行遇到了问题，请在复制活动上启用容错功能，该功能可将有问题的行重定向到存储，以便进一步调查。 参考文档：https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance。


### <a name="error-code--sqlconnectionisclosed"></a>错误代码： SqlConnectionIsClosed

- **消息**：`The connection is closed by SQL Database.`

- **原因**： sql 数据库连接在高并发运行和服务器终止连接时关闭。

- **建议**：远程服务器已关闭 SQL 连接。 请重试。 如果问题重现，请联系 Azure SQL 支持。


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>错误代码： SqlCreateTableFailedUnsupportedType

- **消息**：`Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>错误消息：从字符串转换为 uniqueidentifier 时转换失败

- **症状**：使用暂存复制和 PolyBase 将数据从表格数据源（例如 SQL Server）复制到 Azure SQL 数据仓库时，遇到以下错误：

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **原因**： Azure SQL 数据仓库 PolyBase 无法将空字符串转换为 GUID。

- **解决方法**：在复制活动接收器的 "Polybase 设置" 下，将 "**使用类型默认值**" 选项设置为 false。

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>错误消息：预期的数据类型： DECIMAL （x，x），有问题的值

- **症状**：使用暂存复制和 PolyBase 将数据从表格数据源（例如 SQL Server）复制到 SQL DW 时，遇到以下错误：

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **原因**： Azure SQL 数据仓库 Polybase 不能将空字符串（null 值）插入 decimal 列中。

- **解决方法**：在复制活动接收器的 "Polybase 设置" 下，将 "**使用类型默认值**" 选项设置为 false。

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>错误消息： Java 异常消息： HdfsBridge：： CreateRecordReader

- **症状**：使用 PolyBase 将数据复制到 Azure SQL 数据仓库，并遇到以下错误：

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **原因**：可能的原因是架构（总列宽宽度）太大（大于 1 MB）。 通过添加所有列的大小来检查目标 SQL 数据仓库表的架构：

    - Int -> 4 字节
    - Bigint -> 8 字节
    - Varchar(n),char(n),binary(n), varbinary(n) -> n 字节
    - Nvarchar(n), nchar(n) -> n*2 字节
    - Date -> 6 字节
    - Datetime/(2), smalldatetime -> 16 字节
    - Datetimeoffset -> 20 字节
    - Decimal -> 19 字节
    - Float -> 8 字节
    - Money -> 8 字节
    - Smallmoney -> 4 字节
    - Real -> 4 字节
    - Smallint -> 2 字节
    - Time -> 12 字节
    - Tinyint -> 1 字节

- **解决方法**：将列宽缩小为小于 1 MB

- 或者，通过禁用 Polybase 来使用批量插入方法

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>错误消息：不满足使用 HTTP 条件标头指定的条件

- **症状**：使用 SQL 查询从 Azure SQL 数据仓库拉取数据，并遇到以下错误：

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **原因**： Azure SQL 数据仓库在 azure 存储中查询外部表时遇到问题。

- **解决方法**：在 SSMS 中运行相同的查询，并检查是否看到相同的结果。 如果出现同一结果，请开具一个 Azure SQL 数据仓库的支持票证并提供你的 SQL DW 服务器和数据库名称，方便我们进一步排查问题。
            

## <a name="delimited-text-format"></a>带分隔符的文本格式

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>错误代码： DelimitedTextColumnNameNotAllowNull

- **消息**：`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **原因**：在活动中设置 "firstRowAsHeader" 时，将使用第一行作为列名称。 此错误表示第一行包含空值。 例如： "ColumnA，，ColumnB"。

- **建议**：检查第一行，如果有空值，则修复此值。


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>错误代码： DelimitedTextMoreColumnsThanDefined

- **消息**：`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **原因**：有问题的行的列计数大于第一行的列计数。 这可能是由于数据问题或列分隔符/引号字符设置错误引起的。

- **建议**：请获取错误消息中的行计数，检查行的列并修复数据。

- **原因**：如果所需的列计数在错误消息中为 "1"，则可能是指定了错误的压缩或格式设置，这会导致 ADF 错误地分析文件。

- **建议**：检查格式设置，确保它与源文件匹配。

- **原因**：如果源是文件夹，则可能是指定文件夹下的文件具有不同的架构。

- **建议**：请确保给定文件夹下的文件具有相同的架构。


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>错误代码： DelimitedTextIncorrectRowDelimiter

- **消息**：`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>错误代码： DelimitedTextTooLargeColumnCount

- **消息**：`Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>错误代码： DelimitedTextInvalidSettings

- **消息**：`%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>错误代码： DynamicsCreateServiceClientError

- **消息**：`This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **原因**：这是 dynamics server 端的暂时性问题。

- **建议**：重新运行管道。 如果仍然失败，请尝试降低并行度。 如果仍然失败，请联系 dynamics 支持部门。



## <a name="json-format"></a>JSON 格式

### <a name="error-code--jsoninvalidarraypathdefinition"></a>错误代码： JsonInvalidArrayPathDefinition

- **消息**：`Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>错误代码： JsonEmptyJObjectData

- **消息**：`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>错误代码： JsonNullValueInPathDefinition

- **消息**：`Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>错误代码： JsonUnsupportedHierarchicalComplexValue

- **消息**：`The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>错误代码： JsonConflictPartitionDiscoverySchema

- **消息**：`Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>错误代码： JsonInvalidDataFormat

- **消息**：`Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>错误代码： JsonInvalidDataMixedArrayAndObject

- **消息**：`Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`



## <a name="parquet-format"></a>Parquet 格式

### <a name="error-code--parquetjavainvocationexception"></a>错误代码： ParquetJavaInvocationException

- **消息**：`An error occurred when invoking java, message: %javaException;.`

- **原因**：如果错误消息包含 "OutOfMemory"、"java 堆空间" 和 "doubleCapacity"，通常是早期版本的 integration runtime 中的内存管理问题。

- **建议**：如果使用自承载 Integration Runtime 并且版本早于3.20.7159.1，则建议升级到最新版本。

- **原因**：当错误消息包含 "OutOfMemory" 时，集成运行时没有足够的资源来处理文件。

- **建议**：限制集成运行时上的并发运行。 对于自承载 Integration Runtime，增加到内存等于或大于 8 GB 的强大计算机。

- **原因**：如果错误消息包含 "NullPointerReference"，则可能是暂时性错误。

- **建议**：请重试。 如果问题仍然存在，请与支持人员联系。


### <a name="error-code--parquetinvalidfile"></a>错误代码： ParquetInvalidFile

- **消息**：`File is not a valid parquet file.`

- **原因**： Parquet 文件问题。

- **建议**：检查输入是否为有效的 parquet 文件。


### <a name="error-code--parquetnotsupportedtype"></a>错误代码： ParquetNotSupportedType

- **消息**：`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **原因**： Azure 数据工厂不支持 parquet 格式。

- **建议**：仔细检查源数据。 请参阅文档： https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs 。


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>错误代码： ParquetMissedDecimalPrecisionScale

- **消息**：`Decimal Precision or Scale information is not found in schema for column: %column;.`

- **原因**：尝试解析数字的精度和小数位数，但不提供此类信息。

- **建议**： "Source" 不会返回正确的精度和小数位数。 检查问题列的精度和小数位数。


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>错误代码： ParquetInvalidDecimalPrecisionScale

- **消息**：`Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **原因**：架构无效。

- **建议**：检查问题列的精度和小数位数。


### <a name="error-code--parquetcolumnnotfound"></a>错误代码： ParquetColumnNotFound

- **消息**：`Column %column; does not exist in Parquet file.`

- **原因**：源架构与接收器架构不匹配。

- **建议**：检查 "activity" 中的 the'mappings "。 请确保源列可以映射到正确的接收器列。


### <a name="error-code--parquetinvaliddataformat"></a>错误代码： ParquetInvalidDataFormat

- **消息**：`Incorrect format of %srcValue; for converting to %dstType;.`

- **原因**：无法将数据转换为在映射中指定的类型。源

- **建议**：请在复制活动列映射中仔细检查源数据或指定此列的正确数据类型。 请参阅文档： https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs 。


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>错误代码： ParquetDataCountNotMatchColumnCount

- **消息**：`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **原因**：源列计数和接收器列计数不匹配

- **建议**：双检查源列计数与 "映射" 中的接收器列计数相同。


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>错误代码： ParquetDataTypeNotMatchColumnType

- **消息**：数据类型% srcType;与给定的列类型% dstType; 不匹配位于列 "% columnIndex;"。

- **原因**：源中的数据无法转换为接收器中定义的类型化

- **建议**：请在映射中指定正确的类型。


### <a name="error-code--parquetbridgeinvaliddata"></a>错误代码： ParquetBridgeInvalidData

- **消息**：`%message;`

- **原因**：数据值超出限制

- **建议**：请重试。 如果问题仍然存在，请联系我们。


### <a name="error-code--parquetunsupportedinterpretation"></a>错误代码： ParquetUnsupportedInterpretation

- **消息**：`The given interpretation '%interpretation;' of parquet format is not supported.`

- **原因**：不支持的方案

- **建议**： "ParquetInterpretFor" 不应为 "sparkSql"。


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>错误代码： ParquetUnsupportFileLevelCompressionOption

- **消息**：`File level compression is not supported for Parquet.`

- **原因**：不支持的方案

- **建议**：在负载中删除 "CompressionType"。



## <a name="general-copy-activity-error"></a>常规复制活动错误

### <a name="error-code--jrenotfound"></a>错误代码： JreNotFound

- **消息**：`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **原因**：自承载集成运行时找不到 Java 运行时。 读取特定源时需要 Java 运行时。

- **建议**：检查集成运行时环境，参考文档：https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>错误代码： WildcardPathSinkNotSupported

- **消息**：`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **原因**：接收器数据集不支持通配符。

- **建议**：检查接收器数据集并修复不带通配符值的路径。


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>错误代码： MappingInvalidPropertyWithEmptyValue

- **消息**：`One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>错误代码： MappingInvalidPropertyWithNamePathAndOrdinal

- **消息**：`Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>错误代码： MappingDuplicatedOrdinal

- **消息**：`Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>错误代码： MappingInvalidOrdinalForSinkColumn

- **消息**：`Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>后续步骤

尝试通过以下资源获得故障排除方面的更多帮助：

*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [用于数据工厂的 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [有关数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
            
