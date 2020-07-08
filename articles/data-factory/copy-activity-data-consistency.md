---
title: 复制活动中的数据一致性验证
description: 了解如何在 Azure 数据工厂的复制活动中启用数据一致性验证。
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: yexu
ms.openlocfilehash: a45c8ce820532d11f18758924dc3399818cb9158
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84610213"
---
#  <a name="data-consistency-verification-in-copy-activity-preview"></a>复制活动中的数据一致性验证（预览版）

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

当你将数据从源存储移动到目标存储时，Azure 数据工厂复制活动提供了一个选项，供你执行额外的数据一致性验证，以确保数据不仅成功地从源存储复制到目标存储，而且验证了源存储和目标存储之间的一致性。 在数据移动过程中发现不一致的数据后，你可以中止复制活动，或者通过启用容错设置跳过不一致的数据来继续复制其余的数据。 通过在复制活动中启用会话日志设置，可以获取跳过的对象名称。 

> [!IMPORTANT]
> 此功能目前处于预览状态，我们正在积极处理以下限制：
>- 只有在复制活动中具有“PreserveHierarchy”行为的基于文件的存储之间复制二进制文件时，数据一致性验证才可用。 对于复制表格数据，数据一致性验证在复制活动中尚不可用。
>- 当在复制活动中启用会话日志设置以记录跳过的不一致文件时，如果复制活动失败，则无法 100% 保证日志文件的完整性。
>- 会话日志只包含不一致的文件，到目前为止，尚未记录成功复制的文件。

## <a name="supported-data-stores"></a>支持的数据存储

### <a name="source-data-stores"></a>源数据存储

-   [Azure Blob 存储](connector-azure-blob-storage.md)
-   [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Azure 文件存储](connector-azure-file-storage.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)
-   [文件系统](connector-file-system.md)
-   [HDFS](connector-hdfs.md)

### <a name="destination-data-stores"></a>目标数据存储

-   [Azure Blob 存储](connector-azure-blob-storage.md)
-   [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Azure 文件存储](connector-azure-file-storage.md)
-   [文件系统](connector-file-system.md)


## <a name="configuration"></a>配置
以下示例提供了一个 JSON 定义，用于在复制活动中启用数据一致性验证： 

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
    "validateDataConsistency": true, 
    "skipErrorFile": { 
        "dataInconsistency": true 
    }, 
    "logStorageSettings": { 
        "linkedServiceName": { 
            "referenceName": "ADLSGen2_storage", 
            "type": "LinkedServiceReference" 
        }, 
        "path": "/sessionlog/" 
} 
} 
```

properties | 描述 | 允许的值 | 必须
-------- | ----------- | -------------- | -------- 
validateDataConsistency | 如果将此属性设置为 true，则复制活动将检查从源存储复制到目标存储的每个对象的文件大小、lastModifiedDate 和 MD5 校验和，以确保源存储和目标存储之间的数据一致性。 请注意，启用此选项会影响复制性能。  | True<br/>False（默认值） | 否
dataInconsistency | SkipErrorFile 属性包中的一个键值对，用于确定是否要跳过不一致的数据。<br/> -True：要通过跳过不一致的数据来复制其余内容。<br/> -False：找到不一致的数据后要中止复制活动。<br/>请注意，仅当你将 validateDataConsistency 设置为 True 时，此属性才有效。  | True<br/>False（默认值） | 否
logStorageSettings | 一组属性，可以指定这些属性以使会话日志能够记录跳过的对象。 | | 否
linkedServiceName | [Azure Blob 存储](connector-azure-blob-storage.md#linked-service-properties)或 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) 的链接服务，用于存储会话日志文件。 | `AzureBlobStorage` 或 `AzureBlobFS` 类型链接服务的名称，指代用于存储日志文件的实例。 | 否
path | 日志文件的路径。 | 指定用于存储日志文件的路径。 如果未提供路径，服务会为用户创建一个容器。 | 否

>[!NOTE]
>- 临时复制方案不支持数据一致性。 
>- 从/向 Azure Blob 或 Azure Data Lake Storage Gen2 复制文件时，ADF 会利用 [Azure Blob API](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions?view=azure-dotnet-legacy) 和 [Azure Data Lake Storage Gen2 API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update#request-headers) 来进行块级 MD5 校验和验证。 如果 Azure Blob 或 Azure Data Lake Storage Gen2 上存在用作数据源的基于文件的 ContentMD5，则 ADF 在读取文件后也会进行文件级 MD5 校验和验证。 将文件复制到作为数据目标的 Azure Blob 或 Azure Data Lake Storage Gen2 之后，ADF 会将 ContentMD5 写入 Azure Blob 或 Azure Data Lake Storage Gen2，下游应用程序可以进一步使用 ContentMD5 进行数据一致性验证。
>- ADF 在任何存储区存储之间复制文件时，会进行文件大小验证。

## <a name="monitoring"></a>监视

### <a name="output-from-copy-activity"></a>自定义活动的输出
复制活动完全运行后，可以从每个复制活动运行的输出中看到数据一致性验证的结果：

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
可以从“dataConsistencyVerification 属性”中查看数据一致性验证的详细信息。

VerificationResult 的值： 
-   **已验证**：已验证复制的数据在源存储和目标存储之间是否一致。 
-   **未验证**：由于未在复制活动中启用 validateDataConsistency，因此尚未验证复制的数据是否一致。 
-   **不支持**：尚未验证复制的数据是否一致，因为此特定副本对不支持数据一致性验证。 

InconsistentData 的值： 
-   **已发现**：ADF 复制活动发现了不一致的数据。 
-   **已跳过**：ADF 复制活动发现并跳过了不一致的数据。 
-   **无**：ADF 复制活动未发现任何不一致的数据。 这可能是因为已验证你的数据在源存储和目标存储之间是一致的，也可能是因为你在复制活动中禁用了 validateDataConsistency。 

### <a name="session-log-from-copy-activity"></a>复制活动的会话日志

如果配置为记录不一致的文件，则可以从以下路径找到日志文件：`https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv`。  日志文件只能是 csv 文件。 

日志文件的架构如下所示：

列 | 说明 
-------- | -----------  
Timestamp | ADF 跳过不一致文件时的时间戳。
Level | 此项的日志级别。 对于显示文件跳过的项，它将处于“警告”级别。
OperationName | 每个文件上的 ADF 复制活动操作行为。 它将为“FileSkip”，以指定要跳过的文件。
OperationItem | 要跳过的文件名。
消息 | 说明为何要跳过文件的详细信息。

日志文件的示例如下所示： 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
```
从上面的日志文件中，你可以看到 sample1.csv 已跳过，因为无法验证它在源存储和目标存储之间是否一致。 你可以获取有关 sample1.csv 变得不一致的原因的更多详细信息，这是因为当 ADF 复制活动同时复制时，其他应用程序正在更改 sample1.csv。 



## <a name="next-steps"></a>后续步骤
请参阅其他复制活动文章：

- [复制活动概述](copy-activity-overview.md)
- [复制活动容错](copy-activity-fault-tolerance.md)


