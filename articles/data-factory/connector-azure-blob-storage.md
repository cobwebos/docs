---
title: 使用数据工厂向/从 Azure Blob 存储复制数据 | Microsoft Docs
description: 了解如何使用数据工厂将数据从支持的源数据存储复制到 Azure Blob 存储，或从 Blob 存储复制到受支持的接收器数据存储。
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: b0eca20a801bbe431eff8ed0accffe2705d6c41c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>使用 Azure 数据工厂向/从 Azure Blob 存储复制数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](v1/data-factory-azure-blob-connector.md)
> * [版本 2 - 预览版](connector-azure-blob-storage.md)

本文概述如何使用 Azure 数据工厂中的复制活动向/从 Azure Blob 存储复制数据。 本文是根据总体概述复制活动的[复制活动概述](copy-activity-overview.md)一文编写的。

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用版本 1 的数据工厂服务，请参阅[版本 1 中的 Blob 存储连接器](v1/data-factory-azure-blob-connector.md)。


## <a name="supported-capabilities"></a>支持的功能

可将数据从任一支持的源数据存储复制到 Azure Blob 存储。 也可以将数据从 Azure Blob 存储复制到任一支持的接收器数据存储。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md)表。

具体而言，此 Blob 存储连接器支持：

- 向/从常规用途的 Azure 存储帐户和热/冷 Blob 存储复制 Blob。 
- 使用帐户密钥和服务共享访问签名身份验证复制 Blob。
- 从块、追加或页 Blob 中复制 Blob，并将数据仅复制到块 Blob。 不支持将 Azure 高级存储用作接收器，因为它由页 Blob 支持。
- 按原样复制 Blob，或者使用[支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)分析或生成 Blob。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Blob 存储的数据工厂实体，以下部分提供了有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

### <a name="use-an-account-key"></a>使用帐户密钥

可以使用帐户密钥创建存储链接服务。 该链接服务将存储的全局访问权限提供给数据工厂。 支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **AzureStorage**。 |是 |
| connectionString | 为 connectionString 属性指定连接到存储所需的信息。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 |是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure 集成运行时或自承载集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 |否 |

**示例：**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-service-shared-access-signature-authentication"></a>使用服务共享访问签名身份验证

还可以使用共享访问签名创建存储链接服务。 这样，便可以将存储中所有/特定资源（Blob/容器）的受限/限时访问权限提供给数据工厂。

