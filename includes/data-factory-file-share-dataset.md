## <a name="fileshare-dataset-type-properties"></a>FileShare 数据集类型属性
有关可用于定义数据集的节和属性的完整列表，请参阅 [Creating datasets](../articles/data-factory/data-factory-create-datasets.md)（创建数据集）一文。 数据集 JSON 的结构、可用性和策略等节类似于所有数据集类型。

每个数据集类型的 **typeProperties** 节都不同。 它提供特定于数据集类型的信息。 **FileShare** 数据集类型的 typeProperties 节具有以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| folderPath |文件夹的子路径。 请对字符串中的特殊字符使用转义符“\”。 有关示例，请参阅 [Sample linked service and dataset definitions](#sample-linked-service-and-dataset-definitions)（链接服务和数据集定义示例）。<br/><br/>可将此属性与 **partitionBy** 相组合，基于切片开始/结束日期时间构成文件夹路径。 |是 |
| fileName |如果希望表引用文件夹中的特定文件，请在 **folderPath** 中指定文件名。 如果没有为此属性指定任何值，表将指向文件夹中的所有文件。<br/><br/>如果没有为输出数据集指定 fileName，生成的文件的名称将采用以下格式： <br/><br/>Data.<Guid>.txt（示例：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt） |否 |
| fileFilter |指定在 folderPath 中选择一部分文件而不是所有文件时要使用的筛选器。<br/><br/>允许的值为：`*`（多个字符）和 `?`（单个字符）。<br/><br/>示例 1：`"fileFilter": "*.log"`<br/>示例 2：`"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter 适用于 FileShare 输入数据集。 HDFS 不支持此属性。 |否 |
| partitionedBy |partitionedBy 可用于指定时序数据的动态 folderPath 和 filename。 例如，folderPath 可针对每小时的数据参数化。 |否 |
| 格式 | 支持以下格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 请将格式中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](#specifying-textformat)、[Json 格式](#specifying-jsonformat)、[Avro 格式](#specifying-avroformat)、[Orc 格式](#specifying-orcformat)和 [Parquet 格式](#specifying-parquetformat)部分。 <br><br> 如果想要在基于文件的存储之间**按原样复制文件**（二进制副本），可以在输入和输出数据集定义中跳过格式节。 |否 |
| compression | 指定数据的压缩类型和级别。 支持的类型为：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**；支持的级别为：**Optimal** 和 **Fastest**。 有关详细信息，请参阅[指定压缩](#specifying-compression)部分。 |否 |
| useBinaryTransfer |指定是否使用二进制传输模式。 使用二进制模式时为 true，使用 ASCII 时为 false。 默认值：True。 仅当关联的链接服务类型为 FtpServer 时，才可以使用此属性。 |否 |

> [!NOTE]
> filename 和 fileFilter 不能同时使用。
>
>

### <a name="using-partionedby-property"></a>使用 partionedBy 属性
如上一部分所述，可以使用 partitionedBy 指计时序数据的动态 folderPath 和 filename。 为此，可以使用数据工厂宏和系统变量 SliceStart 与 SliceEnd（表示给定数据切片的逻辑时间段）。

若要了解有关时序数据集、计划和切片的信息，请参阅文章 [Creating Datasets](../articles/data-factory/data-factory-create-datasets.md)（创建数据集）、[Scheduling & Execution](../articles/data-factory/data-factory-scheduling-and-execution.md)（计划和执行）及 [Creating Pipelines](../articles/data-factory/data-factory-create-pipelines.md)（创建管道）。

#### <a name="sample-1"></a>示例 1：

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
在本示例中，{Slice} 已替换为数据工厂系统变量 SliceStart 的值（使用指定的格式 (YYYYMMDDHH)）。 SliceStart 指切片开始时间。 每个切片的 folderPath 不同。 示例：wikidatagateway/wikisampledataout/2014100103 或 wikidatagateway/wikisampledataout/2014100104。

#### <a name="sample-2"></a>示例 2：

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
在本示例中，SliceStart 的年、月、日和时间已提取到 folderPath 和 fileName 属性使用的各个变量。
