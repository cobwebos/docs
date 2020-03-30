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
ms.openlocfilehash: 9f3a13a097d7cce87aead4ec2d76ce7cbbb1a206
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75778220"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>排查 Azure 数据工厂连接器问题

本文探讨 Azure 数据工厂中的连接器的常用故障排除方法。
  

## <a name="azure-blob-storage"></a>Azure Blob 存储

### <a name="error-code--azurebloboperationfailed"></a>错误代码：AzureBlob 操作失败

- **消息**：`Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **原因**：Blob 存储操作命中问题。

- **建议**：详细检查错误。 请参阅 blob 帮助文档https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes： 。 如果需要帮助，请联系存储团队。


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>错误代码：AzureBlob 服务不返回预期数据长度

- **消息**：`Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>错误代码：AzureBlob 不支持多个文件到单一 Blob

- **消息**：`Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>错误代码：Azure 存储操作失败并发写入

- **消息**：`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>错误消息：请求大小太大

- **症状**：将数据复制到具有默认写入批处理大小的 Azure Cosmos DB 中，并命中错误 *"**请求大小太大***"。

- **原因**：Cosmos DB 将单个请求的大小限制为 2 MB。 公式为请求大小 = 单个文档大小 * 写入批大小。 如果文档过大，默认行为会导致请求过大。 可以优化写入批大小。

- **分辨率**：在复制活动接收器中，减小"写入批处理大小"值（默认值为 10000）。

### <a name="error-message-unique-index-constraint-violation"></a>错误消息：唯一索引约束冲突

- **症状**： 将数据复制到 Cosmos DB 时，您出现以下错误：

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **原因**：有两个可能的原因：

    - 如果使用“插入”作为写入行为，则此错误表示源数据包含具有相同 ID 的行/对象。****

    - 如果使用“更新插入”作为写入行为，并设置了容器的另一个唯一键，则此错误表示源数据中的行/对象使用了定义的唯一键的不同 ID，但使用了该键的相同值。****

- **分辨率**： 

    - 对于原因 1，请将“更新插入”设置为写入行为。****
    - 对于原因 2，请确保每个文档使用定义的唯一键的不同值。

### <a name="error-message-request-rate-is-large"></a>错误消息：请求速率较大

