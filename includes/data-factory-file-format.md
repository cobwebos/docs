## <a name="specifying-formats"></a>指定格式
Azure 数据工厂支持以下格式类型：

* [文本格式](#specifying-textformat)
* [JSON 格式](#specifying-jsonformat)
* [Avro 格式](#specifying-avroformat)
* [ORC 格式](#specifying-orcformat)
* [Parquet 格式](#specifying-parquetformat)

### <a name="specifying-textformat"></a>指定 TextFormat
如果格式设置为 **TextFormat**，可以在 **Format** 节中指定以下**可选**属性。

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| columnDelimiter |用于分隔文件中的列的字符。 |只允许一个字符。 默认值为逗号（“,”）。 <br/><br/>若要使用 Unicode 字符，请参阅 [Unicode 字符](https://en.wikipedia.org/wiki/List_of_Unicode_characters)获取相应的代码。 例如，可以考虑使用数据中不太可能存在的极少见的不可打印字符：指定“\u0001”表示标题开头 (SOH)。 |否 |
| rowDelimiter |用于分隔文件中的行的字符。 |只允许一个字符。 默认值为以下任何一项：[“\r\n”、“\r”、“\n”]（读取时）和“\r\n”（写入时）。 |否 |
| escapeChar |用于转义输入文件内容中的列分隔符的特殊字符。 <br/><br/>不能同时为表指定 escapeChar 和 quoteChar。 |只允许一个字符。 没有默认值。 <br/><br/>示例：如果以逗号（“,”）作为列分隔符，但想要在文本中使用逗号字符（例如：“Hello, world”），可以将“$”定义为转义符，在源中使用字符串“Hello$, world”。 |否 |
| quoteChar |括住字符串值的引号字符。 引号字符内的列和行分隔符被视为字符串值的一部分。 此属性同时适用于输入和输出数据集。<br/><br/>不能同时为表指定 escapeChar 和 quoteChar。 |只允许一个字符。 没有默认值。 <br/><br/>例如，如果以逗号（“,”）作为列分隔符，但想要在文本中使用逗号字符（例如：<Hello, world>），可以将 "（双引号）定义为引号字符，在源中使用字符串“Hello, world”。 |否 |
| nullValue |用于表示 null 值的一个或多个字符。 |一个或多个字符。 默认值为“\N”和“NULL”（读取时）及“\N”（写入时）。 |否 |
| encodingName |指定编码名称。 |有效的编码名称。 请参阅 [Encoding.EncodingName 属性](https://msdn.microsoft.com/library/system.text.encoding.aspx)。 例如：windows-1250 或 shift_jis。 默认值为 UTF-8。 |否 |
| firstRowAsHeader |指定是否将第一行视为标头。 对于输入数据集，数据工厂将读取第一行作为标头。 对于输出数据集，数据工厂将写入第一行作为标头。 <br/><br/>有关示例方案，请参阅 [**firstRowAsHeader** 和 **skipLineCount** 的使用方案](#scenarios-for-using-firstrowasheader-and-skiplinecount)。 |True<br/>False（默认值） |否 |
| skipLineCount |指示从输入文件读取数据时要跳过的行数。 如果同时指定了 skipLineCount 和 firstRowAsHeader，则先跳过代码行，然后从输入文件读取标头信息。 <br/><br/>有关示例方案，请参阅 [firstRowAsHeader 和 skipLineCount 的使用方案](#scenarios-for-using-firstrowasheader-and-skiplinecount)。 |整数 |否 |
| treatEmptyAsNull |指定从输入文件读取数据时，是否将 null 或空字符串视为 null 值。 |True（默认值）<br/>False |否 |

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

若要使用 escapeChar 而不是 quoteChar，请将包含 quoteChar 的行替换为以下 escapeChar：

```json
"escapeChar": "$",
```


### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>firstRowAsHeader 和 skipLineCount 的使用方案
* 你要从非文件源复制到文本文件，并想要添加包含架构元数据（例如 SQL 架构）的标头行。 对于此方案，请在输出数据集中将 **firstRowAsHeader** 指定为 true。
* 你要从包含标头行的文本文件复制到非文件接收器，并想要删除该行。 请在输入数据集中将 **firstRowAsHeader** 指定为 true。
* 你要从文本文件复制，并想跳过不包含数据或标头信息的开头几行。 指定 **skipLineCount** 来指明要跳过的行数。 如果文件的剩余部分包含标头行，则也可以指定 **firstRowAsHeader**。 如果同时指定了 **skipLineCount** 和 **firstRowAsHeader**，则先跳过代码行，然后从输入文件读取标头信息。

### <a name="specifying-jsonformat"></a>指定 JsonFormat
若要在 DocumentDB 中按原样导入/导出 JSON 文件，请参阅 DocumentDB 连接器中的 [Import/export JSON documents](../articles/data-factory/data-factory-azure-documentdb-connector.md#importexport-json-documents)（导入/导出 JSON 文档）部分了解详细信息。

如果格式设置为 **JsonFormat**，可以在 **Format** 节中指定以下**可选**属性。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| filePattern |指示每个 JSON 文件中存储的数据模式。 允许的值为：**setOfObjects** 和 **arrayOfObjects**。 **默认**值为：**setOfObjects**。 有关这些模式的详细信息，请参阅以下部分。 |否 |
| encodingName |指定编码名称。 有关有效编码名称的列表，请参阅：[Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) 属性。 例如：windows-1250 或 shift_jis。 **默认**值为 **UTF-8**。 |否 |
| nestingSeparator |用于分隔嵌套级别的字符。 默认值为“.”（点）。 |否 |

#### <a name="setofobjects-file-pattern"></a>setOfObjects 文件模式
每个文件包含单个对象，或者以行分隔/串连的多个对象。 在输出数据集中选择此选项时，复制活动将生成单个 JSON 文件，其中每行包含一个对象（以行分隔）。

**单个对象**

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

**行分隔的 JSON**

```json
    {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
    {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
    {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"789037573","callingnum2":"789044691","switch1":"UK","switch2":"Australia"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789044691","switch1":"US","switch2":"Australia"}
```

**串连的 JSON**

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

#### <a name="arrayofobjects-file-pattern"></a>arrayOfObjects 文件模式。
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
    },
    {
        "time": "2015-04-29T07:13:22.0960000Z",
        "callingimsi": "466922202613463",
        "callingnum1": "789037573",
        "callingnum2": "789044691",
        "switch1": "UK",
        "switch2": "Australia"
    },
    {
        "time": "2015-04-29T07:13:22.0960000Z",
        "callingimsi": "466922202613463",
        "callingnum1": "123436380",
        "callingnum2": "789044691",
        "switch1": "US",
        "switch2": "Australia"
    },
    {
        "time": "2015-04-29T07:13:24.2120000Z",
        "callingimsi": "466922201102759",
        "callingnum1": "345698602",
        "callingnum2": "789097900",
        "switch1": "UK",
        "switch2": "Australia"
    },
    {
        "time": "2015-04-29T07:13:25.6320000Z",
        "callingimsi": "466923300236137",
        "callingnum1": "567850552",
        "callingnum2": "789086133",
        "switch1": "China",
        "switch2": "Germany"
    }
]
```
### <a name="jsonformat-example"></a>JsonFormat 示例
如果 JSON 文件包含以下内容：  

```json
{
    "Id": 1,
    "Name": {
        "First": "John",
        "Last": "Doe"
    },
    "Tags": ["Data Factory”, "Azure"]
}
```
你想要使用以下格式将该文件复制到 Azure SQL 表中：

| ID | Name.First | Name.Middle | Name.Last | 标记 |
| --- | --- | --- | --- | --- |
| 1 |John |Null |Doe |["Data Factory”, "Azure"] |

JsonFormat 类型的输入数据集定义如下：（仅包含相关部件的部分定义）

```json
"properties": {
    "structure": [
        {"name": "Id", "type": "Int"},
        {"name": "Name.First", "type": "String"},
        {"name": "Name.Middle", "type": "String"},
        {"name": "Name.Last", "type": "String"},
        {"name": "Tags", "type": "string"}
    ],
    "typeProperties":
    {
        "folderPath": "mycontainer/myfolder",
        "format":
        {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "encodingName": "UTF-8",
            "nestingSeparator": "."
        }
    }
}
```
如果未定义结构，则默认情况下，复制活动将平整结构并复制所有内容。

#### <a name="supported-json-structure"></a>支持的 JSON 结构
注意以下几点：

* 包含名称/值对集合的每个对象将以表格格式映射到一个数据行。 对象可以嵌套。可以定义默认情况下如何在数据集中使用嵌套分隔符 (.) 来平整结构。 有关示例，请参阅前面的 [JsonFormat 示例](#jsonformat-example)部分。  
* 如果未在数据工厂数据集中定义结构，复制活动将检测第一个对象的架构，并平整整个对象。
* 如果 JSON 输入包含数组，复制活动会将整个数组值转换为字符串。 可以选择使用[列映射或筛选](#column-mapping-with-translator-rules)跳过此操作。
* 如果同一级别存在重复的名称，复制活动将选择最后一个名称。
* 属性名称区分大小写。 名称相同但大小写不同的两个属性被视为两个不同的属性。

### <a name="specifying-avroformat"></a>指定 AvroFormat
如果格式设置为 AvroFormat，不需要在 typeProperties 节的 Format 节中指定任何属性。 示例：

```json
"format":
{
    "type": "AvroFormat",
}
```

若要在 Hive 表中使用 Avro 格式，可以参考 [Apache Hive 教程](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe)。

注意以下几点：  

* 不支持[复杂数据类型](http://avro.apache.org/docs/current/spec.html#schema_complex)（记录、枚举、数组、映射、联合与固定值）

### <a name="specifying-orcformat"></a>指定 OrcFormat
如果格式设置为 OrcFormat，不需要在 typeProperties 节的 Format 节中指定任何属性。 示例：

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
如果格式设置为 ParquetFormat，不需要在 typeProperties 节的 Format 节中指定任何属性。 示例：

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


<!--HONumber=Jan17_HO3-->