共享访问签名对存储帐户中的资源提供委托访问。 使用共享访问签名可以在指定的时间内授予客户端对存储帐户中对象的有限访问权限。 无需共享帐户访问密钥。 共享访问签名是一个 URI，在其查询参数中包含对存储资源已验证访问所需的所有信息。 若要使用共享访问签名访问存储资源，客户端只需将共享访问签名传入到相应的构造函数或方法。 有关共享访问签名的详细信息，请参阅[共享访问签名：了解共享访问签名模型](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。

> [!IMPORTANT]
> 数据工厂目前仅支持服务共享访问签名，而不支持帐户共享访问签名。 有关这两种类型及其构建方式的详细信息，请参阅[共享访问签名的类型](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures)。 通过 Azure 门户或 Azure 存储资源管理器生成的共享访问签名 URL 是不受支持的帐户共享访问签名。

> [!TIP]
> 可执行以下 PowerShell 命令为存储帐户生成服务共享访问签名。 请替换占位符并授予所需的权限。
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

若要使用服务共享访问签名身份验证，需支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **AzureStorage**。 |是 |
| sasUri | 指定存储资源（例如 Blob、容器或表）的共享访问签名 URI。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 |是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure 集成运行时或自承载集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 |否 |

**示例：**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

在创建共享访问签名 URI 时，请注意以下几点：

- 根据链接服务（读取、写入、读/写）在数据工厂中的用法，设置针对对象的适当读/写权限。
- 根据需要设置“到期时间”。 确保存储对象的访问权限不会在管道的活动期限内过期。
- 应该根据需要在正确的容器/Blob 或表级别创建 URI。 数据工厂可以使用 Blob 的共享访问签名 URI 访问该特定 Blob。 数据工厂可以使用 Blob 存储容器的共享访问签名 URI 迭代该容器中的 Blob。 以后若要提供更多/更少对象的访问权限或需要更新共享访问签名 URI，请记得使用新 URI 更新链接服务。

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Blob 存储数据集支持的属性列表。

若要向/从 Blob 存储复制数据，请将数据集的 type 属性设置为 **AzureBlob**。 支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 **AzureBlob**。 |是 |
| folderPath | Blob 存储中的容器和文件夹的路径。 示例：myblobcontainer/myblobfolder/。 |是 |
| fileName | 若要向/从特定 Blob 复制，请在 **folderPath** 中指定 Blob 名称。 如果没有为此属性指定任何值，则数据集会指向文件夹中的所有 Blob。<br/><br/>如果没有为输出数据集指定 fileName，并且没有在活动接收器中指定 **preserveHierarchy**，则复制活动会自动生成采用以下格式的 Blob 名称：`Data.[activity run id GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]`。 例如 `Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz`。 |否 |
| 格式 | 若要在基于文件的存储之间按原样复制文件（二进制副本），可以在输入和输出数据集定义中跳过格式节。<br/><br/>如果想要分析或生成具有特定格式的文件，则下面是支持的文件格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 请将 **format** 中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](supported-file-formats-and-compression-codecs.md#text-format)、[JSON 格式](supported-file-formats-and-compression-codecs.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs.md#parquet-format)部分。 |否（仅适用于二进制复制方案） |
| compression | 指定数据的压缩类型和级别。 有关详细信息，请参阅[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md#compression-support)。<br/>支持的类型为 **GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。<br/>支持的级别为“最佳”和“最快”。 |否 |

**示例：**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName": "myfile.csv.gz",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Blob 存储源和接收器支持的属性列表。

### <a name="blob-storage-as-a-source-type"></a>将 Blob 存储用作源类型

若要从 Blob 存储复制数据，请将复制活动中的源类型设置为 **BlobSource**。 复制活动的 **source** 节支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为 **BlobSource**。 |是 |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 请注意，当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹或子文件夹。<br/>允许的值为 **true**（默认值）和 **false**。 | 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="blob-storage-as-a-sink-type"></a>用作接收器类型的 Blob 存储

若要向 Blob 存储复制数据，请将复制活动中的接收器类型设置为 **BlobSink**。 **sink** 节支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为 **BlobSink**。 |是 |
| copyBehavior | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认值）</b>：保留目标文件夹中的文件层次结构。 从源文件到源文件夹的相对路径与从目标文件到目标文件夹的相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定了文件名或 Blob 名称，则合并文件的名称为指定名称。 否则，它是自动生成的文件名。 | 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="some-recursive-and-copybehavior-examples"></a>一些 recursive 和 copyBehavior 示例

本节介绍了将 recursive 和 copyBehavior 值进行不同组合所产生的复制操作行为。

| recursive | copyBehavior | 源文件夹结构 | 生成目标 |
|:--- |:--- |:--- |:--- |
| 是 |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用与源相同的结构创建目标文件夹 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| 是 |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 的自动生成的名称 |
| 是 |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 的内容将合并到一个文件中，且自动生成文件名。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标文件夹 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>不会选取带有 File3、File4 和 File5 的 Subfolder1。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标文件夹 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成的名称<br/><br/>不会选取带有 File3、File4 和 File5 的 Subfolder1。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标文件夹 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的内容将合并到一个文件中，且自动生成文件名。 File1 的自动生成的名称<br/><br/>不会选取带有 File3、File4 和 File5 的 Subfolder1。 |

## <a name="next-steps"></a>后续步骤
有关数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
