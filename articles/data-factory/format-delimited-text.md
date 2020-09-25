---
title: Azure 数据工厂中带分隔符的文本格式
description: 本主题介绍了如何处理 Azure 数据工厂中带分隔符的文本格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jingwang
ms.openlocfilehash: ac6540dfd86430aab518b145ed391d1d6283219e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91276571"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Azure 数据工厂中带分隔符的文本格式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

若要**分析带分隔符的文本文件或以带分隔符的文本格式写入数据**，请遵循此文章中的说明。 

以下连接器支持带分隔符的文本格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [azure 文件存储](connector-azure-file-storage.md)、 [文件系统](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和 [SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供带分隔符的文本数据集支持的属性列表。

| 属性         | 说明                                                  | 必须 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 数据集的类型属性必须设置为 **DelimitedText**。 | 是      |
| location         | 文件的位置设置。 每个基于文件的连接器在 `location` 下都有其自己的位置类型和支持的属性。  | 是      |
| columnDelimiter  | 用于分隔文件中的列的字符。 <br>默认值为“逗号(`,`)”。 当列分隔符定义为空字符串（意味着没有分隔符）时，整行将被视为单个列。<br>目前，仅支持映射数据流而不支持复制活动的列分隔符作为空字符串或多字符。  | 否       |
| rowDelimiter     | 用于分隔文件中的行的单个字符或“\r\n”。 <br>由映射数据流和复制活动读取或写入时，默认值分别为以下任一值：["\r\n", "\r", "\n"]，以及“\n”或“\r\n” 。 <br>当行分隔符设置为“无分隔符”（空字符串）时，列分隔符也必须设置为“无分隔符”（空字符串），这意味着将整个内容视为单个值。<br>目前，仅支持将行分隔符作为空字符串用于映射数据流，但不支持复制活动。 | 否       |
| quoteChar        | 当列包含列分隔符时，用于括住列值的单个字符。 <br>默认值为**双引号** `"`。 <br>对于映射数据流，`quoteChar` 不能为空字符串。 <br>对于复制活动，当 `quoteChar` 定义为空字符串时，表示不使用引号字符且不用引号括住列值，`escapeChar` 用于转义列分隔符和值本身。 | 否       |
| escapeChar       | 用于转义括住值中的引号的单个字符。<br>默认值为**反斜杠`\`** 。 <br>对于映射数据流，`escapeChar` 不能为空字符串。 <br/>对于复制活动，当 `escapeChar` 定义为空字符串时，`quoteChar` 也必须设置为空字符串，在这种情况下，应确保所有列值不包含分隔符。 | 否       |
| firstRowAsHeader | 指定是否要将第一行视为/设为包含列名称的标头行。<br>允许的值为 **true** 和 **true**（默认值）。<br>当第一行作为标题为 false 时，备注 UI 数据预览和查找活动输出会自动生成列名称，因为 (从 0) 开始，复制活动需要从源到接收器的 [显式映射](copy-activity-schema-and-type-mapping.md#explicit-mapping) ，并按 (序号从 1) 到 Prop_ 查找列，并映射数据流列表，并将名称为 Column_ {n} (从 1) 开始查找列。  | 否       |
| nullValue        | 指定 null 值的字符串表示形式。 <br>默认值为**空字符串**。 | 否       |
| encodingName     | 用于读取/写入测试文件的编码类型。 <br>可用的值如下："UTF-8"、"UTF-16"、"UTF-16BE"、"UTF-32"、"UTF-32BE"、"US-ASCII"、“UTF-7”、"BIG5"、"EUC-JP"、"EUC-KR"、"GB2312"、"GB18030"、"JOHAB"、"SHIFT-JIS"、"CP875"、"CP866"、"IBM00858"、"IBM037"、"IBM273"、"IBM437"、"IBM500"、"IBM737"、"IBM775"、"IBM850"、"IBM852"、"IBM855"、"IBM857"、"IBM860"、"IBM861"、"IBM863"、"IBM864"、"IBM865"、"IBM869"、"IBM870"、"IBM01140"、"IBM01141"、"IBM01142"、"IBM01143"、"IBM01144"、"IBM01145"、"IBM01146"、"IBM01147"、"IBM01148"、"IBM01149"、"ISO-2022-JP"、"ISO-2022-KR"、"ISO-8859-1"、"ISO-8859-2"、"ISO-8859-3"、"ISO-8859-4"、"ISO-8859-5"、"ISO-8859-6"、"ISO-8859-7"、"ISO-8859-8"、"ISO-8859-9"、"ISO-8859-13"、"ISO-8859-15"、"WINDOWS-874"、"WINDOWS-1250"、"WINDOWS-1251"、"WINDOWS-1252"、"WINDOWS-1253"、"WINDOWS-1254"、"WINDOWS-1255"、"WINDOWS-1256"、"WINDOWS-1257"、"WINDOWS-1258”。<br>请注意，映射数据流不支持 UTF-7 编码。 | 否       |
| compressionCodec | 用于读取/写入文本文件的压缩编解码器。 <br>允许的值为 **bzip2**、 **gzip**、 **deflate**、 **ZipDeflate**、 **TarGzip**、 **snappy**或 **lz4**。 默认设置是不压缩。 <br>请注意  ，复制活动目前不支持“snappy”和“lz4”，映射数据流不支持“ZipDeflate”。 <br>**请注意**，在使用复制活动将**ZipDeflate** / **TarGzip**文件解压缩 (s) 并写入基于文件的接收器数据存储时，默认情况下，将文件提取到该文件夹： `<path specified in dataset>/<folder named as source compressed file>/` ，使用 `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` [复制活动源](#delimited-text-as-source)控制是否保留压缩文件的名称， (s) 为文件夹结构。 | 否       |
| compressionLevel | 压缩率。 <br>允许的值为 **Optimal** 或 **Fastest**。<br>- **Fastest**：尽快完成压缩操作，不过，无法以最佳方式压缩生成的文件。<br>- **Optimal**：以最佳方式完成压缩操作，不过，需要耗费更长的时间。 有关详细信息，请参阅 [Compression Level](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx)（压缩级别）主题。 | 否       |

下面是 Azure Blob 存储上的带分隔符的文本数据集的示例：

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
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
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "escapeChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供带分隔符的文本源和接收器支持的属性列表。

### <a name="delimited-text-as-source"></a>带分隔符的文本作为源 

复制活动的 ***\*source\**** 节支持以下属性。

| 属性       | 说明                                                  | 必需 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | 复制活动源的 type 属性必须设置为 **DelimitedTextSource**。 | 是      |
| formatSettings | 一组属性。 请参阅下面的“带分隔符的文本读取设置”表。 |  否       |
| storeSettings  | 有关如何从数据存储读取数据的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自己支持的读取设置。 | 否       |

`formatSettings` 下支持的**带分隔符的文本读取设置**：

| 属性      | 说明                                                  | 必需 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | formatSettings 的类型必须设置为 **DelimitedTextReadSettings**。 | 是      |
| skipLineCount | 指示从输入文件读取数据时要跳过的非空行数  。 <br>如果同时指定了 skipLineCount 和 firstRowAsHeader，则先跳过行，然后从输入文件读取标头信息。 | 否       |
| compressionProperties | 一组属性，指示如何为给定的压缩编解码器解压缩数据。 | 否       |
| preserveZipFileNameAsFolder<br> (*下 `compressionProperties` -> `type`) `ZipDeflateReadSettings` * |  当输入数据集配置了 ZipDeflate 压缩时适用。 指示是否在复制过程中以文件夹结构形式保留源 zip 文件名。<br>- 当设置为 true（默认值）时，数据工厂会将已解压缩的文件写入 `<path specified in dataset>/<folder named as source zip file>/`。<br>- 当设置为 false 时，数据工厂会直接将未解压缩的文件写入 `<path specified in dataset>`。 请确保在不同的源 zip 文件中没有重复的文件名，以免出现赛车或意外的行为。  | 否 |
| preserveCompressionFileNameAsFolder<br> (*下 `compressionProperties` -> `type`) `TarGZipReadSettings` *  | 当输入数据集配置了 **TarGzip** 压缩时适用。 指示是否在复制过程中将源压缩文件名保留为文件夹结构。<br>-如果设置为 **true (默认) **，数据工厂会将解压缩的文件写入 `<path specified in dataset>/<folder named as source compressed file>/` 。 <br>-当设置为 **false**时，数据工厂会将解压缩的文件直接写入到 `<path specified in dataset>` 中。 请确保在不同的源文件中没有重复的文件名，以免产生赛车或意外的行为。 | 否 |

```json
"activities": [
    {
        "name": "CopyFromDelimitedText",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                },
                "formatSettings": {
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 3,
                    "compressionProperties": {
                        "type": "ZipDeflateReadSettings",
                        "preserveZipFileNameAsFolder": false
                    }
                }
            },
            ...
        }
        ...
    }
]
```

### <a name="delimited-text-as-sink"></a>带分隔符的文本作为接收器

复制活动的 ***\*sink\**** 节支持以下属性。

| 属性       | 说明                                                  | 必需 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | 复制活动源的 type 属性必须设置为 **DelimitedTextSink**。 | 是      |
| formatSettings | 一组属性。 请参阅下面的“带分隔符的文本写入设置”表。 |    否      |
| storeSettings  | 有关如何将数据写入到数据存储的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自身支持的写入设置。  | 否       |

`formatSettings` 下支持的**带分隔符的文本写入设置**：

| 属性      | 说明                                                  | 必需                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | formatSettings 的类型必须设置为 **DelimitedTextWriteSettings**。 | 是                                                   |
| fileExtension | 用来为输出文件命名的扩展名，例如 `.csv`、`.txt`。 未在 DelimitedText 输出数据集中指定 `fileName` 时，必须指定该扩展名。 如果在输出数据集中配置了文件名，则它将其用作接收器文件名，并且将忽略文件扩展名设置。  | 未在输出数据集中指定文件名时为“是” |
| maxRowsPerFile | 向文件夹中写入数据时，可以选择写入多个文件，并指定每个文件的最大行数。  | 否 |
| fileNamePrefix | 在配置时适用 `maxRowsPerFile` 。<br> 在将数据写入多个文件时指定文件名前缀，导致此模式： `<fileNamePrefix>_00000.<fileExtension>` 。 如果未指定，则将自动生成文件名前缀。 当源是基于文件的存储或 [启用了分区选项的数据存储](copy-activity-performance-features.md)时，此属性不适用。  | 否 |

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流时，可以在以下数据存储中读取和写入分隔文本格式： [Azure Blob 存储](connector-azure-blob-storage.md#mapping-data-flow-properties)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)和 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。

### <a name="source-properties"></a>源属性

下表列出了由分隔文本源支持的属性。 可以在 " **源选项** " 选项卡中编辑这些属性。

| 名称 | 说明 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 通配符路径 | 将处理所有匹配通配符路径的文件。 重写在数据集中设置的文件夹和文件路径。 | 否 | string[] | wildcardPaths |
| 分区根路径 | 对于已分区的文件数据，可以输入分区根路径以便将分区文件夹读取为列 | 否 | String | partitionRootPath |
| 文件列表 | 你的源是否指向列出要处理的文件的文本文件 | 否 | `true` 或 `false` | fileList |
| 多行行 | 源文件是否包含跨多行的行。 多行值必须用引号引起来。 | 否 `true` 或 `false` | multiLineRow |
| 要存储文件名的列 | 使用源文件名称和路径创建新列 | 否 | String | rowUrlColumn |
| 完成后 | 在处理后删除或移动文件。 文件路径从容器根开始 | 否 | 删除： `true` 或 `false` <br> 移动 `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| 按上次修改时间筛选 | 选择根据文件上次更改时间筛选文件 | 否 | 时间戳 | ModifiedAfter <br> modifiedBefore |
| 允许找不到文件 | 如果为 true，则在找不到文件时不会引发错误 | 否 | `true` 或 `false` | ignoreNoFilesFound |

