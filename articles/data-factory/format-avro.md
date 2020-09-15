---
title: Azure 数据工厂中的 Avro 格式
description: 本主题介绍了如何处理 Azure 数据工厂中的 Avro 格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jingwang
ms.openlocfilehash: 7be92289d293798393ead3562c39721d46ce561b
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531842"
---
# <a name="avro-format-in-azure-data-factory"></a>Azure 数据工厂中的 Avro 格式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

如果要**分析 Avro 文件或以 Avro 格式写入数据**，请遵循本文中的说明。 

以下连接器支持 Avro 格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [azure 文件存储](connector-azure-file-storage.md)、 [文件系统](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和 [SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Avro 数据集支持的属性列表。

| 属性         | 说明                                                  | 必须 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 数据集的 type 属性必须设置为 **Avro**。 | 是      |
| location         | 文件的位置设置。 每个基于文件的连接器在 `location` 下都有其自己的位置类型和支持的属性。 **请在连接器文章 -> 数据集属性部分中查看详细信息**。 | 是      |
| avroCompressionCodec | 写入到 Avro 文件时要使用的压缩编解码器。 从 Avro 文件中读取数据时，数据工厂会根据文件元数据自动确定压缩编解码器。<br>支持的类型为“none”  （默认值）、“deflate”  、“snappy”  。 请注意，当前复制活动在读取/写入 Avro 文件时不支持 Snappy。 | 否       |

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

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Avro 源和接收器支持的属性列表。

### <a name="avro-as-source"></a>Avro 作为源

复制活动的 ***\*source\**** 节支持以下属性。

| 属性      | 说明                                                  | 必须 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为 **AvroSource**。 | 是      |
| storeSettings | 有关如何从数据存储读取数据的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自己支持的读取设置。 **请在连接器文章 -> 复制活动属性部分中查看详细信息**。 | 否       |

### <a name="avro-as-sink"></a>Avro 作为接收器

复制活动的 ***\*sink\**** 节支持以下属性。

| 属性      | 说明                                                  | 必须 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为 **AvroSink**。 | 是      |
| formatSettings          | 一组属性。 请参阅下面的 **Avro 写入设置** 表。| 否      |
| storeSettings | 有关如何将数据写入到数据存储的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自身支持的写入设置。 **请在连接器文章 -> 复制活动属性部分中查看详细信息**。 | 否       |

支持的 **Avro 写入设置** `formatSettings` 如下：

| 属性      | 说明                                                  | 必需                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | FormatSettings 的类型必须设置为 **AvroWriteSettings**。 | 是                                                   |
| maxRowsPerFile | 向文件夹中写入数据时，可以选择写入多个文件，并指定每个文件的最大行数。  | 否 |
| fileNamePrefix | 在配置时适用 `maxRowsPerFile` 。<br> 在将数据写入多个文件时指定文件名前缀，导致此模式： `<fileNamePrefix>_00000.<fileExtension>` 。 如果未指定，则将自动生成文件名前缀。 当源是基于文件的存储或 [启用了分区选项的数据存储](copy-activity-performance-features.md)时，此属性不适用。  | 否 |

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流时，可以在以下数据存储中读取和写入 avro 格式： [Azure Blob 存储](connector-azure-blob-storage.md#mapping-data-flow-properties)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)和 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。

### <a name="source-properties"></a>源属性

下表列出了 avro 源支持的属性。 可以在 " **源选项** " 选项卡中编辑这些属性。

| 名称 | 说明 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 通配符路径 | 将处理所有匹配通配符路径的文件。 重写在数据集中设置的文件夹和文件路径。 | 否 | String[] | wildcardPaths |
| 分区根路径 | 对于已分区的文件数据，可以输入分区根路径以便将分区文件夹读取为列 | 否 | String | partitionRootPath |
| 文件列表 | 你的源是否指向列出要处理的文件的文本文件 | 否 | `true` 或 `false` | fileList |
| 要存储文件名的列 | 使用源文件名称和路径创建新列 | 否 | String | rowUrlColumn |
| 完成后 | 在处理后删除或移动文件。 文件路径从容器根开始 | 否 | 删除： `true` 或 `false` <br> 移动 `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| 按上次修改时间筛选 | 选择根据文件上次更改时间筛选文件 | 否 | 时间戳 | ModifiedAfter <br> modifiedBefore |

### <a name="sink-properties"></a>接收器属性

下表列出了 avro 接收器支持的属性。 可以在 " **设置** " 选项卡中编辑这些属性。

| 名称 | 说明 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 清除文件夹 | 如果在写入前清除目标文件夹 | 否 | `true` 或 `false` | truncate |
| 文件名选项 | 写入的数据的命名格式。 默认情况下，每个分区的一个文件的格式为 `part-#####-tid-<guid>` | 否 | 模式：字符串 <br> 每个分区： String [] <br> As 列中的数据：字符串 <br> 输出到单个文件： `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |
| 全部引用 | 将所有值括在引号中 | 否 | `true` 或 `false` | quoteAll |

## <a name="data-type-support"></a>数据类型支持

### <a name="copy-activity"></a>复制活动
在复制活动中 (记录、枚举、数组、映射、联合和固定) 不支持 Avro [复杂数据类型](https://avro.apache.org/docs/current/spec.html#schema_complex) 。

### <a name="data-flows"></a>数据流
处理数据流中的 Avro 文件时，可以读取和写入复杂的数据类型，但请务必先从数据集中清除物理架构。 在数据流中，可以设置逻辑投影，并派生作为复杂结构的列，然后将这些字段自动映射到 Avro 文件。

## <a name="next-steps"></a>后续步骤

- [复制活动概述](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
