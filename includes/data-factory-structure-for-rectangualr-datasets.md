## <a name="specifying-structure-definition-for-rectangular-datasets"></a>指定矩形数据集的结构定义
数据集 JSON 中的结构部分是矩形表（包括行与列）的**可选**部分，其中包含该表的列集合。 结构部分用于提供类型转换的类型信息或执行列映射。 以下部分更详细说明了这些功能。 

每个列包含以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| 名称 |列的名称。 |是 |
| type |列的数据类型。 有关何时应指定类型信息，请参阅下面的类型转换部分 |否 |
| culture |指定类型时要使用的基于 .NET 的区域性，是 .NET 类型的 Datetime 或 Datetimeoffset。 默认值为“en-us”。 |否 |
| 格式 |指定类型时要使用的格式字符串，是 .NET 类型的 Datetime 或 Datetimeoffset。 |否 |

以下示例演示包含三个列（userid、name、lastlogindate）的表的结构部分 JSON。

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

有关何时要包括“结构”信息以及在**结构**部分中要包含哪些信息，请遵循以下指导原则。

* 对于存储数据架构、类型信息以及数据本身的**结构化数据源**（如 SQL Server、Oracle、Azure 表等源），仅当想要执行列映射，以便将特定源列映射到接收器中的特定列且其名称不相同时，才应该指定“结构”部分（请参阅下面列映射部分中的详细信息）。 
  
    如上所述，“结构”部分中的类型信息是可选的。 对于结构化源，类型信息已可用作数据存储中的数据集定义的一部分，因此包含“结构”部分时不应包含类型信息。
* **对于读取的数据源（尤其是 Azure Blob）的架构**，可以选择存储数据但不存储任何架构或类型信息。 对于这些类型的数据源，应在以下 2 种情况下包含“结构”：
  * 想要执行列映射。
  * 当数据集是“复制”活动中的源时，可以在“结构”中提供类型信息，数据工厂将使用此类型信息转换为接收器的本机类型。 有关详细信息，请参阅 [Move data to and from Azure Blob](../articles/data-factory/v1/data-factory-azure-blob-connector.md)（将数据移入和移出 Azure Blob）一文。

### <a name="supported-net-based-types"></a>支持的基于 .NET 的类型
数据工厂支持使用以下符合 CLS 标准的基于 .NET 的类型在“结构”中为读取的数据源（如 Azure Blob）中的架构提供类型信息。

* Int16
* Int32 
* Int64
* Single
* Double
* 小数
* Byte[]
* Bool
* 字符串 
* Guid
* Datetime
* Datetimeoffset
* Timespan 

对于 Datetime 和 Datetimeoffset，可以选择性地指定“culture”和“format”字符串来帮助分析自定义的 Datetime 字符串。 请参阅下面的类型转换示例。