### <a name="source-example"></a>源示例

下图是用于映射数据流的分隔文本源配置的示例。

![DelimitedText 源](media/data-flow/delimited-text-source.png)

关联的数据流脚本为：

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    multiLineRow: true,
    wildcardPaths:['*.csv']) ~> CSVSource
```

### <a name="sink-properties"></a>接收器属性

下表列出了由分隔的文本接收器支持的属性。 可以在 " **设置** " 选项卡中编辑这些属性。

| 名称 | 说明 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 清除文件夹 | 如果在写入前清除目标文件夹 | 否 | `true` 或 `false` | truncate |
| 文件名选项 | 写入的数据的命名格式。 默认情况下，每个分区的一个文件的格式为 `part-#####-tid-<guid>` | 否 | 模式：字符串 <br> 每个分区： String [] <br> As 列中的数据：字符串 <br> 输出到单个文件： `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |
| 全部引用 | 将所有值括在引号中 | 否 | `true` 或 `false` | quoteAll |

### <a name="sink-example"></a>接收器示例

下图是用于映射数据流的分隔文本接收器配置的示例。

![DelimitedText 接收器](media/data-flow/delimited-text-sink.png)

关联的数据流脚本为：

```
CSVSource sink(allowSchemaDrift: true,
    validateSchema: false,
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CSVSink
```

## <a name="next-steps"></a>后续步骤

- [复制活动概述](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
