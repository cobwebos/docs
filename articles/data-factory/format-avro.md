---
title: Azure 数据工厂中的 Avro 格式
description: 本主题介绍如何在 Azure 数据工厂中处理 Avro 格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 9e962a0e76cdc0d51a87df3c33927c34db991fc7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927416"
---
# <a name="avro-format-in-azure-data-factory"></a>Azure 数据工厂中的 Avro 格式

如果要**分析 avro 文件或以 Avro 格式写入数据**，请遵循此文。 

以下连接器支持 Avro 格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [azure 文件存储](connector-azure-file-storage.md)、[文件系统](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和[SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Avro 数据集支持的属性列表。

| properties         | 描述                                                  | 需要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 数据集的 type 属性必须设置为**Avro**。 | 是      |
| 位置         | 文件的位置设置。 每个基于文件的连接器都具有其自己的位置类型和 `location`下支持的属性。 **请参阅连接器文章-> 数据集属性 "部分中的详细信息**。 | 是      |
| avroCompressionCodec | 写入 Avro 文件时要使用的压缩编解码器。 从 Avro 文件中读取数据时，数据工厂会根据文件元数据自动确定压缩编解码器。<br>支持的类型为 "**none**" （默认值）、"**deflate**" 和 "**snappy**"。 | No       |

> [!NOTE]
> Avro 文件不支持列名中的空格。

下面是 Azure Blob 存储上 Avro 数据集的示例：

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Avro 源和接收器支持的属性列表。

### <a name="avro-as-source"></a>Avro 作为源

复制活动***\*源\**** 部分支持以下属性。

| properties      | 描述                                                  | 需要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为**AvroSource**。 | 是      |
| storeSettings | 有关如何从数据存储区中读取数据的一组属性。 在 `storeSettings`下，每个基于文件的连接器都有其自己支持的读取设置。 **请参阅连接器文章-> 复制活动属性部分中的详细信息**。 | No       |

### <a name="avro-as-sink"></a>Avro 作为接收器

复制活动***\*接收器\**** 部分支持以下属性。

| properties      | 描述                                                  | 需要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为**AvroSink**。 | 是      |
| storeSettings | 如何将数据写入数据存储区的一组属性。 每个基于文件的连接器在 `storeSettings`下有自己的受支持的写入设置。 **请参阅连接器文章-> 复制活动属性部分中的详细信息**。 | No       |

## <a name="data-type-support"></a>数据类型支持

不支持 Avro[复杂数据类型](https://avro.apache.org/docs/current/spec.html#schema_complex)（记录、枚举、数组、映射、联合和固定）。

## <a name="next-steps"></a>后续步骤

- [复制活动概述](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
