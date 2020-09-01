---
title: 从 Amazon 简单存储服务 (S3) 复制数据
description: 了解如何使用 Azure 数据工厂将数据从 Amazon 简单存储服务 (S3) 复制到受支持的接收器数据存储。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/31/2020
ms.openlocfilehash: 584aafccf146f3f261a7d375ecb57dcde18d03d5
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182529"
---
# <a name="copy-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 Amazon 简单存储服务复制数据
> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
>
> * [版本 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [当前版本](connector-amazon-simple-storage-service.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何从 Amazon 简单存储服务 (Amazon S3) 复制数据。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。

>[!TIP]
>若要详细了解从 Amazon S3 到 Azure 存储的数据迁移方案，请参阅[使用 Azure 数据工厂将数据从 Amazon S3 迁移到 Azure 存储](data-migration-guidance-s3-azure-storage.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Amazon S3 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
- [Delete 活动](delete-activity.md)

具体而言，此 Amazon S3 连接器支持按原样复制文件，或者使用[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)分析文件。 还可以选择[在复制期间保留文件元数据](#preserve-metadata-during-copy)。 此连接器使用 [AWS 签名版本 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) 对发往 S3 的请求进行身份验证。

>[!TIP]
>可以使用此 Amazon S3 连接器从任何兼容 S3 的存储提供程序（例如 [Google 云存储](connector-google-cloud-storage.md)）复制数据。 在链接的服务配置中指定相应的服务 URL。

## <a name="required-permissions"></a>所需的权限

若要从 Amazon S3 复制数据，请确保已具有以下权限：

- **执行复制活动**：Amazon S3 对象操作所需的 `s3:GetObject` 和 `s3:GetObjectVersion` 权限。
- **数据工厂 GUI 创作**：Amazon S3 Bucket 操作所需的 `s3:ListAllMyBuckets` 和 `s3:ListBucket`/`s3:GetBucketLocation` 权限。 测试连接和浏览到文件路径等操作也需要权限。 如果不想授予这些权限，请跳过链接服务创建页面中的测试连接，直接在数据集设置中指定路径。

如需 Amazon S3 权限的完整列表，请参阅 AWS 站点上的[在策略中指定权限](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html)。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

对于特定于 Amazon S3 的数据工厂实体，以下部分提供了有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Amazon S3 链接服务支持以下属性：

| 属性 | 描述 | 必需 |
|:--- |:--- |:--- |
| type | **type** 属性必须设置为 **AmazonS3**。 | 是 |
| accessKeyId | 机密访问键 ID。 |是 |
| secretAccessKey | 机密访问键本身。 将此字段标记为 **SecureString** 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 |是 |
| serviceUrl | 如果从官方 Amazon S3 服务之外的兼容 S3 的存储提供程序复制数据，请指定自定义 S3 终结点。 例如，若要从 Google 云存储复制数据，请指定 `https://storage.googleapis.com`。 | 否 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络中）。 如果未指定此属性，服务会使用默认的 Azure Integration Runtime。 |否 |

此连接器需要 AWS 标识和访问管理 (IAM) 帐户的访问密钥才能从 Amazon S3 复制数据。 目前不支持[临时安全凭据](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html)。

>[!TIP]
>如果从官方 Amazon S3 服务之外的兼容 S3 的存储复制数据，请指定自定义 S3 服务 URL。

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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Amazon S3 支持基于格式的数据集中 `location` 设置下的以下属性：

| 属性   | 描述                                                  | 必需 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 数据集中 `location` 下的 **type** 属性必须设置为 **AmazonS3Location**。 | 是      |
| bucketName | S3 存储桶的名称。                                          | 是      |
| folderPath | 给定 Bucket 下的文件夹路径。 如果要使用通配符来筛选文件夹，请跳过此设置并在活动源设置中进行相应的指定。 | 否       |
| fileName   | 给定 Bucket 和文件夹路径下的文件名。 如果要使用通配符来筛选文件，请跳过此设置并在活动源设置中进行相应的指定。 | 否       |
| 版本 | 启用 S3 版本控制时 S3 对象的版本。 如果未指定，则会提取最新版本。 |否 |

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

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Amazon S3 源支持的属性列表。

### <a name="amazon-s3-as-a-source-type"></a>以 Amazon S3 作为源类型

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Amazon S3 支持基于格式的复制源中 `storeSettings` 设置下的以下属性：

| 属性                 | 描述                                                  | 必需                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | `storeSettings` 下的 **type** 属性必须设置为 **AmazonS3ReadSettings**。 | 是                                                         |
| 找到要复制的文件： |  |  |
| 选项 1：静态路径<br> | 从数据集中指定的给定存储桶或文件夹/文件路径复制。 若要复制 Bucket 或文件夹中的所有文件，请另外将 `wildcardFileName` 指定为 `*`。 |  |
| 选项 2：S3 前缀<br>- prefix | 数据集中配置的给定 Bucket 下的 S3 密钥名称的前缀，用于筛选源 S3 文件。 名称以 `bucket_in_dataset/this_prefix` 开头的 S3 密钥会被选中。 它利用 S3 的服务端筛选器。与通配符筛选器相比，该服务端筛选器可提供更好的性能。 | 否 |
| 选项 3：通配符<br>- wildcardFolderPath | 数据集中配置的给定 Bucket 下包含通配符的文件夹路径，用于筛选源文件夹。 <br>允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。 如果文件夹名内包含通配符或此转义字符，请使用 `^` 进行转义。 <br>请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 否                                            |
| 选项 3：通配符<br>- wildcardFileName | 给定 Bucket 和文件夹路径（或通配符文件夹路径）下包含通配符的文件名，用于筛选源文件。 <br>允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。 如果文件夹名内包含通配符或此转义字符，请使用 `^` 进行转义。  请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 是 |
| 选项 4：文件列表<br>- fileListPath | 指明复制给定文件集。 指向包含要复制的文件列表的文本文件，其中每行一个文件（即数据集中所配置路径的相对路径）。<br/>使用此选项时，请不要在数据集中指定文件名。 请参阅[文件列表示例](#file-list-examples)中的更多示例。 |否 |
| 其他设置： |  | |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 请注意，当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹或子文件夹。 <br>允许的值为 **true**（默认值）和 **false**。<br>如果配置 `fileListPath`，则此属性不适用。 |否 |
| deleteFilesAfterCompletion | 指示是否会在二进制文件成功移到目标存储后将其从源存储中删除。 文件删除按文件进行。因此，当复制活动失败时，你会看到一些文件已经复制到目标并从源中删除，而另一些文件仍保留在源存储中。 <br/>此属性仅在二进制复制方案中有效，其中数据源存储为 Blob、ADLS Gen1、ADLS Gen2、S3、Google 云存储、文件、Azure 文件、SFTP 或 FTP。 默认值：false。 |否 |
| modifiedDatetimeStart    | 文件根据“上次修改时间”属性进行筛选。 <br>如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br> 属性可以为 **NULL**，这意味着不会向数据集应用任何文件属性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则会选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则会选中“上次修改时间”属性小于该日期/时间值的文件。<br/>如果配置 `fileListPath`，则此属性不适用。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                                          |
| enablePartitionDiscovery | 对于已分区的文件，指定是否分析文件路径中的分区，并将其添加为其他源列。<br/>允许的值为 **false** (默认值) 为 **true**。 | 否                                            |
| partitionRootPath | 启用分区发现时，请指定绝对根路径，以便将分区文件夹作为数据列进行读取。<br/><br/>如果未指定，则默认情况下，<br/>-在数据集或源中的文件列表中使用文件路径时，分区根路径是在数据集中配置的路径。<br/>-使用通配符文件夹筛选器时，"分区根路径" 是第一个通配符之前的子路径。<br/>-使用前缀时，分区根路径是最后一个 "/" 之前的子路径。 <br/><br/>例如，假设你将数据集中的路径配置为 "根/文件夹/年 = 2020/month = 08/day = 27"：<br/>-如果将分区根路径指定为 "root/folder/year = 2020"，则除了文件中的列外，复制活动还将分别生成另外两个列 `month` 和 `day` 值 "08" 和 "27"。<br/>-如果未指定分区根路径，则不会生成额外的列。 | 否                                            |
| maxConcurrentConnections | 与数据存储的并发连接数。 仅在要限制与数据存储的并发连接数时指定。 | 否                                                          |

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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AmazonS3ReadSettings",
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
| Bucket | `Folder*/*` | 是 | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| Bucket | `Folder*/*.csv` | false | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| Bucket | `Folder*/*.csv` | 是 | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>文件列表示例

本部分介绍了在复制活动源中使用文件列表路径时产生的行为。

假设有以下源文件夹结构，并且要复制以粗体显示的文件：

| 示例源结构                                      | FileListToCopy.txt 中的内容                             | 数据工厂配置                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;元数据<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | 在数据集中：<br>- 桶：`bucket`<br>- 文件夹路径：`FolderA`<br><br>在复制活动源中：<br>- 文件列表路径：`bucket/Metadata/FileListToCopy.txt` <br><br>文件列表路径指向同一数据存储中的一个文本文件，该文件包含要复制的文件列表（每行一个文件，带有数据集中所配置路径的相对路径）。 |

## <a name="preserve-metadata-during-copy"></a>在复制期间保留元数据

将文件从 Amazon S3 复制到 Azure Data Lake Storage Gen2 或 Azure Blob 存储时，可以选择将文件元数据与数据一起保留。 从[保留元数据](copy-activity-preserve-metadata.md#preserve-metadata)中了解更多信息。

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活动属性

若要了解有关属性的详细信息，请查看 [GetMetadata 活动](control-flow-get-metadata-activity.md)。 

## <a name="delete-activity-properties"></a>Delete 活动属性

若要了解有关属性的详细信息，请查看[删除活动](delete-activity.md)。

## <a name="legacy-models"></a>旧模型

>[!NOTE]
>仍会按原样支持以下模型，以实现后向兼容性。 建议使用前面提到的新模型。 数据工厂创作 UI 已切换为生成新模型。

### <a name="legacy-dataset-model"></a>旧数据集模型

| 属性 | 描述 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 **type** 属性必须设置为 **AmazonS3Object**。 |是 |
| bucketName | S3 存储桶的名称。 通配符筛选器不受支持。 |对于复制或查找活动为“是”，对于 GetMetadata 活动为“否” |
| key | 指定 Bucket 下的 S3 对象键的名称或通配符筛选器。 仅当未指定 **prefix** 属性时应用。 <br/><br/>文件夹部分和文件名部分都支持通配符筛选器。 允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。<br/>- 示例 1：`"key": "rootfolder/subfolder/*.csv"`<br/>- 示例 2：`"key": "rootfolder/subfolder/???20180427.txt"`<br/>请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 如果实际文件夹名或文件名内具有通配符或此转义字符，请使用 `^` 进行转义。 |否 |
| 前缀 | S3 对象键的前缀。 已选中其键以该前缀开头的对象。 仅当未指定 **key** 属性时应用。 |否 |
| 版本 | 启用 S3 版本控制时 S3 对象的版本。 如果未指定版本，则会提取最新版本。 |否 |
| modifiedDatetimeStart | 文件根据“上次修改时间”属性进行筛选。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 请注意，要对大量文件进行筛选时，启用此设置会影响数据移动的整体性能。 <br/><br/> 属性可以为 **NULL**，这意味着不会向数据集应用任何文件属性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则会选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则会选中“上次修改时间”属性小于该日期/时间值的文件。| 否 |
| modifiedDatetimeEnd | 文件根据“上次修改时间”属性进行筛选。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 请注意，要对大量文件进行筛选时，启用此设置会影响数据移动的整体性能。 <br/><br/> 属性可以为 **NULL**，这意味着不会向数据集应用任何文件属性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则会选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则会选中“上次修改时间”属性小于该日期/时间值的文件。| 否 |
| format | 若要在基于文件的存储之间按原样复制文件（二进制副本），可以在输入和输出数据集定义中跳过格式节。<br/><br/>若要分析或生成具有特定格式的文件，以下是受支持的文件格式类型：TextFormat、JsonFormat、AvroFormat、OrcFormat、ParquetFormat    。 请将 **format** 中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[JSON 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)部分。 |否（仅适用于二进制复制方案） |
| compression | 指定数据的压缩类型和级别。 有关详细信息，请参阅[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支持的类型为 **GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。<br/>支持的级别为“最佳”和“最快”。 |否 |

>[!TIP]
>若要复制文件夹下的所有文件，请使用 Bucket 的名称指定“bucketName”并使用文件夹部分指定“prefix” 。
>
>若要复制具有给定名称的单个文件，请使用 Bucket 的名称指定“bucketName”并使用文件夹部分及文件名指定“key”。
>
>若要复制文件夹下的文件子集，请使用 Bucket 的名称指定“bucketName”并使用文件夹部分及通配符筛选器指定“key” 。

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

### <a name="legacy-source-model-for-the-copy-activity"></a>复制活动的旧源模型

| 属性 | 描述 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动源的 **type** 属性必须设置为 **FileSystemSource**。 |是 |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 请注意，当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹或子文件夹。<br/>允许的值为 **true**（默认值）和 **false**。 | 否 |
| maxConcurrentConnections | 可以同时连接到数据存储的连接数。 仅在要限制与数据存储的并发连接数时指定。 | 否 |

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

## <a name="next-steps"></a>后续步骤
有关数据存储（Azure 数据工厂中的复制活动支持将其用作源和接收器）的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
