## <a name="specifying-formats"></a>指定格式
Azure 数据工厂支持以下格式类型：

* [文本格式](#specifying-textformat)
* [JSON 格式](#specifying-jsonformat)
* [Avro 格式](#specifying-avroformat)
* [ORC 格式](#specifying-orcformat)
* [Parquet 格式](#specifying-parquetformat)

### <a name="specifying-textformat"></a>指定 TextFormat
若要分析文本文件或以文本格式写入数据，请将 `format` `type` 属性设置为 **TextFormat**。 也可在 `format` 节指定以下**可选**属性。 请参阅 [TextFormat 示例](#textformat-example)部分，了解如何进行配置。

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| columnDelimiter |用于分隔文件中的列的字符。 可以考虑使用数据中不太可能存在的极少见的不可打印字符：例如，指定“\u0001”表示标题开头 (SOH)。 |只允许一个字符。 **默认**值为**逗号（“,”）**。 <br/><br/>若要使用 Unicode 字符，请参阅 [Unicode 字符](https://en.wikipedia.org/wiki/List_of_Unicode_characters)获取相应的代码。 |否 |
| rowDelimiter |用于分隔文件中的行的字符。 |只允许一个字符。 **默认**值为以下任何一项：**[“\r\n”、“\r”、“\n”]**（读取时）和**“\r\n”**（写入时）。 |否 |
| escapeChar |用于转义输入文件内容中的列分隔符的特殊字符。 <br/><br/>不能同时为表指定 escapeChar 和 quoteChar。 |只允许一个字符。 没有默认值。 <br/><br/>示例：如果以逗号（“,”）作为列分隔符，但想要在文本中使用逗号字符（例如：“Hello, world”），可以将“$”定义为转义符，在源中使用字符串“Hello$, world”。 |否 |
| quoteChar |括住字符串值的引号字符。 引号字符内的列和行分隔符被视为字符串值的一部分。 此属性同时适用于输入和输出数据集。<br/><br/>不能同时为表指定 escapeChar 和 quoteChar。 |只允许一个字符。 没有默认值。 <br/><br/>例如，如果以逗号（“,”）作为列分隔符，但想要在文本中使用逗号字符（例如：<Hello, world>），可以将 "（双引号）定义为引号字符，在源中使用字符串“Hello, world”。 |否 |
| nullValue |用于表示 null 值的一个或多个字符。 |一个或多个字符。 **默认**值为**“\N”和“NULL”**（读取时）及**“\N”**（写入时）。 |否 |
| encodingName |指定编码名称。 |有效的编码名称。 请参阅 [Encoding.EncodingName 属性](https://msdn.microsoft.com/library/system.text.encoding.aspx)。 例如：windows-1250 或 shift_jis。 **默认**值为 **UTF-8**。 |否 |
| firstRowAsHeader |指定是否将第一行视为标头。 对于输入数据集，数据工厂将读取第一行作为标头。 对于输出数据集，数据工厂将写入第一行作为标头。 <br/><br/>有关示例方案，请参阅 [`firstRowAsHeader` 和 `skipLineCount` 使用方案](#scenarios-for-using-firstrowasheader-and-skiplinecount)。 |True<br/>**False（默认值）** |否 |
| skipLineCount |指示从输入文件读取数据时要跳过的行数。 如果同时指定了 skipLineCount 和 firstRowAsHeader，则先跳过代码行，然后从输入文件读取标头信息。 <br/><br/>有关示例方案，请参阅 [`firstRowAsHeader` 和 `skipLineCount` 使用方案](#scenarios-for-using-firstrowasheader-and-skiplinecount)。 |Integer |否 |
| treatEmptyAsNull |指定从输入文件读取数据时，是否将 null 或空字符串视为 null 值。 |**True（默认值）**<br/>False |否 |

#### <a name="textformat-example"></a>TextFormat 示例
以下示例显示 TextFormat 的一些格式属性。

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

若要使用 `escapeChar` 而非 `quoteChar`，请将 `quoteChar` 所在的行替换为以下 escapeChar：

```json
"escapeChar": "$",
```

#### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>firstRowAsHeader 和 skipLineCount 的使用方案
* 你要从非文件源复制到文本文件，并想要添加包含架构元数据（例如 SQL 架构）的标头行。 对于此方案，请在输出数据集中将 `firstRowAsHeader` 指定为 true。
* 你要从包含标头行的文本文件复制到非文件接收器，并想要删除该行。 请在输入数据集中将 `firstRowAsHeader` 指定为 true。
* 你要从文本文件复制，并想跳过不包含数据或标头信息的开头几行。 通过指定 `skipLineCount` 指明要跳过的行数。 如果文件的剩余部分包含标头行，则也可指定 `firstRowAsHeader`。 如果同时指定了 `skipLineCount` 和 `firstRowAsHeader`，则先跳过代码行，然后从输入文件读取标头信息

### <a name="specifying-jsonformat"></a>指定 JsonFormat
若要**在 Azure Cosmos DB 中按原样导入/导出 JSON 文件**，请参阅 Azure Cosmos DB 连接器中的[导入/导出 JSON 文档](../articles/data-factory/data-factory-azure-documentdb-connector.md#importexport-json-documents)部分，了解详细信息。

若要分析 JSON 文件或以 JSON 格式写入数据，请将 `format` `type` 属性设置为 **JsonFormat**。 也可在 `format` 节指定以下**可选**属性。 请参阅 [JsonFormat 示例](#jsonformat-example)部分，了解如何进行配置。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| filePattern |指示每个 JSON 文件中存储的数据模式。 允许的值为：**setOfObjects** 和 **arrayOfObjects**。 **默认**值为 **setOfObjects**。 请参阅 [JSON 文件模式](#json-file-patterns)部分，详细了解这些模式。 |否 |
| jsonNodeReference | 若要进行迭代操作，以同一模式从数组字段中的对象提取数据，请指定该数组的 JSON 路径。 只有从 JSON 文件复制数据时，才支持此属性。 | 否 |
| jsonPathDefinition | 为每个使用自定义列名映射的列指定 JSON 路径表达式（开头为小写）。 只有从 JSON 文件复制数据时，才支持此属性，而且用户可以从对象或数组提取数据。 <br/><br/> 对于根对象下的字段，请以根 $ 开头；对于按 `jsonNodeReference` 属性选择的数组中的字段，请以数组元素开头。 请参阅 [JsonFormat 示例](#jsonformat-example)部分，了解如何进行配置。 | 否 |
| encodingName |指定编码名称。 有关有效编码名称的列表，请参阅：[Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) 属性。 例如：windows-1250 或 shift_jis。 **默认**值为 **UTF-8**。 |否 |
| nestingSeparator |用于分隔嵌套级别的字符。 默认值为“.”（点）。 |否 |

#### <a name="json-file-patterns"></a>JSON 文件模式

复制活动可以分析下面的 JSON 文件模式：

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

#### <a name="jsonformat-example"></a>JsonFormat 示例

**案例 1：从 JSON 文件复制数据**

从 JSON 文件复制数据时，请参阅下面两种类型的示例，以及要注意的一般要点：

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
并且你想要通过从对象和数组中提取数据，使用以下格式将该文件复制到 Azure SQL 表：

| id | deviceType | targetResourceType | resourceManagmentProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | PC | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 1/13/2017 11:24:37 AM |

**JsonFormat** 类型的输入数据集定义如下（部分定义，仅包含相关部件）。 更具体说来：

- `structure` 节定义自定义列名以及在转换为表格数据时的相应数据类型。 本节为**可选**，除非你需要进行列映射。 请参阅[指定矩形数据集的结构定义](#specifying-structure-definition-for-rectangular-datasets)部分，了解更多详细信息。
- `jsonPathDefinition` 为每个列指定 JSON 路径，表明从何处提取数据。 若要从数组中复制数据，可以使用 **array[x].property** 从 xth 对象中提取给定属性的值，或者使用 **array[*].property** 从包含此类属性的任何对象中查找该值。

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

- `structure` 节定义自定义列名以及在转换为表格数据时的相应数据类型。 本节为**可选**，除非你需要进行列映射。 请参阅[指定矩形数据集的结构定义](#specifying-structure-definition-for-rectangular-datasets)部分，了解更多详细信息。
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

如果 SQL 数据库中存在下表：

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

**JsonFormat** 类型的输出数据集定义如下（部分定义，仅包含相关部件）。 更具体说来，`structure` 节用于定义目标文件中的自定义属性名称，`nestingSeparator`（默认为“.”）则用于标识名称中的嵌套层。 本节为**可选**，除非你需要根据源列名更改属性名称，或者需要嵌套部分属性。

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

### <a name="specifying-avroformat"></a>指定 AvroFormat
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

### <a name="specifying-orcformat"></a>指定 OrcFormat
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

注意以下几点：

* 不支持复杂数据类型（STRUCT、MAP、LIST、UNION）
* ORC 文件有三个[压缩相关的选项](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/)：NONE、ZLIB、SNAPPY。 数据工厂支持从使用其中任一压缩格式的 ORC 文件中读取数据。 它使用元数据中的压缩编解码器来读取数据。 但是，写入 ORC 文件时，数据工厂会选择 ZLIB，这是 ORC 的默认选项。 目前没有任何选项可以重写此行为。

### <a name="specifying-parquetformat"></a>指定 ParquetFormat
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

注意以下几点：

* 不支持复杂数据类型（MAP、LIST）
* Parquet 文件提供以下压缩相关的选项：NONE、SNAPPY、GZIP 和 LZO。 数据工厂支持从使用其中任一压缩格式的 ORC 文件中读取数据。 它使用元数据中的压缩编解码器来读取数据。 但是，写入 Parquet 文件时，数据工厂会选择 SNAPPY，这是 Parquet 格式的默认选项。 目前没有任何选项可以重写此行为。
