---
title: Azure 数据工厂中的二进制格式
description: 本主题介绍了如何处理 Azure 数据工厂中的二进制格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jingwang
ms.openlocfilehash: 297fb51dd1dd8f1dabdcf2fe9e0d2ead5c906c6f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531825"
---
# <a name="binary-format-in-azure-data-factory"></a>Azure 数据工厂中的二进制格式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

以下连接器支持二进制格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [azure 文件存储](connector-azure-file-storage.md)、 [文件系统](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和 [SFTP](connector-sftp.md)。

可以在 [Copy 活动](copy-activity-overview.md)、[GetMetadata 活动](control-flow-get-metadata-activity.md)或 [Delete 活动](delete-activity.md)中使用二进制数据集。 使用二进制数据集时，ADF 不会分析文件内容，而是将其按原样处理。 

>[!NOTE]
>在复制活动中使用二进制数据集时，只能从二进制数据集复制到二进制数据集。

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供二进制数据集支持的属性列表。

| 属性         | 说明                                                  | 必须 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 数据集的 type 属性必须设置为 **Binary**。 | 是      |
| location         | 文件的位置设置。 每个基于文件的连接器在 `location` 下都有其自己的位置类型和支持的属性。 **请在连接器文章 -> 数据集属性部分中查看详细信息**。 | 是      |
| compression | 用来配置文件压缩的属性组。 如果需要在活动执行期间进行压缩/解压缩，请配置此部分。 | 否 |
| type | 用于读取/写入二进制文件的压缩编解码器。 <br>允许的值为 **bzip2**、 **gzip**、 **deflate**、 **ZipDeflate**或 **TarGzip**。 <br>**请注意**，在使用复制活动将**ZipDeflate** / **TarGzip**文件解压缩 (s) 并写入基于文件的接收器数据存储时，默认情况下，将文件提取到该文件夹： `<path specified in dataset>/<folder named as source compressed file>/` ，使用 `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` [复制活动源](#binary-as-source)控制是否保留压缩文件的名称， (s) 为文件夹结构。| 否       |
| level | 压缩率。 在 Copy 活动接收器中使用数据集时应用。<br>允许的值为 **Optimal** 或 **Fastest**。<br>- **Fastest**：尽快完成压缩操作，不过，无法以最佳方式压缩生成的文件。<br>- **Optimal**：以最佳方式完成压缩操作，不过，需要耗费更长的时间。 有关详细信息，请参阅 [Compression Level](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx)（压缩级别）主题。 | 否       |

下面是 Azure Blob 存储上的二进制数据集的示例：

```json
{
    "name": "BinaryDataset",
    "properties": {
        "type": "Binary",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供二进制源和接收器支持的属性列表。

>[!NOTE]
>在复制活动中使用二进制数据集时，只能从二进制数据集复制到二进制数据集。

### <a name="binary-as-source"></a>二进制文件作为源

复制活动的 ***\*source\**** 节支持以下属性。

| 属性      | 说明                                                  | 必须 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为 **BinarySource**。 | 是      |
| formatSettings | 一组属性。 请参阅下面的“二进制读取设置”表。 | 否       |
| storeSettings | 有关如何从数据存储读取数据的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自己支持的读取设置。 **请在连接器文章 -> 复制活动属性部分中查看详细信息**。 | 否       |

`formatSettings` 下支持的“二进制读取设置”：

| 属性      | 说明                                                  | 必须 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | formatSettings 的 type 必须设置为“BinaryReadSettings”。 | 是      |
| compressionProperties | 一组属性，指示如何为给定的压缩编解码器解压缩数据。 | 否       |
| preserveZipFileNameAsFolder<br> (*下 `compressionProperties` -> `type`) `ZipDeflateReadSettings` * | 当输入数据集配置了 ZipDeflate 压缩时适用。 指示是否在复制过程中以文件夹结构形式保留源 zip 文件名。<br>- 当设置为 true（默认值）时，数据工厂会将已解压缩的文件写入 `<path specified in dataset>/<folder named as source zip file>/`。<br>- 当设置为 false 时，数据工厂会直接将未解压缩的文件写入 `<path specified in dataset>`。 请确保在不同的源 zip 文件中没有重复的文件名，以免出现赛车或意外的行为。  | 否 |
| preserveCompressionFileNameAsFolder<br> (*下 `compressionProperties` -> `type`) `TarGZipReadSettings` * | 当输入数据集配置了 **TarGzip** 压缩时适用。 指示是否在复制过程中将源压缩文件名保留为文件夹结构。<br>-如果设置为 **true (默认) **，数据工厂会将解压缩的文件写入 `<path specified in dataset>/<folder named as source compressed file>/` 。 <br>-当设置为 **false**时，数据工厂会将解压缩的文件直接写入到 `<path specified in dataset>` 中。 请确保在不同的源文件中没有重复的文件名，以免产生赛车或意外的行为。 | 否 |

```json
"activities": [
    {
        "name": "CopyFromBinary",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true,
                    "deleteFilesAfterCompletion": true
                },
                "formatSettings": {
                    "type": "BinaryReadSettings",
                    "compressionProperties": {
                        "type": "ZipDeflateReadSettings",
                        "preserveZipFileNameAsFolder": false
                    }
                }
            },
            ...
        }
        ...
    }
]
```

### <a name="binary-as-sink"></a>二进制文件作为接收器

复制活动的 ***\*sink\**** 节支持以下属性。

| 属性      | 说明                                                  | 必须 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为 **BinarySink**。 | 是      |
| storeSettings | 有关如何将数据写入到数据存储的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自身支持的写入设置。 **请在连接器文章 -> 复制活动属性部分中查看详细信息**。 | 否       |

## <a name="next-steps"></a>后续步骤

- [复制活动概述](copy-activity-overview.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
- [Delete 活动](delete-activity.md)