- **症状**： 将数据复制到 Cosmos DB 时，您出现以下错误：

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **原因**：使用的请求单位大于 Cosmos DB 中配置的可用 RU。 在[此处](../cosmos-db/request-units.md#request-unit-considerations)了解 Cosmos DB 如何计算 RU。

- **解决方法**： 这里有两个解决方案：

    1. **将容器 RU 增加到**Cosmos DB 中更大的值，这将提高复制活动性能，但在 Cosmos DB 中会产生更多成本。 

    2. 将**writeBatchSize**减少到较小的值（如 1000），并将**并行副本**设置为较小的值（如 1），这将使复制运行性能比当前性能差，但不会在 Cosmos DB 中产生更大的成本。

### <a name="column-missing-in-column-mapping"></a>列映射中缺少列

- **症状**：当您为 Cosmos DB 导入用于列映射的架构时，某些列将丢失。 

- **原因**：ADF 从前 10 个 Cosmos DB 文档中推断出架构。 如果某些列/属性在这些文档中没有值，则它们不会被 ADF 检测到，因此也就不会显示。

- **解决方法**：您可以将查询按如下所示进行优化，以强制列在结果集中显示，结果值为空值：（假设前 10 个文档中缺少"不可能"列）。 或者，可以手动添加要映射的列。

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>错误消息：读者的 Guid 代表是 CSharpLegacy

- **症状**： 使用 UUID 字段从 Cosmos DB MongoAPI/MongoDB 复制数据时，您点击了以下错误：

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **原因**： 在 BSON 中表示 UUID 的方法有两种 - UuidStarard 和 UuidLegacy。 默认使用 UuidLegacy 来读取数据。 如果 MongoDB 中的 UUID 数据是 UuidStandard，则会出现错误。

- **分辨率**：在 MongoDB 连接字符串中，添加选项 **"uuid表示\标准**"。 有关详细信息，请参阅 [MongoDB 连接字符串](connector-mongodb.md#linked-service-properties)。
            

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code--adlsgen2operationfailed"></a>错误代码：AdlsGen2操作失败

- **消息**：`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **原因**： ADLS Gen2 引发错误指示操作失败。

- **建议**：检查 ADLS Gen2 引发的详细错误消息。 如果是由暂时性失败导致的，请重试。 如果需要进一步的帮助，请联系 Azure 存储支持，并提供错误消息中的请求 ID。

- **原因**：当错误消息包含"禁止"时，您使用的服务主体或托管标识可能没有足够的权限来访问 ADLS Gen2。

- **建议**： 请参阅帮助文档： https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication.

- **原因**：当错误消息包含"内部服务器错误"时，错误由 ADLS Gen2 返回。

- **建议**：可能是由暂时性故障引起的，请重试。 如果问题持续存在，请联系 Azure 存储支持，并提供错误消息中的请求 ID。


### <a name="error-code--adlsgen2invalidurl"></a>错误代码： AdlsGen2 无效URL

- **消息**：`Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>错误代码： AdlsGen2 无效文件夹路径

- **消息**：`The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>错误代码：AdlsGen2操作失败并发写入

- **消息**：`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code--adlsgen2timeouterror"></a>错误代码：AdlsGen2超时错误

- **消息**：`Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>错误消息：远程服务器返回错误： （403） 禁止

- **症状**： 复制活动失败，出现以下错误： 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **原因**：一个可能的原因是您使用的服务主体或托管标识没有访问特定文件夹/文件的权限。

- **解决方法**：对需要复制的所有文件夹和子文件夹授予相应的权限。 请参阅[此文档](connector-azure-data-lake-store.md#linked-service-properties)。

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>错误消息：无法使用服务主体获取访问令牌。 ADAL 错误：service_unavailable

- **症状**： 复制活动失败，出现以下错误：

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **原因**：当 Azure 活动目录拥有的服务令牌服务器 （STS） 不可用，即忙得无法处理请求时，它将返回 HTTP 错误 503。 

- **解决方法**：几分钟后重新运行复制活动。
                  

## <a name="azure-sql-data-warehouseazure-sql-databasesql-server"></a>Azure SQL 数据仓库/Azure SQL 数据库/SQL 服务器

### <a name="error-code--sqlfailedtoconnect"></a>错误代码：SqlFailed 连接到

- **消息**：`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **原因**：如果错误消息包含"SqlException"，SQL 数据库将引发错误，指示某些特定操作失败。

- **建议**：请在此参考文档中按 SQL 错误代码搜索更多详细信息： https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors。 如果您需要其他帮助，请与 Azure SQL 支持部门联系。

- **原因**：如果错误消息包含"具有 IP 地址的客户端"...'不允许访问服务器"，并且您尝试连接到 Azure SQL 数据库，通常是由 Azure SQL 数据库防火墙问题引起的。

- **建议**：在 Azure SQL Server 防火墙配置中，启用"允许 Azure 服务和资源访问此服务器"选项。 参考文档：https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure。


### <a name="error-code--sqloperationfailed"></a>错误代码：Sql操作失败

- **消息**：`A database operation failed. Please search error to get more details.`

- **原因**：如果错误消息包含"SqlException"，SQL 数据库将引发错误，指示某些特定操作失败。

- **建议**：如果 SQL 错误不明确，请尝试将数据库更改为最新的兼容性级别"150"。 它可以引发最新版本的 SQL 错误。 请参阅详细信息文档： https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#backwardCompat。
        有关解决 SQL 问题的疑难解答，请在此参考文档中搜索 SQL 错误代码https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors以了解更多详细信息： 。 如果您需要其他帮助，请与 Azure SQL 支持部门联系。

- **原因**：如果错误消息包含"Pdw管理到本机InteropException"，通常是由于源列和接收器列大小不匹配造成的。

- **建议**：检查源列和接收器列的大小。 如果您需要其他帮助，请与 Azure SQL 支持部门联系。

- **原因**：如果错误消息包含"无效操作异常"，则通常是由无效输入数据引起的。

- **建议**：要确定遇到问题的行，请在复制活动上启用容错功能，该功能可以将有问题的行重定向到存储以进行进一步调查。 参考文档：https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance。


### <a name="error-code--sqlunauthorizedaccess"></a>错误代码：Sql 未经授权访问

- **消息**：`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **原因**：凭据不正确或登录帐户无法访问 SQL 数据库。

- **建议**：检查登录帐户是否有足够的权限访问 SQL 数据库。


### <a name="error-code--sqlopenconnectiontimeout"></a>错误代码：SqlOpen连接超时

- **消息**：`Open connection to database timeout after '%timeoutValue;' seconds.`

- **原因**：可能是 SQL 数据库瞬态故障。

- **建议**：请重试更新具有较大连接超时值的链接服务连接字符串。


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>错误代码：SqlAutocreateTable 类型映射失败

- **消息**：`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **原因**：自动创建表无法满足源要求。

- **建议**：在"映射"中更新列类型，或在目标服务器中手动创建接收器表。


### <a name="error-code--sqldatatypenotsupported"></a>错误代码：不支持 SqlDataType

- **消息**：`A database operation failed. Check the SQL errors.`

- **原因**：如果问题发生在 SQL 源上，并且错误与 SqlDateTime 溢出有关，则数据值超过逻辑类型范围（1/1/1753 12：00：00 AM - 12/31/9999 11：59：59 PM）。

- **建议**：在源 SQL 查询或复制活动列映射中将类型转换为字符串，将列类型更改为"String"。

- **原因**：如果问题发生在 SQL 接收器上，并且错误与 SqlDateTime 溢出有关，则数据值超过接收器表中允许的范围。

- **建议**：将相应的列类型更新为接收器表中的"datetime2"类型。


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>错误代码：SqlInvalidDb存储过程

- **消息**：`The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **原因**：指定的存储过程无效。 这可能是因为存储过程不返回任何数据。

- **建议**：通过 SQL 工具验证存储过程。 请确保存储过程可以返回数据。


### <a name="error-code--sqlinvaliddbquerystring"></a>错误代码：SqlInvalidDb查询字符串

- **消息**：`The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **原因**：指定的 SQL 查询无效。 这可能是因为查询不返回任何数据。

- **建议**：按 SQL 工具验证 SQL 查询。 确保查询可以返回数据。


### <a name="error-code--sqlinvalidcolumnname"></a>错误代码：SqlInvalidcolumn 名称

- **消息**：`Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **原因**：找不到列。 可能存在配置错误。

- **建议**：验证查询中的列、数据集中的"结构"和活动中的"映射"。


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>错误代码：SqlColumnName不匹配通过区分大小写

- **消息**：`Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>错误代码：SqlBatchWriteTimetimeout

- **消息**：`Timeouts in SQL write operation.`

- **原因**：可能是 SQL 数据库瞬态故障。

- **建议**：请重试。 如果重新提供问题，请与 Azure SQL 支持部门联系。


### <a name="error-code--sqlbatchwritetransactionfailed"></a>错误代码：SqlBatchWrite 交易失败

- **消息**：`SQL transaction commits failed`

- **原因**：如果异常详细信息不断告诉事务超时，则集成运行时和数据库之间的网络延迟高于默认阈值 30 秒。

- **建议**：更新 Sql 链接的服务连接字符串，其"连接超时"值等于 120 或更高，然后重新运行活动。

- **原因**：如果异常详细信息间歇性地告诉 sql 连接断开，则可能只是暂时性网络故障或 SQL 数据库端问题

- **建议**：请重试活动并查看 SQL 数据库端指标。


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>错误代码：SqlBulkCopy 无效列长度

- **消息**：`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **原因**：由于从 bcp 客户端接收无效列长度，SQL 批量复制失败。

- **建议**：要确定遇到问题的行，请在复制活动上启用容错功能，该功能可以将有问题的行重定向到存储以进行进一步调查。 参考文档：https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance。


### <a name="error-code--sqlconnectionisclosed"></a>错误代码：SqlConnection 已关闭

- **消息**：`The connection is closed by SQL Database.`

- **原因**：当高并发运行和服务器终止连接时，SQL 数据库将关闭 SQL 连接。

- **建议**：远程服务器关闭了 SQL 连接。 请重试。 如果重新提供问题，请与 Azure SQL 支持部门联系。


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>错误代码：SqlcreateTable 失败不支持的类型

- **消息**：`Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>错误消息：从字符串转换为唯一标识符时转换失败

- **症状**：当您使用暂存副本和 PolyBase 将数据从表格数据源（如 SQL Server）复制到 Azure SQL 数据仓库时，您会遭遇以下错误：

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **原因**：Azure SQL 数据仓库库基础无法将空字符串转换为 GUID。

- **分辨率**：在"复制活动接收器"中，在"多边基"设置下，将"**使用类型默认值**"选项设置为 false。

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>错误消息：预期数据类型：DECIMAL（x，x），违规值

- **症状**：当您使用暂存副本和 PolyBase 将数据从表格数据源（如 SQL Server）复制到 SQL DW 时，您会遭遇以下错误：

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **原因**：Azure SQL 数据仓库库基础无法将空字符串（空值）插入到十进制列中。

- **分辨率**：在"复制活动接收器"中，在"多边基"设置下，将"**使用类型默认值**"选项设置为 false。

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>错误消息：Java 异常消息：HdfsBridge：：创建记录阅读器

- **症状**：使用 PolyBase 将数据复制到 Azure SQL 数据仓库，并点击以下错误：

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **原因**：可能的原因是架构（总列宽度）过大（大于 1 MB）。 通过添加所有列的大小来检查目标 SQL 数据仓库表的架构：

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

- **分辨率**：将列宽度减小到小于 1 MB

- 或者，通过禁用 Polybase 来使用批量插入方法

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>错误消息：未满足使用 HTTP 条件标头指定的条件

- **症状**：使用 SQL 查询从 Azure SQL 数据仓库中提取数据，并命中以下错误：

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **原因**：Azure SQL 数据仓库在 Azure 存储中查询外部表时出现问题。

- **解决方法**：在 SSMS 中运行相同的查询，并检查是否看到相同的结果。 如果出现同一结果，请开具一个 Azure SQL 数据仓库的支持票证并提供你的 SQL DW 服务器和数据库名称，方便我们进一步排查问题。
            

## <a name="delimited-text-format"></a>带分隔符的文本格式

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>错误代码：分隔文本列名不允许无效

- **消息**：`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **原因**：在活动中设置"第一行"时，第一行将用作列名。 此错误表示第一行包含空值。 例如："列A，列B"。

- **建议**：检查第一行，并在有空值时修复该值。


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>错误代码：分隔文本更多列符，已定义

- **消息**：`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **原因**：有问题的行的列计数大于第一行的列计数。 这可能是由数据问题或不正确的列分隔符/报价字符设置引起的。

- **建议**：请在错误消息中获取行计数，检查行的列并修复数据。

- **原因**：如果错误消息中的预期列计数为"1"，则可能是您指定的压缩或格式设置错误，从而导致 ADF 错误地分析文件。

- **建议**：检查格式设置，以确保它与源文件匹配。

- **原因**：如果源是文件夹，则指定文件夹下的文件可能具有不同的架构。

- **建议**：确保给定文件夹下的文件具有相同的架构。


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>错误代码：分隔文本不正确的行限制器

- **消息**：`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>错误代码：分隔文本太大列计数

- **消息**：`Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>错误代码：分隔文本无效设置

- **消息**：`%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>动态 365/通用数据服务/动态 CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>错误代码：动态创建服务客户端错误

- **消息**：`This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **原因**：这是动态服务器端的暂时性问题。

- **建议**：重新运行管道。 如果继续失败，请尝试降低并行性。 如果仍然失败，请联系动态支持。



## <a name="json-format"></a>JSON 格式

### <a name="error-code--jsoninvalidarraypathdefinition"></a>错误代码：Json 无效数组路径定义

- **消息**：`Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>错误代码：JsonEmptyJObjectData

- **消息**：`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>错误代码：JsonNullValueInPath定义

- **消息**：`Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>错误代码：Json 不支持的分层复杂值

- **消息**：`The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>错误代码：Json冲突分区数据架构

- **消息**：`Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>错误代码：Json 无效数据格式

- **消息**：`Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>错误代码： Json 无效数据混合数组和对象

- **消息**：`Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`



## <a name="parquet-format"></a>镶木地板格式

### <a name="error-code--parquetjavainvocationexception"></a>错误代码：ParquetJavain调用异常

- **消息**：`An error occurred when invoking java, message: %javaException;.`

- **原因**：当错误消息包含"java.lang.OutMemory"、"Java 堆空间"和"双容量"时，通常是旧版本的集成运行时的内存管理问题。

- **建议**：如果您使用的是自托管集成运行时，并且版本早于 3.20.7159.1，建议升级到最新版本。

- **原因**：当错误消息包含"java.lang.OutMemory"时，集成运行时没有足够的资源来处理文件。

- **建议**：限制集成运行时的并发运行。 对于自托管集成运行时，可扩展到内存等于或大于 8 GB 的强大计算机。

- **原因**：当错误消息包含"NullPointer 参考"时，可能是暂时性错误。

- **建议**：请重试。 如果问题仍然存在，请联系支持人员。


### <a name="error-code--parquetinvalidfile"></a>错误代码：Parquet 无效文件

- **消息**：`File is not a valid parquet file.`

- **原因**：镶木地板文件问题。

- **建议**：检查输入是有效的镶木地板文件。


### <a name="error-code--parquetnotsupportedtype"></a>错误代码：Parquet 不支持类型

- **消息**：`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **原因**：Azure 数据工厂不支持镶木地板格式。

- **建议**：仔细检查源数据。 请参阅文档： https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs。


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>错误代码：镶木地板十进制精度刻度

- **消息**：`Decimal Precision or Scale information is not found in schema for column: %column;.`

- **原因**：尝试分析数字精度和比例，但没有提供此类信息。

- **建议**： "源"不返回正确的精度和比例。 检查问题列精度和比例。


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>错误代码：镶木地板无效十进制精度刻度

- **消息**：`Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **原因**：架构无效。

- **建议**：检查问题列精度和比例。


### <a name="error-code--parquetcolumnnotfound"></a>错误代码： ParquetColumn 未找到

- **消息**：`Column %column; does not exist in Parquet file.`

- **原因**：源架构与接收器架构不匹配。

- **建议**：检查"活动"中的"映射"。 确保源列可以映射到右接收器列。


### <a name="error-code--parquetinvaliddataformat"></a>错误代码：Parquet 无效数据格式

- **消息**：`Incorrect format of %srcValue; for converting to %dstType;.`

- **原因**：数据无法转换为映射中指定的类型。

- **建议**：在复制活动列映射中仔细检查源数据或指定此列的正确数据类型。 请参阅文档： https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs。


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>错误代码：ParquetDataCountNotMatchColumnCount

- **消息**：`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **原因**：源列计数和接收器列计数不匹配

- **建议**：双检查源列计数与"映射"中的接收器列计数相同。


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>错误代码：镶木地板类型不匹配列型

- **消息**： 数据类型 %srcType;与给定列类型 %dstType 不匹配;在列"%列索引;"。

- **原因**：源数据无法转换为在接收器中定义的类型化数据

- **建议**：请在映射.sink 中指定正确的类型。


### <a name="error-code--parquetbridgeinvaliddata"></a>错误代码：镶木地板无效数据

- **消息**：`%message;`

- **原因**： 数据值超过限制

- **建议**：请重试。 如果问题仍然存在，请联系我们。


### <a name="error-code--parquetunsupportedinterpretation"></a>错误代码：无支持的"镶木地板"

- **消息**：`The given interpretation '%interpretation;' of parquet format is not supported.`

- **原因**： 不支持方案

- **建议**：'参数解释为'不应该是'火花Sql'。


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>错误代码：Parquet 不支持文件级别压缩选项

- **消息**：`File level compression is not supported for Parquet.`

- **原因**： 不支持方案

- **建议**：删除有效负载中的"压缩类型"。



## <a name="general-copy-activity-error"></a>常规复制活动错误

### <a name="error-code--jrenotfound"></a>错误代码： JreNotFound

- **消息**：`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **原因**：自托管的集成运行时找不到 Java 运行时。 读取特定源需要 Java 运行时。

- **建议**：检查集成运行时环境，参考文档：https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>错误代码：不支持通配符路径

- **消息**：`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **原因**：接收器数据集不支持通配符。

- **建议**：检查接收器数据集并修复没有通配符值的路径。


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>错误代码：映射无效属性与空值

- **消息**：`One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>错误代码：映射无效属性与名称路径和 ordinal

- **消息**：`Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>错误代码：映射重复性Ordinal

- **消息**：`Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>错误代码：映射无效的"无效"，用于查找问题柱

- **消息**：`Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>后续步骤

尝试通过以下资源获得故障排除方面的更多帮助：

*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [数据工厂的堆栈溢出论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [有关数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
            
