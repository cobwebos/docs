---
title: Azure 数据工厂中的 Parquet 格式
description: 本主题介绍如何在 Azure 数据工厂中处理 Parquet 格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 340f91fc926c155f95449f7cc49c214f46d1ff35
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77423651"
---
# <a name="parquet-format-in-azure-data-factory"></a>Azure 数据工厂中的 Parquet 格式

若要**分析 Parquet 文件或以 Parquet 格式写入数据**，请参阅此文。 

以下连接器支持 Parquet 格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [azure 文件存储](connector-azure-file-storage.md)、[文件系统](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和[SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Parquet 数据集支持的属性列表。

| 属性         | 说明                                                  | 必需 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 数据集的 type 属性必须设置为**Parquet**。 | 是      |
| 位置         | 文件的位置设置。 每个基于文件的连接器都具有其自己的位置类型和 `location`下支持的属性。 **请参阅连接器文章-> 数据集属性 "部分中的详细信息**。 | 是      |
| compressionCodec | 写入 Parquet 文件时要使用的压缩编解码器。 从 Parquet 文件中读取数据时，数据工厂会根据文件元数据自动确定压缩编解码器。<br>支持的类型为 "**none**"、"**gzip**"、"**snappy**" （默认值）和 "**lzo**"。 注意：当前复制活动在读取/写入 Parquet 文件时不支持 LZO。 | 是       |

> [!NOTE]
> Parquet 文件不支持列名中的空格。

下面是 Azure Blob 存储上的 Parquet 数据集的示例：

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
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
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Parquet 源和接收器支持的属性列表。

### <a name="parquet-as-source"></a>作为源的 Parquet

复制活动***\*源\**** 部分支持以下属性。

| 属性      | 说明                                                  | 必需 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为**ParquetSource**。 | 是      |
| storeSettings | 有关如何从数据存储区中读取数据的一组属性。 在 `storeSettings`下，每个基于文件的连接器都有其自己支持的读取设置。 **请参阅连接器文章-> 复制活动属性部分中的详细信息**。 | 是       |

### <a name="parquet-as-sink"></a>Parquet 作为接收器

复制活动***\*接收器\**** 部分支持以下属性。

| 属性      | 说明                                                  | 必需 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为**ParquetSink**。 | 是      |
| storeSettings | 如何将数据写入数据存储区的一组属性。 每个基于文件的连接器在 `storeSettings`下有自己的受支持的写入设置。 **请参阅连接器文章-> 复制活动属性部分中的详细信息**。 | 是       |

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流中了解[源转换](data-flow-source.md)和[接收器转换](data-flow-sink.md)中的详细信息。

## <a name="data-type-support"></a>数据类型支持

当前不支持 Parquet 复杂数据类型（例如，MAP、LIST、STRUCT）。

## <a name="using-self-hosted-integration-runtime"></a>使用自承载 Integration Runtime

> [!IMPORTANT]
> 对于由自承载集成运行时（例如，在本地与云数据存储之间）支持的复制，如果不是**按原样**复制 Parquet 文件，则需要在 IR 计算机上安装 **64 位 JRE 8（Java 运行时环境）或 OpenJDK**。 请参阅下面段落中的更多详细信息。

对于使用 Parquet 文件序列化/反序列化在自承载集成运行时上运行的复制，ADF 将通过首先检查 JRE 的注册表项 *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* 来查找 Java 运行时，如果未找到，则会检查系统变量 *`JAVA_HOME`* 来查找 OpenJDK。

- **使用 JRE**：64位 IR 需要64位 JRE。 可在[此处](https://go.microsoft.com/fwlink/?LinkId=808605)找到它。
- **若要使用 OpenJDK**：从 IR 版本 3.13 开始受支持。 将 jvm.dll 以及所有其他必需的 OpenJDK 程序集打包到自承载 IR 计算机中，并相应地设置系统环境变量 JAVA_HOME。

> [!TIP]
> 如果使用自承载集成运行时将数据复制为 Parquet 格式或从 Parquet 格式复制数据，并遇到“调用 java 时发生错误，消息: java.lang.OutOfMemoryError:Java 堆空间”的错误，则可以在托管自承载 IR 的计算机上添加环境变量 **，以便调整 JVM 的最小/最大堆大小，以支持此类复制，然后重新运行管道**`_JAVA_OPTIONS`。

![在自承载 IR 上设置 JVM 堆大小](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

示例：将变量 `_JAVA_OPTIONS` 的值设置为 `-Xms256m -Xmx16g`。 标志 `Xms` 指定 Java 虚拟机 (JVM) 的初始内存分配池，而 `Xmx` 指定最大内存分配池。 这意味着 JVM 初始内存为 `Xms`，并且能够使用的最多内存为 `Xmx`。 默认情况下，ADF 最少使用 64MB 且最多使用 1G。

## <a name="next-steps"></a>后续步骤

- [复制活动概述](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
