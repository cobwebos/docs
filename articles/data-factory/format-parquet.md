---
title: Azure 数据工厂中的 Parquet 格式
description: 本主题介绍了如何处理 Azure 数据工厂中的 Parquet 格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 223b1b996b82acaa753eb55723e251dc5901bbec
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417702"
---
# <a name="parquet-format-in-azure-data-factory"></a>Azure 数据工厂中的 Parquet 格式
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

如果要**分析 Parquet 文件或以 Parquet 格式写入数据**，请遵循此文章中的说明。 

支持以下连接器的 Parquet 格式[：Amazon S3、Azure](connector-amazon-simple-storage-service.md) [Blob、Azure 数据存储湖存储第 1 代](connector-azure-data-lake-store.md)[、Azure 数据存储湖存储第 2 代](connector-azure-data-lake-storage.md)[、Azure 文件存储](connector-azure-file-storage.md)、[文件系统](connector-file-system.md)[、FTP、Google](connector-ftp.md)[云存储](connector-google-cloud-storage.md)[、HDFS、HTTP](connector-hdfs.md)和[SFTP。](connector-sftp.md) [Azure Blob](connector-azure-blob-storage.md) [HTTP](connector-http.md)

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供了 Parquet 数据集支持的属性列表。

| properties         | 说明                                                  | 必选 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 数据集的 type 属性必须设置为 **Parquet**。 | 是      |
| location         | 文件的位置设置。 每个基于文件的连接器在 `location` 下都有其自己的位置类型和支持的属性。 **请在连接器文章 -> 数据集属性部分中查看详细信息**。 | 是      |
| compressionCodec | 写入到 Parquet 文件时要使用的压缩编解码器。 从 Parquet 文件读取时，数据工厂根据文件元数据自动确定压缩编解码器。<br>支持的类型为“**none**”、“**gzip**”、“**snappy**”（默认值）和“**lzo**”。 注意 当前复制活动不支持 LZO 读取/写入 Parquet 文件。 | 否       |

> [!NOTE]
> Parquet 文件不支持列名称中包含空格。

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

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供了 Parquet 源和接收器支持的属性列表。

### <a name="parquet-as-source"></a>Parquet 作为源

复制活动***\*源\**** 部分支持以下属性。

| properties      | 说明                                                  | 必选 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为 **ParquetSource**。 | 是      |
| storeSettings | 有关如何从数据存储读取数据的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自己支持的读取设置。 **请在连接器文章 -> 复制活动属性部分中查看详细信息**。 | 否       |

### <a name="parquet-as-sink"></a>Parquet 作为接收器

复制活动***\*接收器\**** 部分支持以下属性。

| properties      | 说明                                                  | 必选 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为 **ParquetSink**。 | 是      |
| storeSettings | 有关如何将数据写入到数据存储的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自身支持的写入设置。 **请在连接器文章 -> 复制活动属性部分中查看详细信息**。 | 否       |

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流中从[源转换](data-flow-source.md)和[接收器转换](data-flow-sink.md)中了解详细信息。

## <a name="data-type-support"></a>数据类型支持

目前不支持 Parquet 复杂数据类型（例如 MAP、LIST、STRUCT）。

## <a name="using-self-hosted-integration-runtime"></a>使用自承载集成运行时

> [!IMPORTANT]
> 对于由自托管集成运行时（例如本地数据存储和云数据存储之间）授权的副本，如果您未**按本身份**复制 Parquet 文件，则需要在 IR 计算机上安装**64 位 JRE 8（Java 运行时环境）或 OpenJDK**和**Microsoft Visual C++ 2010 可再分发包**。 有关详细信息，请查看以下段落。

对于在具有 Parquet 文件序列化/反序列化的自承载 IR 上运行的副本，ADF 首先检查注册表*`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* 以查找 JRE（如果未找到）来定位 Java 运行时*`JAVA_HOME`*，其次检查 OpenJDK 的系统变量。

- **要使用 JRE**： 64 位 IR 需要 64 位 JRE。 可在[此处](https://go.microsoft.com/fwlink/?LinkId=808605)找到它。
- **使用 OpenJDK**： 自 IR 版本 3.13 以来，它一直支持它。 将 jvm.dll 以及所有其他必需的 OpenJDK 程序集打包到自承载 IR 计算机中，并相应地设置系统环境变量 JAVA_HOME。
- **要安装 Visual C++ 2010 可分发包**： 可视C++ 2010 可分发包未安装自托管的 IR 安装。 可在[此处](https://www.microsoft.com/download/details.aspx?id=14632)找到它。

> [!TIP]
> 如果使用自承载集成运行时将数据复制为 Parquet 格式或从 Parquet 格式复制数据，并遇到“调用 java 时发生错误，消息: java.lang.OutOfMemoryError:Java 堆空间”的错误，则可以在托管自承载 IR 的计算机上添加环境变量 `_JAVA_OPTIONS`，以便调整 JVM 的最小/最大堆大小，以支持此类复制，然后重新运行管道****。

![在自承载 IR 上设置 JVM 堆大小](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

示例：将变量 `_JAVA_OPTIONS` 的值设置为 `-Xms256m -Xmx16g`。 标志 `Xms` 指定 Java 虚拟机 (JVM) 的初始内存分配池，而 `Xmx` 指定最大内存分配池。 这意味着 JVM 初始内存为 `Xms`，并且能够使用的最多内存为 `Xmx`。 默认情况下，ADF 使用最小 64 MB 和最大 1G。

## <a name="next-steps"></a>后续步骤

- [复制活动概述](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [查找活动](control-flow-lookup-activity.md)
- [获取元数据活动](control-flow-get-metadata-activity.md)
