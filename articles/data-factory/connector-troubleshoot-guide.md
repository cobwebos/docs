---
title: 排查 Azure 数据工厂连接器问题
description: 了解如何排查 Azure 数据工厂中的连接器问题。
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/26/2019
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 218031830a7516dfd539e1c0b9b665807822f38d
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533152"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>排查 Azure 数据工厂连接器问题

本文探讨了 Azure 数据工厂中连接器的常见故障排除方法。

## <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

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

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>错误消息：请求太大

- **症状**：你将数据复制到具有默认写入批大小的 Azure Cosmos DB，并命中错误 *"**请求大小太大**"* 。

- **原因**： Cosmos DB 限制单个请求的大小为 2 MB。 公式为，请求大小 = 单文档大小 * 写入批大小。 如果文档大小太大，则默认行为会导致太大的请求大小。 可以优化写入批大小。

- **解决方法**：在复制活动接收器中，减少 "写入批大小" 值（默认值为10000）。

### <a name="error-message-unique-index-constraint-violation"></a>错误消息：唯一索引约束冲突

- **症状**：将数据复制到 Cosmos DB 时遇到以下错误：

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **原因**：有两个可能的原因：

    - 如果使用**Insert** as 写入行为，此错误意味着源数据具有具有相同 ID 的行/对象。

    - 如果使用**Upsert**作为写入行为，并将另一个唯一键设置到容器，此错误意味着源数据的行/对象具有不同的 id，但定义的唯一键的值相同。

- **解决方法**： 

    - 对于 cause1，将**Upsert**设置为写入行为。
    - 对于原因2，请确保每个文档的定义唯一键的值不同。

### <a name="error-message-request-rate-is-large"></a>错误消息：请求速率太大

