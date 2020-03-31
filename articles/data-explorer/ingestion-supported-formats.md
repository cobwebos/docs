---
title: Azure 数据资源管理器支持用于引入的数据格式。
description: 了解 Azure 数据资源管理器支持的各种数据和压缩格式以进行引入。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 16ecdf7ac3f0062637e4bbea86d26e2560f38222
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235301"
---
# <a name="data-formats-supported-by-azure-data-explorer-for-ingestion"></a>Azure 数据资源管理器支持用于引入的数据格式

数据引入是将数据添加到表中并可在 Azure 数据资源管理器中查询的过程。 对于除从查询引入外的所有引入方法，数据必须采用受支持的格式之一。 下表列出了 Azure 数据资源管理器支持的数据引入的格式。

|格式   |分机   |描述|
|---------|------------|-----------|
|Avro     |`.avro`     |一个 [Avro 容器文件](https://avro.apache.org/docs/current/)。 以下代码受支持：`null`、`deflate`（当前不支持 `snappy`）。|
|CSV      |`.csv`      |一个采用逗号分隔值 (`,`) 的文本文件。 请参阅[RFC 4180：_逗号分隔值 （CSV） 文件的常见格式和 MIME 类型_](https://www.ietf.org/rfc/rfc4180.txt)。|
|JSON     |`.json`     |一个文本文件，其中包含使用 `\n` 或 `\r\n` 分隔的 JSON 对象。 请参阅 [JSON Lines (JSONL)](http://jsonlines.org/)。|
|多伊森|`.multijson`|一个文本文件，包含一个由属性包（每个包代表一条记录）组成的 JSON 数组，或者包含通过空格、`\n` 或 `\r\n` 分隔的任意数目的属性包。 每个属性包可以分布在多个行上。 此格式优先于`JSON`，除非数据是非属性包。|
|ORC      |`.orc`      |[Orc 文件](https://en.wikipedia.org/wiki/Apache_ORC)。|
|Parquet  |`.parquet`  |一个 [Parquet 文件](https://en.wikipedia.org/wiki/Apache_Parquet)。|
|Psv      |`.psv`      |一个采用竖线分隔值 (<code>&#124;</code>) 的文本文件。|
|RAW      |`.raw`      |一个文本文件，其整个内容就是一个字符串值。|
|SCsv     |`.scsv`     |一个采用分号分隔值 (`;`) 的文本文件。|
|SOHsv    |`.sohsv`    |一个采用 SOH 分隔值的文本文件。 （SOH 为 ASCII 代码点 1；此格式由 Hive on HDInsight 使用。）|
|TSV      |`.tsv`      |一个采用制表符分隔值 (`\t`) 的文本文件。|
|TSVE     |`.tsv`      |一个采用制表符分隔值 (`\t`) 的文本文件。 反斜杠字符 (`\`) 用于转义。|
|TXT      |`.txt`      |一个文本文件，使用 `\n` 分隔的行。 空行将被跳过。|

## <a name="supported-data-compression-formats"></a>支持的数据压缩格式

可通过以下任一压缩算法压缩 Blob 和文件：

|压缩|分机|
|-----------|---------|
|GZip       |.gz      |
|Zip        |.zip     |

通过将扩展名追加到 blob 或文件的名称来指示压缩。

例如：
* `MyData.csv.zip`指示 blob 或格式化为 CSV 的文件，使用 ZIP 压缩（存档或单个文件）
* `MyData.csv.gz`指示使用 GZip 压缩的 blob 或格式化为 CSV 的文件

还支持不包含格式扩展名但仅包含压缩（例如）的 Blob 或文件名。 在这种情况下，必须将文件格式指定为引入属性，因为不能对它进行推断。

> [!NOTE]
> 某些压缩格式会在压缩的流中记录原始文件扩展名。 在确定文件格式时，通常会忽略此扩展名。 如果不能根据（压缩的）Blob 或文件的名称确定文件格式，则必须通过 `format` 引入属性指定它。

## <a name="next-steps"></a>后续步骤

* 了解有关[数据引入](/azure/data-explorer/ingest-data-overview)的更多
* 了解有关[Azure 数据资源管理器数据引入属性](ingestion-properties.md)的更多
