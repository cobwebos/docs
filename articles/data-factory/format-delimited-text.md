---
title: Azure 数据工厂中的分隔文本格式 |Microsoft Docs
description: 本主题介绍如何在 Azure 数据工厂中处理分隔文本格式。
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: fb836b44ebd567f0ce1c833ca523b1c199ed9c9a
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72785993"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Azure 数据工厂中的分隔文本格式

如果要**分析分隔的文本文件或将数据写入分隔文本格式**，请遵循此文。 

以下连接器支持带分隔符的文本格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [azure 文件存储](connector-azure-file-storage.md)、[文件系统](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和[SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供分隔文本数据集支持的属性列表。

| properties         | 描述                                                  | 需要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 数据集的 type 属性必须设置为**DelimitedText**。 | 是      |
| 位置         | 文件的位置设置。 每个基于文件的连接器都具有其自己的位置类型和 `location` 下支持的属性。  | 是      |
| columnDelimiter  | 用于分隔文件中的列的字符。 目前，仅支持将多字符分隔符用于映射数据流，而不支持复制活动。 <br>如果将列分隔符定义为空字符串（表示没有分隔符），则默认值为**逗号 `,`** ，整行被视为单个列。 | No       |
| rowDelimiter     | 用于分隔文件中的行的单个字符或 "\r\n"。<br>默认值为读取时的以下任何值 **： ["\r\n"、"\r"、"\n"]** 、 **"n" 或 "\r\n"** （通过分别映射数据流和复制活动进行写入）。 <br>将 `rowDelimiter` 设置为 "无分隔符（空字符串）" 时，还必须将 `columnDelimiter` 设置为 "无分隔符（空字符串）"，这意味着将整个内容视为单个值。 | No       |
| quoteChar        | 如果包含列分隔符，则为要将列值括起来的单个字符。 <br>默认值为**双引号**`"`。 <br>对于映射数据流，`quoteChar` 不能是空字符串。 <br>对于复制活动，当 `quoteChar` 定义为空字符串时，表示没有引号 char 和列值未加引号，而 `escapeChar` 用于转义列分隔符和本身。 | No       |
| escapeChar       | 用于转义带引号的值中的引号的单个字符。<br>默认值为**反斜杠 `\`** 。 <br>对于映射数据流，`escapeChar` 不能是空字符串。 <br/>对于复制活动，在 `escapeChar` 定义为空字符串时，还必须将 `quoteChar` 设置为空字符串，在这种情况下，请确保所有列值都不包含分隔符。 | No       |
| firstRowAsHeader | 指定是否将第一行视为具有列名称的标题行。<br>允许的值为**true**和**false** （默认值）。 | No       |
| nullValue        | 指定 null 值的字符串表示形式。 <br>默认值为**空字符串**。 | No       |
| encodingName     | 用于读取/写入测试文件的编码类型。 <br>允许的值如下： "UTF-8"、"UTF-16"、"UTF-16BE"、"UTF-32"、"32BE"、"US-ASCII"、"UTF-7"、"BIG5"、"EUC-JP"、"EUC-KR"、"GB2312"、"GB18030"、"JOHAB"、"SHIFT-JIS"、"CP875"、"CP866"、"IBM00858"、"IBM037"、"IBM273"、"IBM437"、"IBM500"、""、""、"IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149"、"ISO-2022-JP"、"ISO-2022-KR"、"ISO-8859-1"、"ISO-8859-2"、"ISO-8859-3"、"ISO-8859-4"、"ISO-8859-5"、"ISO-8859-6"、"ISO-8859-7"、"ISO-8859-8"、"ISO-8859-9"、"ISO-8859-13"、"ISO-8859-15"、"WINDOWS-874"、"WINDOWS-1250"、"WINDOWS-1251"、"WINDOWS-1252 "、" WINDOWS-1253 "、" WINDOWS-1254 "、" WINDOWS-1255 "、" WINDOWS-1256 "、" WINDOWS-1257 "、" WINDOWS-1258 "。<br>注意映射数据流不支持 UTF-7 编码。 | No       |
| compressionCodec | 用于读取/写入文本文件的压缩编解码器。 <br>允许的值为**bzip2**、 **gzip**、 **deflate**、 **ZipDeflate**、 **snappy**或**lz4**。 保存文件时要使用的。 <br>注意当前复制活动不支持 "snappy" & "lz4"，并且映射数据流不支持 "ZipDeflate"。 | No       |
| compressionLevel | 压缩比。 <br>允许的值为 "**最佳**" 或 "**最快**"。<br>- **速度最快：** 压缩操作应该尽快完成，即使生成的文件未以最佳方式压缩。<br>- **最优**：应以最佳方式压缩压缩操作，即使操作需要更长的时间才能完成。 有关详细信息，请参阅 [Compression Level](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx)（压缩级别）主题。 | No       |

下面是在 Azure Blob 存储上分隔的文本数据集的示例：

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

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供分隔文本源和接收器支持的属性列表。

### <a name="delimited-text-as-source"></a>分隔文本作为源 

复制活动***\*源\**** 部分支持以下属性。

| properties       | 描述                                                  | 需要 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | 复制活动源的 type 属性必须设置为**DelimitedTextSource**。 | 是      |
| formatSettings | 一组属性。 请参阅下面的**分隔文本读取设置**表。 | No       |
| storeSettings  | 有关如何从数据存储区中读取数据的一组属性。 每个基于文件的连接器在 `storeSettings` 下具有其自己支持的读取设置。 | No       |

`formatSettings`下支持的**分隔文本读取设置**：

| properties      | 描述                                                  | 需要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | FormatSettings 的类型必须设置为**DelimitedTextReadSetting**。 | 是      |
| skipLineCount | 指示从输入文件读取数据时要跳过的非空行数。 <br>如果同时指定了 skipLineCount 和 firstRowAsHeader，则先跳过代码行，然后从输入文件读取标头信息。 | No       |

### <a name="delimited-text-as-sink"></a>分隔文本作为接收器

复制活动***\*接收器\**** 部分支持以下属性。

| properties       | 描述                                                  | 需要 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | 复制活动源的 type 属性必须设置为**DelimitedTextSink**。 | 是      |
| formatSettings | 一组属性。 请参阅下面的**分隔文本写入设置**表。 |          |
| storeSettings  | 如何将数据写入数据存储区的一组属性。 每个基于文件的连接器在 `storeSettings` 下具有其自己的支持的写入设置。  | No       |

`formatSettings`下支持的**分隔文本写入设置**：

| properties      | 描述                                                  | 需要                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | FormatSettings 的类型必须设置为**DelimitedTextWriteSetting**。 | 是                                                   |
| fileExtension | 用于命名输出文件的文件扩展名，例如 `.csv`，`.txt`。 如果未在输出 DelimitedText 数据集中指定 `fileName`，则必须指定该方法。 | 在输出数据集中未指定文件名时为 "是" |

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流中了解[源转换](data-flow-source.md)和[接收器转换](data-flow-sink.md)中的详细信息。

## <a name="next-steps"></a>后续步骤

- [复制活动概述](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
