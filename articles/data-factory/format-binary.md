---
title: Azure 数据工厂中的二进制格式 |Microsoft Docs
description: 本主题介绍如何在 Azure 数据工厂中处理二进制格式。
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: a3b3e4e873497b3b0f726d815a798919ace4f931
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68842538"
---
# <a name="binary-format-in-azure-data-factory"></a>Azure 数据工厂中的二进制格式

以下连接器支持二进制格式:[Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [azure 文件存储](connector-azure-file-storage.md)、[文件系统](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和[SFTP](connector-sftp.md)。

可以在[复制活动](copy-activity-overview.md)、 [GetMetadata 活动](control-flow-get-metadata-activity.md)或[删除活动](delete-activity.md)中使用二进制数据集。 使用二进制数据集时, ADF 不会分析文件内容, 而是按原样处理。 

>[!NOTE]
>在复制活动中使用二进制数据集时, 只能从二进制数据集复制到二进制数据集。

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供二进制数据集支持的属性列表。

| 属性         | 说明                                                  | 必选 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 数据集的 type 属性必须设置为**Binary**。 | 是      |
| location         | 文件的位置设置。 每个基于文件的连接器在 `location` 下都有其自己的位置类型和支持的属性。 **请在连接器文章 -> 数据集属性部分中查看详细信息**。 | 是      |
| compression | 用于配置文件压缩的一组属性。 如果希望在执行活动期间进行压缩/解压缩, 请配置此部分。 | 否 |
| type | 用于读取/写入二进制文件的压缩编解码器。 <br>允许的值为**bzip2**、 **gzip**、 **deflate**、 **ZipDeflate**。 保存文件时使用。 | 否       |
| level | 压缩率。 在复制活动接收器中使用数据集时应用。<br>允许的值为 **Optimal** 或 **Fastest**。<br>- **Fastest**：尽快完成压缩操作，不过，无法以最佳方式压缩生成的文件。<br>- **Optimal**：以最佳方式完成压缩操作，不过，需要耗费更长的时间。 有关详细信息，请参阅 [Compression Level](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx)（压缩级别）主题。 | 否       |

下面是 Azure Blob 存储上的二进制数据集的示例:

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
>在复制活动中使用二进制数据集时, 只能从二进制数据集复制到二进制数据集。

### <a name="binary-as-source"></a>二进制作为源

复制活动的 ***\*source\**** 节支持以下属性。

| 属性      | 说明                                                  | 必选 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为**BinarySource**。 | 是      |
| storeSettings | 有关如何从数据存储读取数据的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自己支持的读取设置。 **请在连接器文章 -> 复制活动属性部分中查看详细信息**。 | 否       |

### <a name="binary-as-sink"></a>二进制作为接收器

复制活动的 ***\*sink\**** 节支持以下属性。

| 属性      | 说明                                                  | 必选 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为**BinarySink**。 | 是      |
| storeSettings | 有关如何将数据写入到数据存储的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自身支持的写入设置。 **请在连接器文章 -> 复制活动属性部分中查看详细信息**。 | 否       |

## <a name="next-steps"></a>后续步骤

- [复制活动概述](copy-activity-overview.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
- [删除活动](delete-activity.md)
