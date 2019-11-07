---
title: Azure 数据工厂中的 Avro 格式
description: 本主题介绍了如何处理 Azure 数据工厂中的 Avro 格式。
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: a40e1b717a2ac791cdf7583afb9a7649a5b00af1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73674898"
---
# <a name="avro-format-in-azure-data-factory"></a>Azure 数据工厂中的 Avro 格式

如果要**分析 Avro 文件或以 Avro 格式写入数据**，请遵循本文中的说明。 

以下连接器支持 Avro 格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [azure 文件存储](connector-azure-file-storage.md)、[文件系统](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google云存储](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和[SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各个部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Avro 数据集支持的属性列表。

| 属性         | 说明                                                  | 必选 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 数据集的 type 属性必须设置为 **Avro**。 | 是      |
| location         | 文件的位置设置。 每个基于文件的连接器在 `location` 下都有其自己的位置类型和支持的属性。 **请在连接器文章 -> 数据集属性部分中查看详细信息**。 | 是      |
| avroCompressionCodec | 写入到 Avro 文件时要使用的压缩编解码器。 当从 Avro 文件进行读取时，数据工厂会基于文件元数据自动确定压缩编解码器。<br>支持的类型为“none”（默认值）、“deflate”、“snappy”。 | 否       |

> [!NOTE]
> Avro 文件不支持列名称中包含空格。

下面是 Azure Blob 存储上的 Avro 数据集的示例：

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

有关可用于定义活动的各节和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Avro 源和接收器支持的属性列表。

### <a name="avro-as-source"></a>Avro 作为源

复制活动的 ***\*source\**** 节支持以下属性。

| 属性      | 说明                                                  | 必选 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为 **AvroSource**。 | 是      |
| storeSettings | 有关如何从数据存储读取数据的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自己支持的读取设置。 **请在连接器文章 -> 复制活动属性部分中查看详细信息**。 | 否       |

### <a name="avro-as-sink"></a>Avro 作为接收器

复制活动的 ***\*sink\**** 节支持以下属性。

| 属性      | 说明                                                  | 必选 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为 **AvroSink**。 | 是      |
| storeSettings | 有关如何将数据写入到数据存储的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自身支持的写入设置。 **请在连接器文章 -> 复制活动属性部分中查看详细信息**。 | 否       |

## <a name="data-type-support"></a>数据类型支持

不支持 Avro [复杂数据类型](https://avro.apache.org/docs/current/spec.html#schema_complex)（记录、枚举、数组、映射、联合与固定值）。

## <a name="next-steps"></a>后续步骤

- [复制活动概述](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
