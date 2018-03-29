---
title: Azure 数据工厂中的文件和压缩格式 | Microsoft Docs
description: 了解 Azure 数据工厂支持的文件格式。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 02a3ca373d88f0a553b48214b0d06ce1fad73cb8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="file-and-compression-formats-supported-by-azure-data-factory"></a>Azure 数据工厂支持的文件和压缩格式
*本主题适用于以下连接器：[Amazon S3](data-factory-amazon-simple-storage-service-connector.md)、[Azure Blob](data-factory-azure-blob-connector.md)、[Azure Data Lake Store](data-factory-azure-datalake-connector.md)、[文件系统](data-factory-onprem-file-system-connector.md)、[FTP](data-factory-ftp-connector.md)、[HDFS](data-factory-hdfs-connector.md)、[HTTP](data-factory-http-connector.md) 和 [SFTP](data-factory-sftp-connector.md)。*

> [!NOTE]
> 本文适用于 Azure 数据工厂版本 1（即正式版 (GA)）。 如果使用数据工厂服务版本 2（即预览版），请参阅[数据工厂版本 2 中受支持的文件格式和压缩编解码器](../supported-file-formats-and-compression-codecs.md)。

Azure 数据工厂支持以下文件格式类型：

* [文本格式](#text-format)
* [JSON 格式](#json-format)
* [Avro 格式](#avro-format)
* [ORC 格式](#orc-format)
* [Parquet 格式](#parquet-format)

## <a name="text-format"></a>文本格式
如果想要读取或写入某个文本文件，请将数据集的 `format` 节中的 `type` 属性设置为 **TextFormat**。 也可在 `format` 节指定以下**可选**属性。 请参阅 [TextFormat 示例](#textformat-example)部分，了解如何进行配置。

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| columnDelimiter |用于分隔文件中的列的字符。 可以考虑使用数据中不太可能存在的极少见的不可打印字符。 例如，指定“\u0001”表示标题开头 (SOH)。 |只允许一个字符。 **默认**值为**逗号（“,”）**。 <br/><br/>若要使用 Unicode 字符，请参阅 [Unicode 字符](https://en.wikipedia.org/wiki/List_of_Unicode_characters)获取相应的代码。 |否 |
| rowDelimiter |用于分隔文件中的行的字符。 |只允许一个字符。 **默认**值为以下任何一项：**[“\r\n”、“\r”、“\n”]**（读取时）和**“\r\n”**（写入时）。 |否 |
| escapeChar |用于转义输入文件内容中的列分隔符的特殊字符。 <br/><br/>不能同时为表指定 escapeChar 和 quoteChar。 |只允许一个字符。 没有默认值。 <br/><br/>示例：如果以逗号（“,”）作为列分隔符，但想要在文本中使用逗号字符（例如：“Hello, world”），可以将“$”定义为转义符，在源中使用字符串“Hello$, world”。 |否 |
| quoteChar |括住字符串值的引号字符。 引号字符内的列和行分隔符被视为字符串值的一部分。 此属性同时适用于输入和输出数据集。<br/><br/>不能同时为表指定 escapeChar 和 quoteChar。 |只允许一个字符。 没有默认值。 <br/><br/>例如，如果以逗号（“,”）作为列分隔符，但想要在文本中使用逗号字符（例如：<Hello, world>），可以将 "（双引号）定义为引号字符，在源中使用字符串“Hello, world”。 |否 |
| nullValue |用于表示 null 值的一个或多个字符。 |一个或多个字符。 **默认**值为**“\N”和“NULL”**（读取时）及**“\N”**（写入时）。 |否 |
| encodingName |指定编码名称。 |有效的编码名称。 请参阅 [Encoding.EncodingName 属性](https://msdn.microsoft.com/library/system.text.encoding.aspx)。 例如：windows-1250 或 shift_jis。 **默认**值为 **UTF-8**。 |否 |
| firstRowAsHeader |指定是否将第一行视为标头。 对于输入数据集，数据工厂将读取第一行作为标头。 对于输出数据集，数据工厂将写入第一行作为标头。 <br/><br/>有关示例方案，请参阅 [`firstRowAsHeader` 和 `skipLineCount` 使用方案](#scenarios-for-using-firstrowasheader-and-skiplinecount)。 |True<br/><b>False（默认值）</b> |否 |
| skipLineCount |指示从输入文件读取数据时要跳过的行数。 如果同时指定了 skipLineCount 和 firstRowAsHeader，则先跳过代码行，然后从输入文件读取标头信息。 <br/><br/>有关示例方案，请参阅 [`firstRowAsHeader` 和 `skipLineCount` 使用方案](#scenarios-for-using-firstrowasheader-and-skiplinecount)。 |Integer |否 |
| treatEmptyAsNull |指定从输入文件读取数据时，是否将 null 或空字符串视为 null 值。 |**True（默认值）**<br/>False |否 |

### <a name="textformat-example"></a>TextFormat 示例
在某个数据集的以下 JSON 定义中，指定了一些可选属性。

```json
"typeProperties":
{
    "folderPath": "mycontainer/myfolder",
    "fileName": "myblobname",
    "format":
    {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";",
        "quoteChar": "\"",
        "NullValue": "NaN",
        "firstRowAsHeader": true,
        "skipLineCount": 0,
        "treatEmptyAsNull": true
    }
},
```

要使用 `escapeChar` 而非 `quoteChar`，请将 `quoteChar` 所在的行替换为以下 escapeChar：

```json
"escapeChar": "$",
```

### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>firstRowAsHeader 和 skipLineCount 的使用方案
* 要从非文件源复制到文本文件，并想要添加包含架构元数据（例如 SQL 架构）的标头行。 对于此方案，请在输出数据集中将 `firstRowAsHeader` 指定为 true。
* 要从包含标头行的文本文件复制到非文件接收器，并想要删除该行。 请在输入数据集中将 `firstRowAsHeader` 指定为 true。
* 要从文本文件复制，并想跳过不包含数据或标头信息的开头几行。 通过指定 `skipLineCount` 指明要跳过的行数。 如果文件的剩余部分包含标头行，则也可指定 `firstRowAsHeader`。 如果同时指定了 `skipLineCount` 和 `firstRowAsHeader`，则先跳过代码行，然后从输入文件读取标头信息

## <a name="json-format"></a>JSON 格式
要在 Azure Cosmos DB 中按原样导入/导出 JSON 文件，请参阅[将数据移入/移出 Cosmos DB](data-factory-azure-documentdb-connector.md) 一文中的[导入/导出 JSON 文档](data-factory-azure-documentdb-connector.md#importexport-json-documents)部分。

要分析 JSON 文件或以 JSON 格式写入数据，请将 `format` 节中的 `type` 属性设置为 **JsonFormat**。 也可在 `format` 节指定以下**可选**属性。 请参阅 [JsonFormat 示例](#jsonformat-example)部分，了解如何进行配置。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| filePattern |指示每个 JSON 文件中存储的数据模式。 允许的值为：**setOfObjects** 和 **arrayOfObjects**。 **默认**值为 **setOfObjects**。 请参阅 [JSON 文件模式](#json-file-patterns)部分，详细了解这些模式。 |否 |
| jsonNodeReference | 若要进行迭代操作，以同一模式从数组字段中的对象提取数据，请指定该数组的 JSON 路径。 只有从 JSON 文件复制数据时，才支持此属性。 | 否 |
| jsonPathDefinition | 为每个使用自定义列名映射的列指定 JSON 路径表达式（开头为小写）。 只有从 JSON 文件复制数据时，才支持此属性，而且用户可以从对象或数组提取数据。 <br/><br/> 对于根对象下的字段，请以根 $ 开头；对于按 `jsonNodeReference` 属性选择的数组中的字段，请以数组元素开头。 请参阅 [JsonFormat 示例](#jsonformat-example)部分，了解如何进行配置。 | 否 |
| encodingName |指定编码名称。 有关有效编码名称的列表，请参阅：[Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) 属性。 例如：windows-1250 或 shift_jis。 **默认**值为 **UTF-8**。 |否 |
| nestingSeparator |用于分隔嵌套级别的字符。 默认值为“.”（点）。 |否 |

### <a name="json-file-patterns"></a>JSON 文件模式

复制活动可分析以下 JSON 文件模式：

- **类型 I：setOfObjects**

    每个文件包含单个对象，或者以行分隔/串连的多个对象。 在输出数据集中选择此选项时，复制活动将生成单个 JSON 文件，其中每行包含一个对象（以行分隔）。

    * **单一对象 JSON 示例**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **行分隔的 JSON 示例**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **串连的 JSON 示例**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **类型 II：arrayOfObjects**

    每个文件包含对象的数组。

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

### <a name="jsonformat-example"></a>JsonFormat 示例

**案例 1：从 JSON 文件复制数据**

从 JSON 文件复制数据时，请参阅以下两个示例。 要注意的一般要点：

**示例 1：从对象和数组中提取数据**

在此示例中，预期要将一个根 JSON 对象映射到表格结果中的单个记录。 如果 JSON 文件包含以下内容：  

```json
{
    "id": "ed0e4960-d9c5-11e6-85dc-d7996816aad3",
    "context": {
        "device": {
            "type": "PC"
        },
        "custom": {
            "dimensions": [
                {
                    "TargetResourceType": "Microsoft.Compute/virtualMachines"
                },
                {
                    "ResourceManagmentProcessRunId": "827f8aaa-ab72-437c-ba48-d8917a7336a3"
                },
                {
                    "OccurrenceTime": "1/13/2017 11:24:37 AM"
                }
            ]
        }
    }
}
```
并且想要通过从对象和数组中提取数据，使用以下格式将该文件复制到 Azure SQL 表：

| id | deviceType | targetResourceType | resourceManagmentProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | PC | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 1/13/2017 11:24:37 AM |

**JsonFormat** 类型的输入数据集定义如下（部分定义，仅包含相关部件）。 更具体说来：

- `structure` 节定义自定义列名以及在转换为表格数据时的相应数据类型。 本节为**可选**，除非需要进行列映射。 有关详细信息，请参阅[将源数据集列映射到目标数据集列](data-factory-map-columns.md)。
- `jsonPathDefinition` 为每个列指定 JSON 路径，表明从何处提取数据。 若要从数组中复制数据，可以使用 **array[x].property** 从 xth 对象中提取给定属性的值，也可以使用 **array[*].property** 从包含此类属性的任何对象中查找该值。

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "deviceType",
            "type": "String"
        },
        {
            "name": "targetResourceType",
            "type": "String"
        },
        {
            "name": "resourceManagmentProcessRunId",
            "type": "String"
        },
        {
            "name": "occurrenceTime",
            "type": "DateTime"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonPathDefinition": {"id": "$.id", "deviceType": "$.context.device.type", "targetResourceType": "$.context.custom.dimensions[0].TargetResourceType", "resourceManagmentProcessRunId": "$.context.custom.dimensions[1].ResourceManagmentProcessRunId", "occurrenceTime": " $.context.custom.dimensions[2].OccurrenceTime"}      
        }
    }
}
```

**示例 2：使用数组中的相同模式交叉应用多个对象**

在此示例中，预期要将一个根 JSON 对象转换为表格结果中的多个记录。 如果 JSON 文件包含以下内容：  

```json
{
    "ordernumber": "01",
    "orderdate": "20170122",
    "orderlines": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "sanmateo": "No 1" } ]
}
```
而用户需要按以下格式通过平展数组中数据的方式将其复制到 Azure SQL 表中，并使用常见的根信息进行交叉联接：

| ordernumber | orderdate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P2 | 13 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P3 | 231 | [{"sanmateo":"No 1"}] |

**JsonFormat** 类型的输入数据集定义如下（部分定义，仅包含相关部件）。 更具体说来：

- `structure` 节定义自定义列名以及在转换为表格数据时的相应数据类型。 本节为**可选**，除非需要进行列映射。 有关详细信息，请参阅[将源数据集列映射到目标数据集列](data-factory-map-columns.md)。
- `jsonNodeReference` 指示进行迭代操作，在**数组**订单行下以同一模式从对象提取数据。
- `jsonPathDefinition` 为每个列指定 JSON 路径，表明从何处提取数据。 在以下示例中，"ordernumber"、"orderdate" 和 "city" 位于 JSON 路径以“$.”开头的根对象下，而 "order_pd" 和 "order_price" 在定义时使用的路径派生自没有“$.”的数组元素。

```json
"properties": {
    "structure": [
        {
            "name": "ordernumber",
            "type": "String"
        },
        {
            "name": "orderdate",
            "type": "String"
        },
        {
            "name": "order_pd",
            "type": "String"
        },
        {
            "name": "order_price",
            "type": "Int64"
        },
        {
            "name": "city",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonNodeReference": "$.orderlines",
            "jsonPathDefinition": {"ordernumber": "$.ordernumber", "orderdate": "$.orderdate", "order_pd": "prod", "order_price": "price", "city": " $.city"}         
        }
    }
}
```

**请注意以下几点：**

* 如果未在数据工厂数据集中定义 `structure` 和 `jsonPathDefinition`，复制活动将检测第一个对象的架构，并平展整个对象。
* 如果 JSON 输入包含数组，则默认情况下，复制活动会将整个数组值转换为字符串。 可以选择使用 `jsonNodeReference` 和/或 `jsonPathDefinition` 通过它提取数据，也可以不在 `jsonPathDefinition` 中指定它，从而将它跳过。
* 如果同一级别存在重复的名称，复制活动将选择最后一个名称。
* 属性名称区分大小写。 名称相同但大小写不同的两个属性被视为两个不同的属性。

**案例 2：将数据写入 JSON 文件**

如果 SQL 数据库中存在以下表：

| id | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

每个记录将按以下格式写入到 JSON 对象中：
```json
{
    "id": "1",
    "order": {
        "date": "20170119",
        "price": 2000,
        "customer": "David"
    }
}
```

**JsonFormat** 类型的输出数据集定义如下（部分定义，仅包含相关部件）。 更具体说来，`structure` 节用于定义目标文件中的自定义属性名称，`nestingSeparator`（默认为“.”）则用于标识名称中的嵌套层。 本节为**可选**，除非需要将属性名称更改为与源列名不同的名称，或者需要嵌套部分属性。

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "order.date",
            "type": "String"
        },
        {
            "name": "order.price",
            "type": "Int64"
        },
        {
            "name": "order.customer",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat"
        }
    }
}
```

## <a name="avro-format"></a>AVRO 格式
若要分析 Avro 文件或以 Avro 格式写入数据，请将 `format` `type` 属性设置为 **AvroFormat**。 不需在 typeProperties 节的 Format 节中指定任何属性。 示例：

```json
"format":
{
    "type": "AvroFormat",
}
```

若要在 Hive 表中使用 Avro 格式，可以参考 [Apache Hive 教程](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe)。

请注意以下几点：  

* 不支持[复杂数据类型](http://avro.apache.org/docs/current/spec.html#schema_complex)（记录、枚举、数组、映射、联合与固定值）。

## <a name="orc-format"></a>ORC 格式
若要分析 ORC 文件或以 ORC 格式写入数据，请将 `format` `type` 属性设置为 **OrcFormat**。 不需在 typeProperties 节的 Format 节中指定任何属性。 示例：

```json
"format":
{
    "type": "OrcFormat"
}
```

> [!IMPORTANT]
> 如果不是在本地与云数据存储之间**按原样**复制 ORC 文件，则需要在网关计算机上安装 JRE 8（Java 运行时环境）。 64 位网关需要 64 位 JRE，32 位网关需要 32 位 JRE。 可以从[此处](http://go.microsoft.com/fwlink/?LinkId=808605)找到这两个版本。 请选择适当的版本。
>
>

请注意以下几点：

* 不支持复杂数据类型（STRUCT、MAP、LIST、UNION）
* ORC 文件有三个[压缩相关的选项](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/)：NONE、ZLIB、SNAPPY。 数据工厂支持从使用其中任一压缩格式的 ORC 文件中读取数据。 它使用元数据中的压缩编解码器来读取数据。 但是，写入 ORC 文件时，数据工厂会选择 ZLIB，这是 ORC 的默认选项。 目前没有任何选项可以重写此行为。

## <a name="parquet-format"></a>Parquet 格式
若要分析 Parquet 文件或以 Parquet 格式写入数据，请将 `format` `type` 属性设置为 **ParquetFormat**。 不需在 typeProperties 节的 Format 节中指定任何属性。 示例：

```json
"format":
{
    "type": "ParquetFormat"
}
```
> [!IMPORTANT]
> 如果不是在本地与云数据存储之间**按原样**复制 Parquet 文件，则需要在网关计算机上安装 JRE 8（Java 运行时环境）。 64 位网关需要 64 位 JRE，32 位网关需要 32 位 JRE。 可以从[此处](http://go.microsoft.com/fwlink/?LinkId=808605)找到这两个版本。 请选择适当的版本。
>
>

请注意以下几点：

* 不支持复杂数据类型（MAP、LIST）
* Parquet 文件提供以下压缩相关的选项：NONE、SNAPPY、GZIP 和 LZO。 数据工厂支持从使用其中任一压缩格式的 ORC 文件中读取数据。 它使用元数据中的压缩编解码器来读取数据。 但是，写入 Parquet 文件时，数据工厂会选择 SNAPPY，这是 Parquet 格式的默认选项。 目前没有任何选项可以重写此行为。

## <a name="compression-support"></a>压缩支持
处理大型数据集可能导致 I/O 和网络瓶颈。 因此，存储中的压缩数据不但可以跨网络加速数据传输和节省磁盘空间，也能在处理大数据时带来显著的性能提升。 目前，Azure Blob 或本地文件系统等基于文件的数据存储支持压缩。  

若要为数据集指定压缩，请在数据集 JSON 中使用 **compression** 属性，如以下示例所示：   

```json
{  
    "name": "AzureBlobDataSet",  
    "properties": {  
        "availability": {  
            "frequency": "Day",  
              "interval": 1  
        },  
        "type": "AzureBlob",  
        "linkedServiceName": "StorageLinkedService",  
        "typeProperties": {  
            "fileName": "pagecounts.csv.gz",  
            "folderPath": "compression/file/",  
            "compression": {  
                "type": "GZip",  
                "level": "Optimal"  
            }  
        }  
    }  
}  
```

假设示例数据集作为复制活动的输出，复制活动通过使用最佳比率的 GZIP 编解码器压缩输出数据，然后将压缩的数据写入 Azure Blob 存储中名为 pagecounts.csv.gz 的文件。

> [!NOTE]
> 目前采用 **AvroFormat**、**OrcFormat** 或 **ParquetFormat** 的数据不支持压缩设置。 读取采用这些格式的文件时，数据工厂将检测并使用元数据中的压缩编解码器。 在采用这些格式的文件中写入数据时，数据工厂将选择适用于该格式的默认压缩编解码器。 例如，为 OrcFormat 使用 ZLIB，为 ParquetFormat 使用 SNAPPY。   

**compression** 节包含两个属性：  

* **Type：**压缩编解码器，可以是 **GZIP**、**Deflate**、**BZIP2** 或 **ZipDeflate**。  
* **Level：**压缩比，可以是 **Optimal** 或 **Fastest**。

  * **Fastest：**尽快完成压缩操作，不过，无法以最佳方式压缩生成的文件。
  * **Optimal：**以最佳方式完成压缩操作，不过，需要耗费更长的时间。

    有关详细信息，请参阅 [Compression Level](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx)（压缩级别）主题。

在输入数据集 JSON 中指定 `compression` 属性时，管道可以从源读取压缩的数据；在输出数据集 JSON 中指定属性时，复制活动可以将压缩的数据写入到目标。 下面是一些示例方案：

* 从 Azure Blob 读取 GZIP 压缩的数据，将其解压缩，然后将结果数据写入 Azure SQL 数据库。 在此情况下，可以使用值为 GZIP 的 `compression` `type` JSON 属性来定义输入 Azure Blob 数据集。
* 从来自本地文件系统的纯文本文件读取数据、使用 GZip 格式进行压缩并将压缩的数据写入到 Azure Blob。 在此情况下，可以使用值为 GZip 的 `compression` `type` JSON 属性来定义输出 Azure Blob 数据集。
* 从 FTP 服务器读取 .zip 文件，将它解压缩以获取文件内容，然后将这些文件加入 Azure Data Lake Store。 可以使用值为 ZipDeflate 的 `compression` `type` JSON 属性来定义输入 FTP 数据集。
* 从 Azure Blob 读取 GZIP 压缩的数据，将其解压缩、使用 BZIP2 将其压缩，然后将结果数据写入 Azure Blob。 在此情况下，可以使用设置为 GZIP 的 `compression` `type` 来定义输入 Azure Blob 数据集，使用设置为 BZIP2 的 `compression` `type` 来定义输出数据集。   


## <a name="next-steps"></a>后续步骤
请参阅以下文章，了解 Azure 数据工厂支持的基于文件的数据存储：

- [Azure Blob 存储](data-factory-azure-blob-connector.md)
- [Azure Data Lake Store](data-factory-azure-datalake-connector.md)
- [FTP](data-factory-ftp-connector.md)
- [HDFS](data-factory-hdfs-connector.md)
- [文件系统](data-factory-onprem-file-system-connector.md)
- [Amazon S3](data-factory-amazon-simple-storage-service-connector.md)
