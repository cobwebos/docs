---
title: Azure 数据工厂中的 Excel 格式
description: 本主题介绍了如何处理 Azure 数据工厂中的 Excel 格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/08/2020
ms.author: jingwang
ms.openlocfilehash: 46108ed06659d234907c6eaa6841dc18022c73bf
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86144123"
---
# <a name="excel-format-in-azure-data-factory"></a>Azure 数据工厂中的 Excel 格式
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

如果要**分析 Excel 文件**，请按此文的要求操作。 Azure 数据工厂同时支持“.xls”和“.xlsx”。

以下连接器支持 Excel 格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [azure 文件存储](connector-azure-file-storage.md)、[文件系统](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和[SFTP](connector-sftp.md)。 它可以作为源，但不可作为接收器。

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Excel 数据集支持的属性列表。

| 属性         | 说明                                                  | 必须 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 数据集的 type 属性必须设置为“Excel”。   | 是      |
| location         | 文件的位置设置。 每个基于文件的连接器在 `location` 下都有其自己的位置类型和支持的属性。 | 是      |
| sheetName        | 要读取数据的 Excel 工作表名称。                       | 是      |
| range            | 给定工作表中用于定位选定数据的单元格范围，例如 `A3:H5`（从 A3 到 H5 的表）、`A3`（从单元格 A3 开始的表）、`A3:A3`（单个单元格）。 如果未指定，则 ADF 以表的形式从整个工作表读取。 | 否       |
| firstRowAsHeader | 指定是否要将给定工作表/范围内的第一行视为带有列名的标题行。<br>允许的值为 **true** 和 **true**（默认值）。 | 否       |
| nullValue        | 指定 null 值的字符串表示形式。 <br>默认值为**空字符串**。 | 否       |
| compressionCodec | 用来读取 Excel 文件的压缩编解码器。 <br>允许的值为 **bzip2**、**gzip**、**deflate**、**ZipDeflate**、**snappy** 或 **lz4**。 默认设置是不压缩。 <br>请注意  ，复制活动目前不支持“snappy”和“lz4”，映射数据流不支持“ZipDeflate”。 <br>**注意**，使用复制活动来解压缩 ZipDeflate 文件并写入到基于文件的接收器数据存储时，会将文件提取到以下文件夹：`<path specified in dataset>/<folder named as source zip file>/`。 | 否       |
| compressionLevel | 压缩率。 <br>允许的值为 **Optimal** 或 **Fastest**。<br>- **Fastest**：尽快完成压缩操作，不过，无法以最佳方式压缩生成的文件。<br>- **Optimal**：以最佳方式完成压缩操作，不过，需要耗费更长的时间。 有关详细信息，请参阅 [Compression Level](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx)（压缩级别）主题。 | 否       |

下面是 Azure Blob 存储上 Excel 数据集的示例：

```json
{
    "name": "ExcelDataset",
    "properties": {
        "type": "Excel",
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
            "sheetName": "MyWorksheet",
            "range": "A3:H5",
            "firstRowAsHeader": true
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Excel 源支持的属性列表。

### <a name="excel-as-source"></a>Excel 作为源 

复制活动的 ***\*source\**** 节支持以下属性。

| 属性      | 说明                                                  | 必须 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为“ExcelSource”。 | 是      |
| storeSettings | 有关如何从数据存储读取数据的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自己支持的读取设置。 | 否       |

```json
"activities": [
    {
        "name": "CopyFromExcel",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "ExcelSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            ...
        }
        ...
    }
]
```

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流时，可以在以下数据存储中读取 Excel 格式： [Azure Blob 存储](connector-azure-blob-storage.md#mapping-data-flow-properties)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)和[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。 您可以使用 Excel 数据集或使用[内联数据集](data-flow-source.md#inline-datasets)指向 excel 文件。

### <a name="source-properties"></a>源属性

下表列出了 Excel 源支持的属性。 可以在 "**源选项**" 选项卡中编辑这些属性。使用内联数据集时，你将看到与 "[数据集属性](#dataset-properties)" 部分所述的属性相同的其他文件设置。

| 名称                      | 说明                                                  | 必需 | 允许的值                                            | 数据流脚本属性         |
| ------------------------- | ------------------------------------------------------------ | -------- | --------------------------------------------------------- | --------------------------------- |
| 通配符路径           | 将处理所有匹配通配符路径的文件。 重写在数据集中设置的文件夹和文件路径。 | 否       | String[]                                                  | wildcardPaths                     |
| 分区根路径       | 对于已分区的文件数据，可以输入分区根路径以便将分区文件夹读取为列 | 否       | String                                                    | partitionRootPath                 |
| 文件列表             | 你的源是否指向列出要处理的文件的文本文件 | 否       | `true` 或 `false`                                         | fileList                          |
| 要存储文件名的列 | 使用源文件名称和路径创建新列       | 否       | String                                                    | rowUrlColumn                      |
| 完成后          | 在处理后删除或移动文件。 文件路径从容器根开始 | 否       | 删除： `true` 或`false` <br> 移动`['<from>', '<to>']` | purgeFiles <br> moveFiles         |
| 按上次修改时间筛选   | 选择根据文件上次更改时间筛选文件 | 否       | 时间戳                                                 | ModifiedAfter <br> modifiedBefore |

### <a name="source-example"></a>源示例

下图是使用数据集模式映射数据流的 Excel 源配置示例。

![Excel 源](media/data-flow/excel-source.png)

关联的数据流脚本为：

```
source(allowSchemaDrift: true,
    validateSchema: false,
    wildcardPaths:['*.xls']) ~> ExcelSource
```

如果使用内联数据集，则会在映射数据流时看到以下源选项。

![Excel 源内联数据集](media/data-flow/excel-source-inline-dataset.png)

关联的数据流脚本为：

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'excel',
    fileSystem: 'container',
    folderPath: 'path',
    fileName: 'sample.xls',
    sheetName: 'worksheet',
    firstRowAsHeader: true) ~> ExcelSourceInlineDataset
```

## <a name="next-steps"></a>后续步骤

- [复制活动概述](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
