---
title: Azure 数据工厂中的 ORC 格式
description: 本主题介绍了如何处理 Azure 数据工厂中的 ORC 格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jingwang
ms.openlocfilehash: 3aa42d6060ecdd93dd97438a025c4f5e4f05ac52
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531723"
---
# <a name="orc-format-in-azure-data-factory"></a>Azure 数据工厂中的 ORC 格式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

如果要**分析 ORC 文件或以 ORC 格式写入数据**，请遵循本文中的说明。 

以下连接器支持 ORC 格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [azure 文件存储](connector-azure-file-storage.md)、 [文件系统](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和 [SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 ORC 数据集支持的属性列表。

| 属性         | 说明                                                  | 必须 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 数据集的 type 属性必须设置为 **Orc**。 | 是      |
| location         | 文件的位置设置。 每个基于文件的连接器在 `location` 下都有其自己的位置类型和支持的属性。 **请在连接器文章 -> 数据集属性部分中查看详细信息**。 | 是      |

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

请注意以下几点：

* 不支持复杂数据类型（STRUCT、MAP、LIST、UNION）。
* 不支持列名称中的空格。
* ORC 文件有三个[压缩相关的选项](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/)：NONE、ZLIB、SNAPPY。 数据工厂支持从使用其中任一压缩格式的 ORC 文件中读取数据。 它使用元数据中的压缩编解码器来读取数据。 但是，写入 ORC 文件时，数据工厂会选择 ZLIB，这是 ORC 的默认选项。 目前没有任何选项可以重写此行为。

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 ORC 源和接收器支持的属性列表。

### <a name="orc-as-source"></a>以 ORC 作为源

复制活动的 ***\*source\**** 节支持以下属性。

| 属性      | 说明                                                  | 必须 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为 OrcSource  。 | 是      |
| storeSettings | 有关如何从数据存储读取数据的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自己支持的读取设置。 **请在连接器文章 -> 复制活动属性部分中查看详细信息**。 | 否       |

### <a name="orc-as-sink"></a>以 ORC 作为接收器

复制活动的 ***\*sink\**** 节支持以下属性。

| 属性      | 说明                                                  | 必须 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为 OrcSink  。 | 是      |
| formatSettings | 一组属性。 请参阅下面的 **ORC 写入设置** 表。 |    否      |
| storeSettings | 有关如何将数据写入到数据存储的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自身支持的写入设置。 **请在连接器文章 -> 复制活动属性部分中查看详细信息**。 | 否       |

受支持的 **ORC 写入设置** `formatSettings` 如下：

| 属性      | 说明                                                  | 必需                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | FormatSettings 的类型必须设置为 **OrcWriteSettings**。 | 是                                                   |
| maxRowsPerFile | 向文件夹中写入数据时，可以选择写入多个文件，并指定每个文件的最大行数。  | 否 |
| fileNamePrefix | 在配置时适用 `maxRowsPerFile` 。<br> 在将数据写入多个文件时指定文件名前缀，导致此模式： `<fileNamePrefix>_00000.<fileExtension>` 。 如果未指定，则将自动生成文件名前缀。 当源是基于文件的存储或 [启用了分区选项的数据存储](copy-activity-performance-features.md)时，此属性不适用。  | 否 |

## <a name="using-self-hosted-integration-runtime"></a>使用自承载集成运行时

> [!IMPORTANT]
> 对于自承载 Integration Runtime （例如，在本地和云数据存储之间）的复制，如果不 **按**原样复制 ORC 文件，则需要在 IR 计算机上安装 **64 位 JRE 8 (Java Runtime Environment) 或 OpenJDK** 和 **Microsoft Visual C++ 2010 可再发行组件包** 。 有关更多详细信息，请查看以下段落。

对于使用 ORC 文件序列化/反序列化在自承载集成运行时上运行的复制，ADF 将通过首先检查 JRE 的注册表项 *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* 来查找 Java 运行时，如果未找到，则会检查系统变量 *`JAVA_HOME`* 来查找 OpenJDK。

- **若要使用 JRE**：64 位 IR 需要 64 位 JRE。 可在[此处](https://go.microsoft.com/fwlink/?LinkId=808605)找到它。
- **使用 OpenJDK**：自 IR 版本3.13 起支持。 将 jvm.dll 以及所有其他必需的 OpenJDK 程序集打包到自承载 IR 计算机中，并相应地设置系统环境变量 JAVA_HOME。
- **若要安装 Visual C++ 2010 可再发行组件包**： Visual C++ 2010 可再发行包未安装自承载 IR 安装。 可在[此处](https://www.microsoft.com/download/details.aspx?id=14632)找到它。

> [!TIP]
> 如果使用自承载集成运行时将数据复制为 ORC 格式或从 ORC 格式复制数据，并遇到“调用 java 时发生错误，消息: java.lang.OutOfMemoryError:Java 堆空间”的错误，则可以在托管自承载 IR 的计算机中添加环境变量 `_JAVA_OPTIONS`，以便调整 JVM 的最小/最大堆大小，以支持此类复制，然后重新运行管道****。

![在自承载 IR 上设置 JVM 堆大小](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

示例：将变量 `_JAVA_OPTIONS` 的值设置为 `-Xms256m -Xmx16g`。 标志 `Xms` 指定 Java 虚拟机 (JVM) 的初始内存分配池，而 `Xmx` 指定最大内存分配池。 这意味着 JVM 初始内存为 `Xms`，并且能够使用的最多内存为 `Xmx`。 默认情况下，ADF 使用最小 64 MB，最大值为1G。

## <a name="next-steps"></a>后续步骤

- [复制活动概述](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
