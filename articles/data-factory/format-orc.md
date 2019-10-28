---
title: Azure 数据工厂中的 ORC 格式 |Microsoft Docs
description: 本主题介绍如何在 Azure 数据工厂中处理 ORC 格式。
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: beff81b7e8d207a2173497d039b915b7614fc30e
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952272"
---
# <a name="orc-format-in-azure-data-factory"></a>Azure 数据工厂中的 ORC 格式

若要**分析 ORC 文件或以 ORC 格式写入数据**，请参阅此文。 

以下连接器支持 ORC 格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [azure 文件存储](connector-azure-file-storage.md)、[文件系统](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud存储](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和[SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 ORC 数据集支持的属性列表。

| properties         | 描述                                                  | 需要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 数据集的 type 属性必须设置为**Orc**。 | 是      |
| 位置         | 文件的位置设置。 每个基于文件的连接器都具有其自己的位置类型和 `location` 下支持的属性。 **请参阅连接器文章-> 数据集属性 "部分中的详细信息**。 | 是      |

下面是 Azure Blob 存储上的 ORC 数据集的示例：

```json
{
    "name": "ORCDataset",
    "properties": {
        "type": "Orc",
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
            }
        }
    }
}
```

注意以下几点：

* 不支持复杂数据类型（STRUCT、MAP、LIST、UNION）。
* 不支持列名称中的空格。
* ORC 文件有三个[压缩相关的选项](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/)：NONE、ZLIB、SNAPPY。 数据工厂支持从使用其中任一压缩格式的 ORC 文件中读取数据。 它使用元数据中的压缩编解码器来读取数据。 但是，写入 ORC 文件时，数据工厂会选择 ZLIB，这是 ORC 的默认选项。 目前没有任何选项可以重写此行为。

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 ORC 源和接收器支持的属性列表。

### <a name="orc-as-source"></a>作为源的 ORC

复制活动***\*源\**** 部分支持以下属性。

| properties      | 描述                                                  | 需要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为**OrcSource**。 | 是      |
| storeSettings | 有关如何从数据存储区中读取数据的一组属性。 每个基于文件的连接器在 `storeSettings` 下具有其自己支持的读取设置。 **请参阅连接器文章-> 复制活动属性部分中的详细信息**。 | No       |

### <a name="orc-as-sink"></a>ORC 作为接收器

复制活动***\*接收器\**** 部分支持以下属性。

| properties      | 描述                                                  | 需要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为**OrcSink**。 | 是      |
| storeSettings | 如何将数据写入数据存储区的一组属性。 每个基于文件的连接器在 `storeSettings` 下具有其自己的支持的写入设置。 **请参阅连接器文章-> 复制活动属性部分中的详细信息**。 | No       |

## <a name="using-self-hosted-integration-runtime"></a>使用自承载 Integration Runtime

> [!IMPORTANT]
> 对于由自承载集成运行时（例如，在本地与云数据存储之间）支持的复制，如果不是**按原样**复制 ORC 文件，则需要在 IR 计算机上安装 **64 位 JRE 8（Java 运行时环境）或 OpenJDK**。 请参阅下面段落中的更多详细信息。

对于使用 ORC 文件序列化/反序列化在自承载集成运行时上运行的复制，ADF 将通过首先检查 JRE 的注册表项 *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* 来查找 Java 运行时，如果未找到，则会检查系统变量 *`JAVA_HOME`* 来查找 OpenJDK。

- **使用 JRE**：64位 IR 需要64位 JRE。 可在[此处](https://go.microsoft.com/fwlink/?LinkId=808605)找到它。
- **若要使用 OpenJDK**：从 IR 版本 3.13 开始受支持。 将 jvm.dll 以及所有其他必需的 OpenJDK 程序集打包到自承载 IR 计算机中，并相应地设置系统环境变量 JAVA_HOME。

> [!TIP]
> 如果使用自承载 Integration Runtime 向/从 ORC 格式复制数据，并遇到错误 "调用 java、message： **OutOfMemoryError： java 堆空间**时出错"，则可以在计算机中添加环境变量 `_JAVA_OPTIONS`承载自承载 IR 以调整 JVM 的最小/最大堆大小以提供此类副本，然后重新运行该管道。

![在自承载 IR 上设置 JVM 堆大小](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

示例：将变量 `_JAVA_OPTIONS` 的值设置为 `-Xms256m -Xmx16g`。 标志 `Xms` 指定 Java 虚拟机 (JVM) 的初始内存分配池，而 `Xmx` 指定最大内存分配池。 这意味着 JVM 初始内存为 `Xms`，并且能够使用的最多内存为 `Xmx`。 默认情况下，ADF 最少使用 64MB 且最多使用 1G。

## <a name="next-steps"></a>后续步骤

- [复制活动概述](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
