---
title: 使用 Azure 数据工厂从 Amazon 简单存储服务 (S3) 复制数据 | Microsoft Docs
description: 了解如何使用 Azure 数据工厂将数据从 Amazon 简单存储服务 (S3) 复制到受支持的接收器数据存储。
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: 7b47aadc28a5e2ea6dbf2a7a8a23cdb713a0b981
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839885"
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>使用 Azure 数据工厂从 Amazon 简单存储服务复制数据
> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
>
> * [版本 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [当前版本](connector-amazon-simple-storage-service.md)

本文概述了如何从 Amazon 简单存储服务 (Amazon S3) 复制数据。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Amazon S3 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)

具体而言，此 Amazon S3 连接器支持按原样复制文件，或者使用[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)分析文件。 它使用 [AWS 签名版本 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) 对发往 S3 的请求进行身份验证。

>[!TIP]
>可以使用此 Amazon S3 连接器从**任何兼容 S3 的存储提供程序**（例如 [Google 云存储](connector-google-cloud-storage.md)）复制数据。 在链接的服务配置中指定相应的服务 URL。

## <a name="required-permissions"></a>所需权限

若要从 Amazon S3 复制数据，请确保已具有以下权限：

- **复制活动执行情况**：Amazon S3 对象操作的 `s3:GetObject` 和 `s3:GetObjectVersion` 权限。
- **数据工厂 GUI 创作**：对于测试连接以及浏览/导航文件路径等操作，额外需要适用于 Amazon S3 Bucket 操作的 `s3:ListAllMyBuckets` and `s3:ListBucket`/`s3:GetBucketLocation` 权限。 如果不想授予这些权限，请跳过“链接服务创建”页面中的测试连接，并直接在“数据集设置”中指定路径。

可以从[在策略中指定权限](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html)中找到 Amazon S3 权限的完整列表（包含详细信息）。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

对于特定于 Amazon S3 的数据工厂实体，以下部分提供了有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Amazon S3 链接的服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为“AmazonS3”。 | 是 |
| accessKeyId | 机密访问键 ID。 |是 |
| secretAccessKey | 机密访问键本身。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 |是 |
| serviceUrl | 如果从官方 Amazon S3 服务之外的兼容 S3 的存储提供程序复制数据，请指定自定义 S3 终结点。 例如，若要从 Google 云存储复制数据，请指定 `https://storage.googleapis.com`。 | 否 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure Integration Runtime 或自承载集成运行时。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

>[!TIP]
>如果从官方 Amazon S3 服务之外的兼容 S3 的存储复制数据，请指定自定义 S3 服务 URL。

>[!NOTE]
>此连接器需要 IAM 帐户的访问密钥才能从 Amazon S3 复制数据。 不支持[临时安全凭据](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html)。
>

下面是一个示例：

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 

