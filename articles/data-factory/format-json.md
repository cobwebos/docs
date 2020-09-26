---
title: Azure 数据工厂中的 JSON 格式
description: 本主题介绍了如何处理 Azure 数据工厂中的 JSON 格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jingwang
ms.openlocfilehash: 0b80c2f125e7eddaa427b75e3e0fe2aff6c679be
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334321"
---
# <a name="json-format-in-azure-data-factory"></a>Azure 数据工厂中的 JSON 格式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

如果要**分析 JSON 文件或以 JSON 格式写入数据**，请遵循本文中的说明。 

以下连接器支持 JSON 格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [azure 文件存储](connector-azure-file-storage.md)、 [文件系统](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和 [SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 JSON 数据集支持的属性列表。

| 属性         | 说明                                                  | 必须 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 数据集的 type 属性必须设置为 **Json**。 | 是      |
| location         | 文件的位置设置。 每个基于文件的连接器在 `location` 下都有其自己的位置类型和支持的属性。 **请在连接器文章 -> 数据集属性部分中查看详细信息**。 | 是      |
| encodingName     | 用于读取/写入测试文件的编码类型。 <br>可用的值如下："UTF-8"、"UTF-16"、"UTF-16BE"、"UTF-32"、"UTF-32BE"、"US-ASCII"、"UTF-7"、"BIG5"、"EUC-JP"、"EUC-KR"、"GB2312"、"GB18030"、"JOHAB"、"SHIFT-JIS"、"CP875"、"CP866"、"IBM00858"、"IBM037"、"IBM273"、"IBM437"、"IBM500"、"IBM737"、"IBM775"、"IBM850"、"IBM852"、"IBM855"、"IBM857"、"IBM860"、"IBM861"、"IBM863"、"IBM864"、"IBM865"、"IBM869"、"IBM870"、"IBM01140"、"IBM01141"、"IBM01142"、"IBM01143"、"IBM01144"、"IBM01145"、"IBM01146"、"IBM01147"、"IBM01148"、"IBM01149"、"ISO-2022-JP"、"ISO-2022-KR"、"ISO-8859-1"、"ISO-8859-2"、"ISO-8859-3"、"ISO-8859-4"、"ISO-8859-5"、"ISO-8859-6"、"ISO-8859-7"、"ISO-8859-8"、"ISO-8859-9"、"ISO-8859-13"、"ISO-8859-15"、"WINDOWS-874"、"WINDOWS-1250"、"WINDOWS-1251"、"WINDOWS-1252"、"WINDOWS-1253"、"WINDOWS-1254"、"WINDOWS-1255"、"WINDOWS-1256"、"WINDOWS-1257"、"WINDOWS-1258"。| 否       |
| compression | 用来配置文件压缩的属性组。 如果需要在活动执行期间进行压缩/解压缩，请配置此部分。 | 否 |
| type<br/>（在 `compression` 下） | 用来读取/写入 JSON 文件的压缩编解码器。 <br>允许的值为 **bzip2**、 **gzip**、 **deflate**、 **ZipDeflate**、 **TarGzip**、 **snappy**或 **lz4**。 默认设置是不压缩。<br>请注意  ，复制活动目前不支持“snappy”和“lz4”，映射数据流不支持“ZipDeflate”。<br>**请注意**，在使用复制活动将**ZipDeflate** / **TarGzip**文件解压缩 (s) 并写入基于文件的接收器数据存储时，默认情况下，将文件提取到该文件夹： `<path specified in dataset>/<folder named as source compressed file>/` ，使用 `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` [复制活动源](#json-as-source)控制是否保留压缩文件的名称， (s) 为文件夹结构。| 否。  |
| level<br/>（在 `compression` 下） | 压缩率。 <br>允许的值为 **Optimal** 或 **Fastest**。<br>- **Fastest**：尽快完成压缩操作，不过，无法以最佳方式压缩生成的文件。<br>- **Optimal**：以最佳方式完成压缩操作，不过，需要耗费更长的时间。 有关详细信息，请参阅 [Compression Level](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx)（压缩级别）主题。 | 否       |

下面是 Azure Blob 存储上的 JSON 数据集的示例：

```json
{
    "name": "JSONDataset",
    "properties": {
        "type": "Json",
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
            "compression": {
                "type": "gzip"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 JSON 源和接收器支持的属性列表。

若要了解如何从 JSON 文件中提取数据并将其映射到接收器数据存储/格式（反之亦然），请参阅[架构映射](copy-activity-schema-and-type-mapping.md)。

### <a name="json-as-source"></a>以 JSON 作为源

复制活动的 ***\*source\**** 节支持以下属性。

| 属性      | 说明                                                  | 必需 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为 **JSONSource**。 | 是      |
| formatSettings | 一组属性。 请参阅下面的“JSON 读取设置”表。 | 否       |
| storeSettings | 有关如何从数据存储读取数据的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自己支持的读取设置。 **请在连接器文章 -> 复制活动属性部分中查看详细信息**。 | 否       |

`formatSettings` 下支持的“JSON 读取设置”：

| 属性      | 说明                                                  | 必需 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | formatSettings 的 type 必须设置为“JsonReadSettings”。 | 是      |
| compressionProperties | 一组属性，指示如何为给定的压缩编解码器解压缩数据。 | 否       |
| preserveZipFileNameAsFolder<br> (*下 `compressionProperties` -> `type`) `ZipDeflateReadSettings` *  | 当输入数据集配置了 ZipDeflate 压缩时适用。 指示是否在复制过程中以文件夹结构形式保留源 zip 文件名。<br>- 当设置为 true（默认值）时，数据工厂会将已解压缩的文件写入 `<path specified in dataset>/<folder named as source zip file>/`。<br>- 当设置为 false 时，数据工厂会直接将未解压缩的文件写入 `<path specified in dataset>`。 请确保在不同的源 zip 文件中没有重复的文件名，以免出现赛车或意外的行为。  | 否 |
| preserveCompressionFileNameAsFolder<br> (*下 `compressionProperties` -> `type`) `TarGZipReadSettings` * | 当输入数据集配置了 **TarGzip** 压缩时适用。 指示是否在复制过程中将源压缩文件名保留为文件夹结构。<br>-如果设置为 **true (默认) **，数据工厂会将解压缩的文件写入 `<path specified in dataset>/<folder named as source compressed file>/` 。 <br>-当设置为 **false**时，数据工厂会将解压缩的文件直接写入到 `<path specified in dataset>` 中。 请确保在不同的源文件中没有重复的文件名，以免产生赛车或意外的行为。 | 否 |

### <a name="json-as-sink"></a>JSON 作为接收器

复制活动的 ***\*sink\**** 节支持以下属性。

| 属性      | 说明                                                  | 必需 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为 **JSONSink**。 | 是      |
| formatSettings | 一组属性。 请参阅下面的“JSON 写入设置”表。 | 否       |
| storeSettings | 有关如何将数据写入到数据存储的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自身支持的写入设置。 **请在连接器文章 -> 复制活动属性部分中查看详细信息**。 | 否       |

`formatSettings` 下支持的 **JSON 写入设置**：

| 属性      | 说明                                                  | 必需                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | formatSettings 的类型必须设置为 **JsonWriteSettings**。 | 是                                                   |
| filePattern |指示每个 JSON 文件中存储的数据模式。 允许的值为：setOfObjects (JSON Lines) 和 arrayOfObjects。 **默认**值为 **setOfObjects**。 请参阅 [JSON 文件模式](#json-file-patterns)部分，详细了解这些模式。 |否 |

### <a name="json-file-patterns"></a>JSON 文件模式

从 JSON 文件复制数据时，复制活动可自动检测并分析以下 JSON 文件模式。 将数据写入 JSON 文件时，可以在复制活动接收器上配置文件模式。

- **类型 I：setOfObjects**

    每个文件都包含单一对象、JSON Lines 或串联的对象。

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

    * **JSON Lines（接收器的默认值）**

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

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流时，可以在以下数据存储中读取和写入 JSON 格式： [Azure Blob 存储](connector-azure-blob-storage.md#mapping-data-flow-properties)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)和 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。

### <a name="source-properties"></a>源属性

下表列出了 json 源支持的属性。 可以在 " **源选项** " 选项卡中编辑这些属性。

| 名称 | 说明 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 通配符路径 | 将处理所有匹配通配符路径的文件。 重写在数据集中设置的文件夹和文件路径。 | 否 | string[] | wildcardPaths |
| 分区根路径 | 对于已分区的文件数据，可以输入分区根路径以便将分区文件夹读取为列 | 否 | String | partitionRootPath |
| 文件列表 | 你的源是否指向列出要处理的文件的文本文件 | 否 | `true` 或 `false` | fileList |
| 要存储文件名的列 | 使用源文件名称和路径创建新列 | 否 | String | rowUrlColumn |
| 完成后 | 在处理后删除或移动文件。 文件路径从容器根开始 | 否 | 删除： `true` 或 `false` <br> 移动 `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| 按上次修改时间筛选 | 选择根据文件上次更改时间筛选文件 | 否 | 时间戳 | ModifiedAfter <br> modifiedBefore |
| 单个文档 | 映射数据流从每个文件读取一个 JSON 文档 | 否 | `true` 或 `false` | singleDocument |
| 不带引号的列名 | 如果选择了不带 **引号的列名称** ，则映射数据流将读取未括在引号中的 JSON 列。 | 否 | `true` 或 `false` |  unquotedColumnNames |
| 包含注释 | 如果 JSON 数据具有 C 或 c + + 样式注释，请选择 " **具有注释** " | 否 | `true` 或 `false` | asComments |
| 单引号 | 读取未括在引号中的 JSON 列 | 否 | `true` 或 `false` | singleQuoted |
| 反斜杠转义 | 如果使用反斜杠对 JSON 数据中的字符进行转义，请选择**反斜杠转义** | 否 | `true` 或 `false` | backslashEscape |
| 允许找不到文件 | 如果为 true，则在找不到文件时不会引发错误 | 否 | `true` 或 `false` | ignoreNoFilesFound |

### <a name="source-format-options"></a>源格式选项

使用 JSON 数据集作为数据流中的源，可设置五个附加设置。 这些设置可在 "**源选项**" 选项卡中的**JSON 设置**可折叠项下找到。  

![JSON 设置](media/data-flow/json-settings.png "JSON 设置")

#### <a name="default"></a>默认

默认情况下，使用以下格式读取 JSON 数据。

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>单个文档

如果选择了 " **单个文档** "，则映射数据流会从每个文件读取一个 JSON 文档。 

``` json
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```
> [!NOTE]
> 如果在预览 JSON 数据时数据流引发了 "corrupt_record" 错误，则可能是因为数据包含的是 JSON 文件中的单个文档。 设置 "单个文档" 应清除该错误。

#### <a name="unquoted-column-names"></a>不带引号的列名

如果选择了不带 **引号的列名称** ，则映射数据流将读取未括在引号中的 JSON 列。 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>包含注释

如果 JSON 数据具有 C 或 c + + 样式注释，请选择 " **具有注释** "。

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>单引号

如果 JSON 字段和值使用单引号而不是双引号，则选择 " **单引号** "。

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>反斜杠转义

如果使用反斜杠对 JSON 数据中的字符进行转义，请选择 **反斜杠转义** 。

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

### <a name="sink-properties"></a>接收器属性

下表列出了 json 接收器支持的属性。 可以在 " **设置** " 选项卡中编辑这些属性。

| 名称 | 说明 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 清除文件夹 | 如果在写入前清除目标文件夹 | 否 | `true` 或 `false` | truncate |
| 文件名选项 | 写入的数据的命名格式。 默认情况下，每个分区的一个文件的格式为 `part-#####-tid-<guid>` | 否 | 模式：字符串 <br> 每个分区： String [] <br> As 列中的数据：字符串 <br> 输出到单个文件： `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

### <a name="creating-json-structures-in-a-derived-column"></a>在派生列中创建 JSON 结构

您可以通过派生列表达式生成器将复杂列添加到数据流。 在派生列转换中，通过单击蓝色框来添加一个新列并打开 "表达式生成器"。 若要使列复杂化，可以手动输入 JSON 结构，或使用 UX 以交互方式添加个子列。

#### <a name="using-the-expression-builder-ux"></a>使用表达式生成器 UX

在 "输出架构" 侧窗格中，将鼠标悬停在某一列上，并单击加号图标。 选择 " **添加 subcolumn** "，使列成为复杂类型。

![添加子列](media/data-flow/derive-add-subcolumn.png "添加子列")

您可以通过相同的方式添加更多的列和个子列。 对于每个非复杂字段，可以在右侧的表达式编辑器中添加表达式。

![添加复杂列](media/data-flow/derive-complex-column.png "添加列")

#### <a name="entering-the-json-structure-manually"></a>手动输入 JSON 结构

若要手动添加 JSON 结构，请添加一个新列，然后在编辑器中输入该表达式。 表达式采用以下常规格式：

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

如果为名为 "complexColumn" 的列输入了此表达式，则会将其作为以下 JSON 写入接收器：

```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>完整层次结构定义的示例手动脚本
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="next-steps"></a>后续步骤

- [复制活动概述](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