- **症状**：将数据复制到 Cosmos DB 时遇到以下错误：

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **原因**：所用的请求单位大于 Cosmos DB 中配置的可用 RU。 了解 Cosmos DB 从[此处](../cosmos-db/request-units.md#request-unit-considerations)计算 RU 的方式。

- **解决方法**：以下是两种解决方案：

    1. 在 Cosmos DB 中将**容器 RU 增加**到更大的值，这将提高复制活动的性能，但在 Cosmos DB 会导致更高的成本。 

    2. 将**writeBatchSize**减少到较小的值（例如1000），并将**parallelCopies**设置为较小的值（例如1），这会使复制运行性能比当前更糟，但不会在 Cosmos DB 中产生更多费用。

### <a name="column-missing-in-column-mapping"></a>列映射中缺少列

- **症状**：导入用于列映射的 Cosmos DB 的架构时，缺少某些列。 

- **原因**： ADF 从前10个 Cosmos DB 文档中推断架构。 如果某些列/属性不具有这些文档中的值，则 ADF 不会检测到它们，因此不会显示。

- **解决方法**：你可以按以下方式优化查询，以强制列在结果集中显示为空值：（假定：前10个文档中缺少 "不可能" 列）。 或者，您可以手动添加用于映射的列。

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>错误消息：读取器的 GuidRepresentation 为 CSharpLegacy

- **症状**：从带 UUID 字段 Cosmos DB MongoAPI/MongoDB 复制数据时，遇到以下错误：

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **原因**：可以通过两种方式来表示 BSON 中的 UUID-UuidStardard 和 UuidLegacy。 默认情况下，UuidLegacy 用于读取数据。 如果 MongoDB 中的 UUID 数据是 UuidStandard，则会遇到错误。

- **解决方法**：在 MongoDB 连接字符串中，添加选项 "**uuidRepresentation = standard**"。 有关详细信息，请参阅[MongoDB 连接字符串](connector-mongodb.md#linked-service-properties)。

## <a name="sftp"></a>SFTP

### <a name="error-message-invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>错误消息：为 "SshPublicKey" 身份验证类型提供的 SFTP 凭据无效

- **症状**：你使用 `SshPublicKey` 身份验证，并且遇到以下错误：

    ```
    Invalid Sftp credential provided for 'SshPublicKey' authentication type
    ```

- **原因**：有3种可能的原因：

    1. 如果你使用 ADF 创作 UI 创作 SFTP 链接服务，此错误意味着你选择使用的私钥的格式不正确。 您可以使用 SSH 私钥的 PKCS # 8 格式，同时请注意，ADF 仅支持传统 SSH 密钥格式。 更具体地说，PKCS # 8 格式和传统密钥格式的差异在于 PKCS # 8 密钥内容以 " *-----开始加密私钥-----* " 开头，而传统密钥格式则以 " *-----BEGIN RSA PRIVATE key-----* " 开头。
    2. 如果使用 Azure Key Vault 来存储私钥内容，或使用 programmatical 方法创作 SFTP 链接服务，此错误表示私钥内容不正确，很可能不是 base64 编码的内容。
    3. 凭据或私钥内容无效。

- **解决方法**： 

    - 出于 #1 的原因，请运行以下命令，将密钥转换为传统密钥格式，然后在 ADF 创作 UI 中使用它。

        ```
        # Decrypt the pkcs8 key and convert the format to traditional key format
        openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file

        chmod 600 traditional_format_key_file

        # Re-encrypte the key file using passphrase
        ssh-keygen -f traditional_format_key_file -p
        ```

    - 为了 #2，若要生成此类字符串，客户可以使用以下两种方法：
    - 使用第三方 base64 转换工具：将整个私钥内容粘贴到[Base64 编码和解码](https://www.base64encode.org/)的工具中，将其编码为 base64 格式字符串，然后将此字符串粘贴到密钥保管库，或使用此值以编程方式创作 SFTP 链接服务。
    - 使用C#代码：

        ```c#
        byte[] keyContentBytes = File.ReadAllBytes(privateKeyPath);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - 出于 #3 的原因，请使用其他工具来确认密钥文件或密码是否正确，以验证是否可以使用它来正确访问 SFTP 服务器。
  

## <a name="azure-sql-data-warehouse--azure-sql-database--sql-server"></a>Azure SQL 数据仓库 \ Azure SQL Database \ SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>错误代码： SqlFailedToConnect

- **消息**： `Cannot connect to SQL database: '%server;', Database: '%database;', User: '%user;'. Please check the linked service configuration is correct, and make sure the SQL database firewall allows the integration runtime to access.`

- **原因**：如果错误消息包含 "SqlException"，SQL 数据库将引发错误，指示某些特定操作失败。

- **建议**：请按照此参考文档中的 SQL 错误代码进行搜索，以了解更多详细信息： https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors 。 如果需要更多帮助，请联系 Azure SQL 支持。

- **原因**：如果错误消息包含 "具有 IP 地址的客户端 ..."不允许访问服务器 "，并且你正在尝试连接到 Azure SQL 数据库，这通常是由 Azure SQL 数据库防火墙问题导致的。

- **建议**：在 Azure SQL Server 防火墙配置中，启用 "允许 Azure 服务和资源访问此服务器" 选项。 参考文档： https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure 。


### <a name="error-code--sqloperationfailed"></a>错误代码： SqlOperationFailed

- **消息**： `A database operation failed. Please search error to get more details.`

- **原因**：如果错误消息包含 "SqlException"，SQL 数据库将引发错误，指示某些特定操作失败。

- **建议**：请按照此参考文档中的 SQL 错误代码进行搜索，以了解更多详细信息： https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors 。 如果需要更多帮助，请联系 Azure SQL 支持。

- **原因**：如果错误消息包含 "PdwManagedToNativeInteropException"，则通常是由于 source 和 sink 列大小不匹配造成的。

- **建议**：请检查源列和接收器列的大小。 如果需要更多帮助，请联系 Azure SQL 支持。

- **原因**：如果错误消息包含 "InvalidOperationException"，通常是由于输入数据无效导致的。

- **建议**：若要确定哪个行遇到了问题，请在复制活动上启用容错功能，该功能可将有问题的行重定向到存储，以便进一步调查。 参考文档： https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance 。


### <a name="error-code--sqlunauthorizedaccess"></a>错误代码： SqlUnauthorizedAccess

- **消息**： `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **原因**：凭据不正确，或登录帐户无法访问 SQL 数据库。

- **建议**：请检查登录帐户是否有足够的权限访问 SQL 数据库。


### <a name="error-code--sqlopenconnectiontimeout"></a>错误代码： SqlOpenConnectionTimeout

- **消息**： `Open connection to database timeout after '%timeoutValue;' seconds.`

- **原因**： SQL 数据库暂时性失败。

- **建议**：请重试以更新连接超时值较大的链接服务连接字符串。


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>错误代码： SqlAutoCreateTableTypeMapFailed

- **消息**： `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(colunm name:'%colunmName;') in auto-create table.`

- **原因**：自动创建表无法满足源要求。

- **建议**：请更新 "映射" 中的列类型，或在目标服务器中手动创建接收器表。


### <a name="error-code--sqldatatypenotsupported"></a>错误代码： SqlDataTypeNotSupported

- **消息**： `A database operation failed. Please check the SQL errors.`

- **原因**：如果在 SQL 源上出现问题，并且错误与 SqlDateTime 溢出有关，则数据值超过逻辑类型范围（1/1/1753 12:00:00 AM-12/31/9999 11:59:59 PM）。

- **建议**：请在源 SQL 查询中将类型转换为字符串，或在复制活动列映射中将列类型更改为 "string"。

- **原因**：如果在 SQL 接收器上出现问题，并且错误与 SqlDateTime 溢出相关，则数据值将超出接收器表中允许的范围。

- **建议**：请在接收器表中将相应的列类型更新为 "datetime2" 类型。


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>错误代码： SqlInvalidDbStoredProcedure

- **消息**： `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **原因**：指定的存储过程无效。 这可能是因为存储过程不返回任何数据。

- **建议**：请通过 SQL 工具验证存储过程。 请确保存储过程可以返回数据。


### <a name="error-code--sqlinvaliddbquerystring"></a>错误代码： SqlInvalidDbQueryString

- **消息**： `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **原因**：指定的 SQL 查询无效。 这可能是因为查询不返回任何数据。

- **建议**：请通过 sql 工具验证 sql 查询。 请确保查询可以返回数据。


### <a name="error-code--sqlinvalidcolumnname"></a>错误代码： SqlInvalidColumnName

- **消息**： `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **原因**：找不到列。 可能的配置错误。

- **建议**：请验证查询中的列、数据集中的 "structure" 和活动中的 "映射"。


### <a name="error-code--sqlbatchwritetimeout"></a>错误代码： SqlBatchWriteTimeout

- **消息**： `Timeout in SQL write opertaion.`

- **原因**： SQL 数据库暂时性失败。

- **建议**：如果问题重现，请联系 Azure SQL 支持。


### <a name="error-code--sqlbatchwriterollbackfailed"></a>错误代码： SqlBatchWriteRollbackFailed

- **消息**： `Timeout in SQL write operation and rollback also fail.`

- **原因**： SQL 数据库暂时性失败。

- **建议**：请重试以更新连接超时值较大的链接服务连接字符串。


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>错误代码： SqlBulkCopyInvalidColumnLength

- **消息**： `SQL Bulk Copy failed due to received an invalid column length from the bcp client.`

- **原因**： SQL 大容量复制失败，因为从 bcp 客户端收到的列长度无效。

- **建议**：若要确定哪个行遇到了问题，请在复制活动上启用容错功能，该功能可将有问题的行重定向到存储，以便进一步调查。 参考文档： https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance 。


### <a name="error-code--sqlconnectionisclosed"></a>错误代码： SqlConnectionIsClosed

- **消息**： `The connection is closed by SQL database.`

- **原因**： sql 数据库在高并发运行和服务器终止连接时关闭 sql 连接。

- **建议**：远程服务器关闭 SQL 连接。 请重试。 如果问题重现，请联系 Azure SQL 支持。

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

- **原因**：可能的原因是架构（总列宽宽度）太大（大于 1 MB）。 通过添加所有列的大小来检查目标 SQL DW 表的架构：

    - Int-> 4 个字节
    - Bigint-> 8 字节
    - Varchar （n）、char （n）、binary （n）、varbinary （n）-> n 个字节
    - Nvarchar （n）、nchar （n）-> n * 2 字节
    - Date-> 6 个字节
    - Datetime/（2）、smalldatetime-> 16 字节
    - Datetimeoffset-> 20 个字节
    - 小数-> 19 个字节
    - Float-> 8 个字节
    - Money-> 8 个字节
    - Smallmoney-> 4 个字节
    - 实 > 4 个字节
    - Smallint-> 2 个字节
    - 时间-> 12 个字节
    - Tinyint-> 1 个字节

- **解决方法**：将列宽缩小为小于 1 MB

- 或通过禁用 Polybase 来使用 bulk insert 方法

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>错误消息：不满足使用 HTTP 条件标头指定的条件

- **症状**：使用 SQL 查询从 Azure SQL 数据仓库拉取数据，并遇到以下错误：

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **原因**： Azure SQL 数据仓库在 azure 存储中查询外部表时遇到问题。

- **解决方法**：在 SSMS 中运行相同的查询，并检查是否看到相同的结果。 如果出现同一结果，请开具一个 Azure SQL 数据仓库的支持票证并提供你的 SQL DW 服务器和数据库名称，方便我们进一步排查问题。
            

## <a name="azure-blob-storage"></a>Azure Blob 存储

### <a name="error-code--azurebloboperationfailed"></a>错误代码： AzureBlobOperationFailed

- **消息**： `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **原因**： Blob 存储操作遇到问题。

- **建议**：请查看详细信息中的错误。 请参阅 blob 帮助文档： https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes 。 如果需要帮助，请联系存储团队。



## <a name="azure-data-lake-gen2"></a>Azure Data Lake Gen2

### <a name="error-code--adlsgen2operationfailed"></a>错误代码： AdlsGen2OperationFailed

- **消息**： `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **原因**： ADLS Gen2 引发指示操作失败的错误。

- **建议**：请检查 ADLS Gen2 引发的详细错误消息。 如果是暂时性故障导致的，请重试。 如果需要进一步的帮助，请联系 Azure 存储支持，并提供错误消息中的请求 ID。

- **原因**：如果错误消息包含 "禁止"，则你使用的服务主体或托管标识可能没有足够的权限访问该 ADLS Gen2。

- **建议**：请参阅帮助文档： https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication 。

- **原因**：当错误消息包含 "InternalServerError" 时，ADLS Gen2 将返回错误。

- **建议**：这可能是由于暂时性故障引起的，请重试。 如果问题仍然存在，请联系 Azure 存储支持，并提供错误消息中的请求 ID。


## <a name="next-steps"></a>后续步骤

有关故障排除的详细信息，请尝试以下资源：

*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [用于数据工厂的 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [有关数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
            
