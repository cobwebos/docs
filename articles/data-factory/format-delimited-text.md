---
title: Azure 数据工厂中带分隔符的文本格式 | Microsoft Docs
description: 本主题介绍了如何处理 Azure 数据工厂中带分隔符的文本格式。
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 407b8ba2fda35d3acbf1b425bb15fe20778613d7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65146007"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Azure 数据工厂中带分隔符的文本格式

若要**分析带分隔符的文本文件或以带分隔符的文本格式写入数据**，请遵循此文章中的说明。 

以下连接器支持带分隔符的文本格式：[Amazon S3](connector-amazon-simple-storage-service.md)， [Azure Blob](connector-azure-blob-storage.md)， [Azure 数据湖存储 Gen1](connector-azure-data-lake-store.md)， [Azure 数据湖存储第 2 代](connector-azure-data-lake-storage.md)， [Azure 文件存储](connector-azure-file-storage.md)，[文件系统](connector-file-system.md)， [FTP](connector-ftp.md)， [Google 云存储](connector-google-cloud-storage.md)， [HDFS](connector-hdfs.md)， [HTTP](connector-http.md)，和[SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供带分隔符的文本数据集支持的属性列表。

| 属性         | 说明                                                  | 必选 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 数据集的类型属性必须设置为 **DelimitedText**。 | 是      |
| 位置         | 文件的位置设置。 每个基于文件的连接器在 `location` 下都有其自己的位置类型和支持的属性。 **请在连接器文章 -> 数据集属性部分中查看详细信息**。 | 是      |
| columnDelimiter  | 用于分隔文件中的列的字符。 目前，只有映射数据流支持多字符分隔符，而复制活动则不支持。 <br>默认值为**逗号 `,`** 。当列分隔符定义为空字符串（表示没有分隔符）时，整行将被视为单个列。 | 否       |
| rowDelimiter     | 用于分隔文件中的行的单个字符或“\r\n”。<br>由映射数据流和复制活动读取或写入时，默认值分别为以下任一值：["\r\n", "\r", "\n"]，以及“\n”或“\r\n”。   <br>当 `rowDelimiter` 设置为无分隔符（空字符串）时，`columnDelimiter` 必须也设置为无分隔符（空字符串），表示将整个内容视为单个值。 | 否       |
| quoteChar        | 当列包含列分隔符时，用于括住列值的单个字符。 <br>默认值为**双引号** `"`。 <br>对于映射数据流，`quoteChar` 不能为空字符串。 <br>对于复制活动，当 `quoteChar` 定义为空字符串时，表示不使用引号字符且不用引号括住列值，`escapeChar` 用于转义列分隔符和值本身。 | 否       |
| escapeChar       | 用于转义括住值中的引号的单个字符。<br>默认值为**反斜杠`\`** 。 <br>对于映射数据流，`escapeChar` 不能为空字符串。 <br/>对于复制活动，当 `escapeChar` 定义为空字符串时，`quoteChar` 也必须设置为空字符串，在这种情况下，应确保所有列值不包含分隔符。 | 否       |
| firstRowAsHeader | 指定是否要将第一行视为/设为包含列名称的标头行。<br>允许的值为 **true** 和 **true**（默认值）。 | 否       |
| nullValue        | 指定 null 值的字符串表示形式。 <br>默认值为**空字符串**。 | 否       |
| encodingName     | 用于读取/写入测试文件的编码类型。 <br>可用的值如下："UTF-8"、"UTF-16"、"UTF-16BE"、"UTF-32"、"UTF-32BE"、"US-ASCII"、“UTF-7”、"BIG5"、"EUC-JP"、"EUC-KR"、"GB2312"、"GB18030"、"JOHAB"、"SHIFT-JIS"、"CP875"、"CP866"、"IBM00858"、"IBM037"、"IBM273"、"IBM437"、"IBM500"、"IBM737"、"IBM775"、"IBM850"、"IBM852"、"IBM855"、"IBM857"、"IBM860"、"IBM861"、"IBM863"、"IBM864"、"IBM865"、"IBM869"、"IBM870"、"IBM01140"、"IBM01141"、"IBM01142"、"IBM01143"、"IBM01144"、"IBM01145"、"IBM01146"、"IBM01147"、"IBM01148"、"IBM01149"、"ISO-2022-JP"、"ISO-2022-KR"、"ISO-8859-1"、"ISO-8859-2"、"ISO-8859-3"、"ISO-8859-4"、"ISO-8859-5"、"ISO-8859-6"、"ISO-8859-7"、"ISO-8859-8"、"ISO-8859-9"、"ISO-8859-13"、"ISO-8859-15"、"WINDOWS-874"、"WINDOWS-1250"、"WINDOWS-1251"、"WINDOWS-1252"、"WINDOWS-1253"、"WINDOWS-1254"、"WINDOWS-1255"、"WINDOWS-1256"、"WINDOWS-1257"、"WINDOWS-1258”。<br>请注意，映射数据流不支持 UTF-7 编码。 | 否       |
| compressionCodec | 用于读取/写入文本文件的压缩编解码器。 <br>允许的值为 **bzip2**、**gzip**、**deflate**、**ZipDeflate**、**snappy** 或 **lz4**。 保存文件时使用。 <br>请注意，复制活动目前不支持“snappy”和“lz4”，映射数据流不支持“ZipDeflate”。 | 否       |
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

| 属性       | 说明                                                  | 必选 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | 复制活动源的 type 属性必须设置为 **DelimitedTextSource**。 | 是      |
| formatSettings | 一组属性。 请参阅下面的“带分隔符的文本读取设置”表。  | 否       |
| storeSettings  | 有关如何从数据存储读取数据的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自己支持的读取设置。 **请在连接器文章 -> 复制活动属性部分中查看详细信息**。 | 否       |

`formatSettings` 下支持的**带分隔符的文本读取设置**：

| 属性      | 说明                                                  | 必选 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | formatSettings 的类型必须设置为 **DelimitedTextReadSetting**。 | 是      |
| skipLineCount | 指示从输入文件读取数据时要跳过的非空行数  。 <br>如果同时指定了 skipLineCount 和 firstRowAsHeader，则先跳过代码行，然后从输入文件读取标头信息。 | 否       |

### <a name="delimited-text-as-sink"></a>带分隔符的文本作为接收器

复制活动的 ***\*sink\**** 节支持以下属性。

| 属性       | 说明                                                  | 必选 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | 复制活动源的 type 属性必须设置为 **DelimitedTextSink**。 | 是      |
| formatSettings | 一组属性。 请参阅下面的“带分隔符的文本写入设置”表。  |          |
| storeSettings  | 有关如何将数据写入到数据存储的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自身支持的写入设置。 **请在连接器文章 -> 复制活动属性部分中查看详细信息**。 | 否       |

`formatSettings` 下支持的**带分隔符的文本写入设置**：

| 属性      | 说明                                                  | 必选                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | formatSettings 的类型必须设置为 **DelimitedTextWriteSetting**。 | 是                                                   |
| fileExtension | 用来为输出文件命名的扩展名，例如 `.csv`、`.txt`。 未在 DelimitedText 输出数据集中指定 `fileName` 时，必须指定该扩展名。 | 未在输出数据集中指定文件名时为“是” |

## <a name="mapping-data-flow-properties"></a>映射数据流属性

从“映射数据流”中的[源转换](data-flow-source.md)和[接收器转换](data-flow-sink.md)了解详细信息。

## <a name="next-steps"></a>后续步骤

- [复制活动概述](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)