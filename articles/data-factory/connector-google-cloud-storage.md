---
title: 使用 Azure 数据工厂从 Google Cloud Storage 复制数据
description: 了解如何使用 Azure 数据工厂将数据从 Google Cloud Storage 复制到受支持的接收器数据存储。
services: data-factory
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: jingwang
ms.openlocfilehash: f6b488fc4817e360ed512441f67d2e00356e6134
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758599"
---
# <a name="copy-data-from-google-cloud-storage-using-azure-data-factory"></a>使用 Azure 数据工厂从 Google Cloud Storage 复制数据
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何从 Google Cloud Storage (GCS) 复制数据。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Google Cloud Storage 连接器：

- 包含[支持的源/接收器矩阵](copy-activity-overview.md)的 [Copy 活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
- [Delete 活动](delete-activity.md)

具体而言，此 Google 云存储连接器支持按原样复制文件，或者使用[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)分析文件。 它利用 GCS 的 S3 兼容互操作性。

## <a name="prerequisites"></a>先决条件

需要在 Google Cloud Storage 帐户中设置以下各项：

1. 为 Google Cloud Storage 帐户启用互操作性
2. 设置默认项目，使其中包含要从目标 GCS 桶复制的数据
3. 使用 GCP 上的云 IAM 创建服务帐户并定义适当的权限级别 
4. 为此服务帐户生成访问密钥

![检索 Google Cloud Storage 的访问密钥](media/connector-google-cloud-storage/google-storage-cloud-settings.png)

## <a name="required-permissions"></a>所需的权限

若要从 Google Cloud Storage 复制数据，请确保已授予所需的权限。 在服务帐户中定义的权限可能包含用于执行对象操作的 `storage.buckets.get`、`storage.buckets.list` 和 `storage.objects.get`。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

以下各部分详细介绍了定义特定于 Google 云存储的数据工厂实体时使用的属性。

## <a name="linked-service-properties"></a>链接服务属性

Google 云存储链接服务支持以下属性：

| properties | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **GoogleCloudStorage**。 | 是 |
| accessKeyId | 机密访问键 ID。 若要查找访问密钥和机密，请参阅[先决条件](#prerequisites)。 |是 |
| secretAccessKey | 机密访问键本身。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 |是 |
| serviceUrl | 将自定义 GCS 终结点指定为 **`https://storage.googleapis.com`** 。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure Integration Runtime 或自承载集成运行时。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

以下是示例：

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "GoogleCloudStorage",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "serviceUrl": "https://storage.googleapis.com"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在基于格式的数据集中的 `location` 设置下，Google Cloud Storage 支持以下属性：

| properties   | 说明                                                  | 必选 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 数据集中 `location` 下的 type 属性必须设置为 **GoogleCloudStorageLocation**。 | 是      |
| bucketName | GCS 桶名称。                                          | 是      |
| folderPath | 给定桶下的文件夹的路径。 若要使用通配符来筛选文件夹，请跳过此设置，并在活动源设置中进行指定。 | 否       |
| fileName   | 给定桶和 folderPath 下的文件名。 若要使用通配符来筛选文件，请跳过此设置，并在活动源设置中进行指定。 | 否       |

**示例：**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Google Cloud Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "GoogleCloudStorageLocation",
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

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供了 Google Cloud Storage 源支持的属性列表。

### <a name="google-cloud-storage-as-source"></a>Google Cloud Storage 用作源

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在基于格式的复制源中的 `storeSettings` 设置下，Google Cloud Storage 支持以下属性：

| properties                 | 说明                                                  | 必选                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | `storeSettings` 下的 type 属性必须设置为 **GoogleCloudStorageReadSettings**。 | 是                                                         |
| 找到要复制的文件： |  |  |
| 选项 1：静态路径<br> | 从数据集中指定的给定存储桶或文件夹/文件路径复制。 若要复制存储桶/文件夹中的所有文件，请另外将 `wildcardFileName` 指定为 `*`。 |  |
| 选项 2：GCS 前缀<br>- prefix | 数据集中配置的给定存储桶下的 GCS 密钥名称的前缀，用于筛选源 GCS 文件。 选择名称以 `bucket_in_dataset/this_prefix` 开头的 GCS 密钥。 它利用 GCS 的服务端筛选器，相较于通配符筛选器，该筛选器可提供更好的性能。 | 否 |
| 选项 3：通配符<br>- wildcardFolderPath | 数据集中配置的给定存储桶下包含通配符的文件夹路径，用于筛选源文件夹。 <br>允许的通配符为：`*`（匹配零个或更多个字符）和 `?`（匹配零个或单个字符）；如果实际文件夹名称中包含通配符或此转义字符，请使用 `^` 进行转义。 <br>请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 否                                            |
| 选项 3：通配符<br>- wildcardFileName | 给定桶和 folderPath/wildcardFolderPath 下带有通配符的文件名，用于筛选源文件。 <br>允许的通配符为：`*`（匹配零个或更多个字符）和 `?`（匹配零个或单个字符）；如果实际文件夹名称中包含通配符或此转义字符，请使用 `^` 进行转义。  请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 是 |
| 选项 3：文件列表<br>- fileListPath | 指明复制给定的文件集。 指向包含要复制的文件列表的文本文件，其中每行一个文件，即在数据集中配置的路径的相对路径。<br/>使用此选项时，请不要在数据集中指定文件名。 请参阅[文件列表示例](#file-list-examples)中的更多示例。 |否 |
| 其他设置： |  | |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 请注意，当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹或子文件夹。 <br>允许的值为 **true**（默认值）和 **false**。<br>如果你配置了 `fileListPath`，则此属性不适用。 |否 |
| modifiedDatetimeStart    | 基于属性“上次修改时间”的文件筛选器。 <br>如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br> 属性可以为 NULL，这意味着不向数据集应用任何文件特性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。<br/>如果你配置了 `fileListPath`，则此属性不适用。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                                          |
| maxConcurrentConnections | 同时连接到存储的连接数。 仅当要限制连接到数据存储的并发连接时，才指定此属性。 | 否                                                          |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "GoogleCloudStorageReadSettings",
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

### <a name="folder-and-file-filter-examples"></a>文件夹和文件筛选器示例

本部分介绍使用通配符筛选器生成文件夹路径和文件名的行为。

| Bucket | key | recursive | 源文件夹结构和筛选器结果（用粗体表示的文件已检索）|
|:--- |:--- |:--- |:--- |
| Bucket | `Folder*/*` | false | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| Bucket | `Folder*/*` | true | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| Bucket | `Folder*/*.csv` | false | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| Bucket | `Folder*/*.csv` | true | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>文件列表示例

本部分介绍了在复制活动源中使用文件列表路径时的结果行为。

假设有以下源文件夹结构，并且要复制加粗显示的文件：

| 示例源结构                                      | FileListToCopy.txt 中的内容                             | ADF 配置                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;元数据<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | 在数据集中：<br>- 桶：`bucket`<br>- 文件夹路径：`FolderA`<br><br>在复制活动源中：<br>- 文件列表路径：`bucket/Metadata/FileListToCopy.txt` <br><br>文件列表路径指向同一数据存储中的一个文本文件，该文件包含要复制的文件列表（每行一个文件）以及相对于在数据集中配置的路径的相对路径。 |

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要详细了解这些属性，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活动属性

若要详细了解这些属性，请查看 [GetMetadata 活动](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Delete 活动属性

若要详细了解这些属性，请查看 [Delete 活动](delete-activity.md)

## <a name="legacy-models"></a>旧模型

如果你过去使用 Amazon S3 连接器从 Google Cloud Storage 复制数据，为了后向兼容，现在仍支持这种操作。 建议使用上面几个部分中提到的新模型，并且 ADF 创作 UI 已经切换为生成新模型。

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中的复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
