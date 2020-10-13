---
title: Azure 数据工厂中复制活动的容错
description: 了解如何通过跳过不兼容数据向 Azure 数据工厂中的复制活动添加容错。
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: yexu
ms.openlocfilehash: 4a0529248c58f7fa7f962d9d1432411c351c7bdd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440637"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Azure 数据工厂中复制活动的容错
> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [当前版本](copy-activity-fault-tolerance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

将数据从源复制到目标存储时，Azure 数据工厂复制活动提供一定程度的容错，以防止数据移动过程中因出现故障而中断。 例如，你要将数百万行从源复制到目标存储，其中，在目标数据库中创建了主键，但源数据库没有定义任何主键。 当你将重复行从源复制到目标时，将在目标数据库上遇到 PK 冲突故障。 目前，复制活动提供了两种处理此类错误的方法： 
- 如果遇到任何故障，你可以中止复制活动。 
- 可以通过启用容错以跳过不兼容的数据，继续复制其余部分。 例如，在这种情况下，跳过重复的行。 此外，还可以通过在复制活动中启用会话日志来记录跳过的数据。 

## <a name="copying-binary-files"></a>复制二进制文件 

ADF 在复制二进制文件时支持以下容错方案。 在以下情况下，可以选择中止复制活动或继续复制其余内容：

1. ADF 要复制的文件同时被其他应用程序删除。
2. 某些特定文件夹或文件不允许 ADF 访问，因为这些文件或文件夹的 ACL 需要比 ADF 中配置的连接信息更高的权限级别。
3. 如果在 ADF 中启用数据一致性验证设置，则不会验证一个或多个文件在源和目标存储之间是否一致。

### <a name="configuration"></a>配置 
在存储之间复制二进制文件时，可以启用容错，如下所示： 

```json
"typeProperties": { 
    "source": { 
        "type": "BinarySource", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreReadSettings", 
            "recursive": true 
            } 
    }, 
    "sink": { 
        "type": "BinarySink", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreWriteSettings" 
        } 
    }, 
    "skipErrorFile": { 
        "fileMissing": true, 
        "fileForbidden": true, 
        "dataInconsistency": true 
    }, 
    "validateDataConsistency": true, 
    "logStorageSettings": { 
        "linkedServiceName": { 
            "referenceName": "ADLSGen2", 
            "type": "LinkedServiceReference" 
            }, 
        "path": "sessionlog/" 
     } 
} 
```
属性 | 说明 | 允许的值 | 必须
-------- | ----------- | -------------- | -------- 
skipErrorFile | 一组属性，用于指定在数据移动过程中要跳过的失败类型。 | | 否
fileMissing | SkipErrorFile 属性包中的一个键值对，用于确定是否要跳过在复制 ADF 时被其他应用程序删除的文件。 <br/> -True：跳过其他应用程序正在删除的文件，复制其余内容。 <br/> -False：在数据移动过程中，一旦从源存储中删除任何文件则中止复制活动。 <br/>默认情况下，该属性设置为 True。 | True（默认值） <br/>False | 否
fileForbidden | SkipErrorFile 属性包中的一个键值对，用来确定当这些文件或文件夹的 ACL 需要比 ADF 中配置的连接更高的权限级别时，是否要跳过特定文件。 <br/> -True：要通过跳过文件来复制其余内容。 <br/> -False：在获取文件夹或文件的权限问题后，要中止复制活动。 | True <br/>False（默认值） | 否
dataInconsistency | SkipErrorFile 属性包中的一个键值对，用于确定是否要跳过源和目标存储之间不一致的数据。 <br/> -True：要通过跳过不一致的数据来复制其余内容。 <br/> -False：找到不一致的数据后要中止复制活动。 <br/>请注意，仅当你将 validateDataConsistency 设置为 True 时，此属性才有效。 | True <br/>False（默认值） | 否
logStorageSettings  | 当要记录跳过的对象名称时可以指定的一组属性。 | &nbsp; | 否
linkedServiceName | [Azure Blob 存储](connector-azure-blob-storage.md#linked-service-properties)或 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) 的链接服务，用于存储会话日志文件。 | `AzureBlobStorage` 或 `AzureBlobFS` 类型链接服务的名称，指代用于存储日志文件的实例。 | 否
path | 日志文件的路径。 | 指定用于存储日志文件的路径。 如果未提供路径，服务会为用户创建一个容器。 | 否

> [!NOTE]
> 下面是在复制二进制文件时在复制活动中启用容错功能的先决条件。
> 若要在从源存储中删除特定文件时跳过这些文件，必须满足以下条件：
> - 源数据集和接收器数据集必须是二进制格式，且不能指定压缩类型。 
> - 支持的数据存储类型为 Azure Blob 存储、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure 文件存储、文件系统、FTP、SFTP、Amazon S3、Google Cloud Storage 和 HDFS。
> - 仅当在源数据集中指定多个文件（可以是文件夹、通配符或文件列表）时，复制活动才能跳过特定错误文件。 若要将源数据集中的单个文件指定为复制到目标，则如果出现任何错误，复制活动会失败。
>
> 若要在源存储中禁止访问特定文件时跳过这些文件，必须满足以下条件：
> - 源数据集和接收器数据集必须是二进制格式，且不能指定压缩类型。 
> - 支持的数据存储类型为 Azure Blob 存储、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure 文件存储、SFTP、Amazon S3 和 HDFS。
> - 仅当在源数据集中指定多个文件（可以是文件夹、通配符或文件列表）时，复制活动才能跳过特定错误文件。 若要将源数据集中的单个文件指定为复制到目标，则如果出现任何错误，复制活动会失败。
>
> 若要在验证特定文件在源存储和目标存储之间是否不一致时跳过这些文件，必须满足以下条件：
> - 可从[此处](https://docs.microsoft.com/azure/data-factory/copy-activity-data-consistency)的数据一致性文档获取更多详细信息。

### <a name="monitoring"></a>监视 

#### <a name="output-from-copy-activity"></a>复制活动的输出
可以通过每个复制活动运行的输出来获取读取、写入和跳过的文件数。 

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```

#### <a name="session-log-from-copy-activity"></a>复制活动的会话日志

如果配置为记录跳过的文件名称，可以通过此路径找到日志文件：`https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv`。 

日志文件只能是 csv 文件。 日志文件的架构如下所示：

列 | 说明 
-------- | -----------  
Timestamp | ADF 跳过文件时的时间戳。
级别 | 此项的日志级别。 对于显示文件跳过的项，它将处于“警告”级别。
OperationName | 每个文件上的 ADF 复制活动操作行为。 它将为“FileSkip”，以指定要跳过的文件。
OperationItem | 要跳过的文件名。
Message | 说明为何要跳过文件的详细信息。

日志文件的示例如下所示： 
```
Timestamp,Level,OperationName,OperationItem,Message 
2020-03-24 05:35:41.0209942,Warning,FileSkip,"bigfile.csv","File is skipped after read 322961408 bytes: ErrorCode=UserErrorSourceBlobNotExist,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=The required Blob is missing. ContainerName: https://transferserviceonebox.blob.core.windows.net/skipfaultyfile, path: bigfile.csv.,Source=Microsoft.DataTransfer.ClientLibrary,'." 
2020-03-24 05:38:41.2595989,Warning,FileSkip,"3_nopermission.txt","File is skipped after read 0 bytes: ErrorCode=AdlsGen2OperationFailed,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=ADLS Gen2 operation failed for: Operation returned an invalid status code 'Forbidden'. Account: 'adlsgen2perfsource'. FileSystem: 'skipfaultyfilesforbidden'. Path: '3_nopermission.txt'. ErrorCode: 'AuthorizationPermissionMismatch'. Message: 'This request is not authorized to perform this operation using this permission.'. RequestId: '35089f5d-101f-008c-489e-01cce4000000'..,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Operation returned an invalid status code 'Forbidden',Source=,''Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message='Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message=Operation returned an invalid status code 'Forbidden',Source=Microsoft.DataTransfer.ClientLibrary,',Source=Microsoft.DataTransfer.ClientLibrary,'." 
```
在上面的日志中，你可以看到已跳过 bigfile.csv，因为在 ADF 复制时，其他应用程序删除了此文件。 已跳过 3_nopermission.txt，因为由于权限问题不允许 ADF 访问它。


## <a name="copying-tabular-data"></a>复制表格数据 

### <a name="supported-scenarios"></a>支持的方案
复制活动支持三种检测、跳过和记录不兼容表格数据的方案：

- **源数据类型与接收器本机类型不兼容**。 

    例如：将数据从 Blob 存储中的 CSV 文件复制到具有架构定义的包含三个 INT 类型列的 SQL 数据库。 包含数值数据的 CSV 文件行（如 123,456,789）成功复制到接收器存储。 但是，包含非数字值的行（如 123,456, abc ）检测为不兼容，并被跳过。

- **源与接收器之间的列数不匹配**。

    例如：使用包含六个列的架构定义，将数据从 Blob 存储中的 CSV 文件复制到 SQL 数据库。 包含六个列的 CSV 文件行会成功复制到接收器存储。 包含多于六列的 CSV 文件行将检测为不兼容，并被跳过。

- 写入 SQL Server/Azure SQL 数据库/Azure Cosmos DB 时发生主键冲突。

    例如：将数据从 SQL 服务器复制到 SQL 数据库。 接收器 SQL 数据库中定义了主键，但源 SQL 服务器中未定义此类主键。 源中的重复行无法复制到接收器。 复制活动仅将源数据的第一行复制到接收器。 包含重复主键值的后续源行会被检测为不兼容，并被跳过。

>[!NOTE]
>- 若要使用 PolyBase 将数据加载到 Azure Synapse Analytics（以前称为 SQL 数据仓库），请配置 PolyBase 的原生容错设置，方法是在复制活动中通过 [polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink) 指定拒绝策略。 同时，仍然可以正常启用将 PolyBase 不兼容行重定向到 Blob 或 ADLS，如下所示。
>- 将复制活动配置为调用 [AmazonRedShift 卸载](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift)时，此功能不适用。
>- 当复制活动配置为调用 [SQL 接收器中的存储过程](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#invoke-a-stored-procedure-from-a-sql-sink)时，此功能不适用。

### <a name="configuration"></a>配置
下面的 JSON 定义示例用于配置在复制活动中跳过不兼容行：

```json
"typeProperties": { 
    "source": { 
        "type": "AzureSqlSource" 
    }, 
    "sink": { 
        "type": "AzureSqlSink" 
    }, 
    "enableSkipIncompatibleRow": true, 
    "logStorageSettings": { 
    "linkedServiceName": { 
        "referenceName": "ADLSGen2", 
        "type": "LinkedServiceReference" 
        }, 
    "path": "sessionlog/" 
    } 
}, 
```

属性 | 说明 | 允许的值 | 必须
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | 指定是否在复制期间跳过不兼容的行。 | True<br/>False（默认值） | 否
logStorageSettings | 若要记录不兼容行，可以指定的一组属性。 | &nbsp; | 否
linkedServiceName | [Azure Blob 存储](connector-azure-blob-storage.md#linked-service-properties)或 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) 的链接服务，用于存储包含已跳过行的日志。 | `AzureBlobStorage` 或 `AzureBlobFS` 类型链接服务的名称，指代用于存储日志文件的实例。 | 否
path | 包含已跳过行的日志文件的路径。 | 指定要用于记录不兼容数据的路径。 如果未提供路径，服务会为用户创建一个容器。 | 否

### <a name="monitor-skipped-rows"></a>监视跳过的行
复制活动运行完成后，可以在复制活动输出中看到跳过的行数：

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "logPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```

如果配置为记录不兼容的行，可以通过此路径找到日志文件：`https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv`。 

日志文件将是 csv 文件。 日志文件的架构如下所示：

列 | 说明 
-------- | -----------  
Timestamp | ADF 跳过不兼容行时的时间戳
级别 | 此项的日志级别。 如果此项显示跳过的行，它将处于“警告”级别
OperationName | 每个行上的 ADF 复制活动操作行为。 它将为“TabularRowSkip”以指定已跳过特定不兼容行
OperationItem | 源数据存储中的已跳过行。
Message | 说明此特定行不兼容性的详细信息。


下面的示例展示了日志文件内容：

```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:32.2586581, Warning, TabularRowSkip, """data1"", ""data2"", ""data3""," "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'." 
2020-02-26 06:22:33.2586351, Warning, TabularRowSkip, """data4"", ""data5"", ""data6"",", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)." 
```

在上面的示例日志文件中，你可以看到由于源到目标存储的类型转换问题，跳过了一行“data1, data2, data3”。 由于源到目标存储中出现 PK 冲突问题，已跳过另一行“data4, data5, data6”。 


## <a name="copying-tabular-data-legacy"></a>复制表格数据（旧版）：

下面是仅对复制表格数据启用容错的传统方法。 如果你正在创建新的管道或活动，建议你从[此处](#copying-tabular-data)开始。

### <a name="configuration"></a>配置
下面的 JSON 定义示例用于配置在复制活动中跳过不兼容行：

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

属性 | 说明 | 允许的值 | 必须
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | 指定是否在复制期间跳过不兼容的行。 | True<br/>False（默认值） | 否
redirectIncompatibleRowSettings | 若要记录不兼容行，可以指定的一组属性。 | &nbsp; | 否
linkedServiceName | [Azure 存储](connector-azure-blob-storage.md#linked-service-properties)或 [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) 的链接服务，用于存储包含跳过的行的记录。 | `AzureStorage` 或 `AzureDataLakeStore` 类型链接服务的名称，指代要用于存储日志文件的实例。 | 否
path | 包含跳过行的日志文件的路径。 | 指定要用于记录不兼容数据的路径。 如果未提供路径，服务会为用户创建一个容器。 | 否

### <a name="monitor-skipped-rows"></a>监视跳过的行
复制活动运行完成后，可以在复制活动输出中看到跳过的行数：

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
如果配置为记录不兼容的行，可以通过此路径找到日志文件：`https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`。 

日志文件只能是 csv 文件。 所跳过的原始数据必要时将以逗号作为列分隔符进行记录。 除了日志文件中的原始源数据外，我们还另外添加两列“ErrorCode”和“ErrorMessage”，可从中查看不兼容的根本原因。 ErrorCode 和 ErrorMessage 将用双引号引起来。 

下面的示例展示了日志文件内容：

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>后续步骤
请参阅其他复制活动文章：

- [复制活动概述](copy-activity-overview.md)
- [复制活动性能](copy-activity-performance.md)


