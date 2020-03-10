---
title: Azure 数据工厂中的二进制格式
description: 本主题介绍如何在 Azure 数据工厂中处理二进制格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 8ebb4f0d1a06a7bf29dc46cd696b6acfd2527095
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78386700"
---
# <a name="binary-format-in-azure-data-factory"></a>Azure 数据工厂中的二进制格式

以下连接器支持二进制格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [azure 文件存储](connector-azure-file-storage.md)、[文件系统](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和[SFTP](connector-sftp.md)。

可以在[复制活动](copy-activity-overview.md)、 [GetMetadata 活动](control-flow-get-metadata-activity.md)或[删除活动](delete-activity.md)中使用二进制数据集。 使用二进制数据集时，ADF 不会分析文件内容，而是按原样处理。 

>[!NOTE]
>在复制活动中使用二进制数据集时，只能从二进制数据集复制到二进制数据集。

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供二进制数据集支持的属性列表。

| properties         | 说明                                                  | 必选 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 数据集的 type 属性必须设置为**Binary**。 | 是      |
| location         | 文件的位置设置。 每个基于文件的连接器都具有其自己的位置类型和 `location`下支持的属性。 **请参阅连接器文章-> 数据集属性 "部分中的详细信息**。 | 是      |
| compression | 用于配置文件压缩的一组属性。 如果希望在执行活动期间进行压缩/解压缩，请配置此部分。 | 否 |
| type | 用于读取/写入二进制文件的压缩编解码器。 <br>允许的值为**bzip2**、 **gzip**、 **deflate**、 **ZipDeflate**。 保存文件时要使用的。<br>请注意，使用复制活动解压缩 ZipDeflate 文件并写入到基于文件的接收器数据存储时，文件将被提取到文件夹： `<path specified in dataset>/<folder named as source zip file>/`。 | 否       |
| 级别 | 压缩比。 在复制活动接收器中使用数据集时应用。<br>允许的值为 "**最佳**" 或 "**最快**"。<br>- **速度最快：** 压缩操作应该尽快完成，即使生成的文件未以最佳方式压缩。<br>- **最佳**：应以最佳方式压缩压缩操作，即使操作需要更长的时间才能完成。 有关详细信息，请参阅 [Compression Level](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx)（压缩级别）主题。 | 否       |

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

### <a name="binary-as-source"></a>二进制作为源

复制活动***\*源\**** 部分支持以下属性。

| properties      | 说明                                                  | 必选 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为**BinarySource**。 | 是      |
| storeSettings | 有关如何从数据存储区中读取数据的一组属性。 在 `storeSettings`下，每个基于文件的连接器都有其自己支持的读取设置。 **请参阅连接器文章-> 复制活动属性部分中的详细信息**。 | 否       |

### <a name="binary-as-sink"></a>二进制作为接收器

复制活动***\*接收器\**** 部分支持以下属性。

| properties      | 说明                                                  | 必选 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为**BinarySink**。 | 是      |
| storeSettings | 如何将数据写入数据存储区的一组属性。 每个基于文件的连接器在 `storeSettings`下有自己的受支持的写入设置。 **请参阅连接器文章-> 复制活动属性部分中的详细信息**。 | 否       |

## <a name="next-steps"></a>后续步骤

- [复制活动概述](copy-activity-overview.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
- [删除活动](delete-activity.md)