- 对于**Parquet (分隔文本和二进制格式**), 请参阅[Parquet, 分隔文本和二进制格式数据集](#format-based-dataset)部分。
- 对于其他格式 (如**ORC/Avro/JSON 格式**), 请参阅[其他格式数据集](#other-format-dataset)部分。

### <a name="format-based-dataset"></a>Parquet, 分隔文本和二进制格式数据集

若要从 Parquet 中的 Amazon S3 复制数据 **(以分隔的文本或二进制格式**), 请参阅基于格式的数据集和支持的设置中的[Parquet 格式](format-parquet.md)、[带分隔符的文本格式](format-delimited-text.md)和[二进制格式](format-binary.md)一文。 基于格式的数据集中 `location` 设置下的 Amazon S3 支持以下属性：

| 属性   | 说明                                                  | 必选 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 数据集中 `location` 下的 type 属性必须设置为 **AmazonS3Location**。 | 是      |
| bucketName | S3 存储桶的名称。                                          | 是      |
| folderPath | 给定存储桶下的文件夹路径。 如果要使用通配符筛选文件夹，请跳过此设置并在活动源设置中指定。 | 否       |
| fileName   | 给定存储桶 + folderPath 下的文件名。 如果要使用通配符筛选文件，请跳过此设置并在活动源设置中指定。 | 否       |
| version | 启用 S3 版本控制时 S3 对象的版本。 如果未指定，则将提取最新版本。 |否 |

> [!NOTE]
> Copy/Lookup/GetMetadata 活动仍然按原样支持下一部分中提到的带有 Parquet/Text 格式的 **AmazonS3Object** 类型数据集，以实现向后兼容，但它不适用于映射数据流。 建议你继续使用此新模型，并且 ADF 创作 UI 已切换为生成这些新类型。

**示例：**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>其他格式数据集

若要以**ORC/Avro/JSON 格式**从 Amazon S3 复制数据, 支持以下属性:

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：AmazonS3Object |是 |
| bucketName | S3 存储桶的名称。 不支持通配符筛选器。 |对于复制/查找活动，为“是”；对于 GetMetadata 活动，为“否” |
| 钥 | 指定存储桶下的 S3 对象键的“名称或通配符筛选器”。 仅当未指定“prefix”属性时应用。 <br/><br/>文件夹部分和文件名部分都支持通配符筛选器。 允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。<br/>- 示例 1：`"key": "rootfolder/subfolder/*.csv"`<br/>- 示例 2：`"key": "rootfolder/subfolder/???20180427.txt"`<br/>请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 如果实际文件夹/文件名内具有通配符或此转义符，请使用 `^` 进行转义。 |否 |
| prefix | S3 对象键的前缀。 已选中其键以该前缀开头的对象。 仅当未指定“key”属性时应用。 |否 |
| version | 启用 S3 版本控制时 S3 对象的版本。 如果未指定，则将提取最新版本。 |否 |
| modifiedDatetimeStart | 基于属性“上次修改时间”的文件筛选器。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 请注意，当你要从大量文件中进行文件筛选时，启用此设置将影响数据移动的整体性能。 <br/><br/> 属性可以为 NULL，这意味着不向数据集应用任何文件特性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。| 否 |
| modifiedDatetimeEnd | 基于属性“上次修改时间”的文件筛选器。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 请注意，当你要从大量文件中进行文件筛选时，启用此设置将影响数据移动的整体性能。 <br/><br/> 属性可以为 NULL，这意味着不向数据集应用任何文件特性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。| 否 |
| format | 如果想要在基于文件的存储之间按原样复制文件（二进制副本），可以在输入和输出数据集定义中跳过格式节。<br/><br/>若要分析或生成具有特定格式的文件，以下是受支持的文件格式类型：TextFormat、JsonFormat、AvroFormat、OrcFormat、ParquetFormat。 请将格式中的“type”属性设置为上述值之一。 有关详细信息，请参阅[文本格式](supported-file-formats-and-compression-codecs.md#text-format)、[Json 格式](supported-file-formats-and-compression-codecs.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs.md#parquet-format)部分。 |否（仅适用于二进制复制方案） |
| compression | 指定数据的压缩类型和级别。 有关详细信息，请参阅[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md#compression-support)。<br/>支持的类型包括：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。<br/>支持的级别为：“最佳”和“最快”。 |否 |

>[!TIP]
>要复制文件夹下的所有文件，请指定存储桶的“bucketName”和文件夹部分的“prefix”。<br>要复制具有给定名称的单个文件，请指定存储桶的“bucketName”和文件夹部分及文件名的“key”。<br>要复制文件夹下的文件子集，请指定存储桶的“bucketName”和文件夹部分及通配符筛选器的“key”。

**示例：使用前缀**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
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

**示例：使用键和版本（可选）**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
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

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Amazon S3 源支持的属性列表。

### <a name="amazon-s3-as-source"></a>作为源的 Amazon S3

- 若要从**Parquet 复制, 分隔文本和二进制格式**, 请参阅[Parquet、带分隔符的文本和二进制格式源](#format-based-source)部分。
- 若要从其他格式 (如**ORC/Avro/JSON 格式**) 复制, 请参阅[其他格式源](#other-format-source)部分。

#### <a name="format-based-source"></a>Parquet, 分隔文本和二进制格式源

若要从 Parquet 中的 Amazon S3 复制数据 **(以分隔的文本或二进制格式**), 请参阅[Parquet 格式](format-parquet.md)、[带分隔符的文本格式](format-delimited-text.md)和[二进制格式](format-binary.md)文章基于格式的复制活动源和支持的设置。 基于格式的复制源中 `storeSettings` 设置下的 Amazon S3 支持以下属性：

| 属性                 | 说明                                                  | 必选                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | `storeSettings` 下的 type 属性必须设置为 **AmazonS3ReadSetting**。 | 是                                                         |
| recursive                | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 请注意，当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹或子文件夹。 允许的值为 **true**（默认值）和 **false**。 | 否                                                          |
| prefix                   | 在数据集中配置的给定存储桶下的 S3 对象键的前缀，用于筛选源对象。 已选中其键以该前缀开头的对象。 <br>仅当未指定 `wildcardFolderPath` 和 `wildcardFileName` 属性时适用。 | 否                                                          |
| wildcardFolderPath       | 数据集中配置的给定存储桶下包含通配符的文件夹路径，用于筛选源文件夹。 <br>允许的通配符为：`*`（匹配零个或更多个字符）和 `?`（匹配零个或单个字符）；如果实际文件夹名称中包含通配符或此转义字符，请使用 `^` 进行转义。 <br>请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 否                                                          |
| wildcardFileName         | 给定的存储桶 + folderPath/wildcardFolderPath 下带有通配符的文件名，用于筛选源文件。 <br>允许的通配符为：`*`（匹配零个或更多个字符）和 `?`（匹配零个或单个字符）；如果实际文件夹名称中包含通配符或此转义字符，请使用 `^` 进行转义。  请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 如果未在数据集中指定 `fileName` 并且未指定 `prefix`，则为“是” |
| modifiedDatetimeStart    | 基于属性“上次修改时间”的文件筛选器。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br> 属性可以为 NULL，这意味着不向数据集应用任何文件特性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。 | 否                                                          |
| modifiedDatetimeEnd      | 同上。                                               | 否                                                          |
| maxConcurrentConnections | 可以同时连接到存储库的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否                                                          |

> [!NOTE]
> 对于 Parquet/带分隔符的文本格式，仍然按原样支持下一部分中提到的 **FileSystemSource** 类型复制活动源，以实现向后兼容性。 建议你继续使用此新模型，并且 ADF 创作 UI 已切换为生成这些新类型。

**示例：**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AmazonS3ReadSetting",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>其他格式源

若要从**ORC/Avro/JSON 格式**的 Amazon S3 复制数据, 复制活动**源**部分支持以下属性:

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：FileSystemSource |是 |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制/创建空的文件夹/子文件夹。<br/>允许的值为：true（默认）、false | 否 |
| maxConcurrentConnections | 可以同时连接到数据存储的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>文件夹和文件筛选器示例

本部分介绍使用通配符筛选器生成文件夹路径和文件名的行为。

| bucket | 钥 | recursive | 源文件夹结构和筛选器结果（用粗体表示的文件已检索）|
|:--- |:--- |:--- |:--- |
| bucket | `Folder*/*` | 假 | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*` | 真 | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | 假 | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | 真 | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中的复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
