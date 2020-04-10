---
title: Azure 数据工厂中的 JSON 格式
description: 本主题介绍了如何处理 Azure 数据工厂中的 JSON 格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: jingwang
ms.openlocfilehash: b63dcfd6ed293cab9d0107a8b6a35c7ce358d429
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011678"
---
# <a name="json-format-in-azure-data-factory"></a>Azure 数据工厂中的 JSON 格式

如果要**分析 JSON 文件或以 JSON 格式写入数据**，请遵循本文中的说明。 

JSON格式支持以下连接器：[亚马逊S3，Azure](connector-amazon-simple-storage-service.md) [Blob，Azure数据存储第1代](connector-azure-data-lake-store.md)[，Azure数据存储第2代](connector-azure-data-lake-storage.md)[，Azure文件存储](connector-azure-file-storage.md)，[文件系统](connector-file-system.md)[，FTP，](connector-ftp.md)[谷歌云存储](connector-google-cloud-storage.md)[，HDFS，HTTP](connector-hdfs.md)和[SFTP。](connector-sftp.md) [Azure Blob](connector-azure-blob-storage.md) [HTTP](connector-http.md)

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 JSON 数据集支持的属性列表。

| Property         | 说明                                                  | 必选 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 数据集的 type 属性必须设置为 **Json**。 | 是      |
| location         | 文件的位置设置。 每个基于文件的连接器在 `location` 下都有其自己的位置类型和支持的属性。 **请在连接器文章 -> 数据集属性部分中查看详细信息**。 | 是      |
| encodingName     | 用于读取/写入测试文件的编码类型。 <br>Allowed values are as follows: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13"，"ISO-8859-15"，"WINDOWS-874"，"WINDOWS-1250"，"WINDOWS-1251"，"WINDOWS-1252"，"视窗-1253"，"视窗-1254"，"视窗-1255"，"视窗-1256"，"视窗-1257"，"视窗-1258"。| 否       |
| compression | 用来配置文件压缩的属性组。 如果需要在活动执行期间进行压缩/解压缩，请配置此部分。 | 否 |
| type | 用于读取/写入 JSON 文件的压缩编解码器。 <br>允许的值为 **bzip2**、**gzip**、**deflate**、**ZipDeflate**、**snappy** 或 **lz4**。 保存文件时使用。 默认值不会压缩。<br>**注意**当前复制活动不支持"快速"&"lz4"，并且映射数据流不支持"ZipDeflate"。<br>**注意**当使用复制活动来解压缩 ZipDeflate 文件并写入基于文件的接收器数据存储时，文件将提取到文件夹： `<path specified in dataset>/<folder named as source zip file>/`。 | 不是。  |
| 级别 | 压缩率。 <br>允许的值为 **Optimal** 或 **Fastest**。<br>- **最快：** 压缩操作应尽快完成，即使生成的文件未以最佳方式压缩也是如此。<br>- **最佳**： 压缩操作应以最佳方式压缩，即使操作需要更长的时间才能完成。 有关详细信息，请参阅 [Compression Level](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx)（压缩级别）主题。 | 否       |

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

### <a name="json-as-source"></a>以 JSON 作为源

复制活动***\*源\**** 部分支持以下属性。

| Property      | 说明                                                  | 必选 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为 **JSONSource**。 | 是      |
| storeSettings | 有关如何从数据存储读取数据的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自己支持的读取设置。 **请在连接器文章 -> 复制活动属性部分中查看详细信息**。 | 否       |

### <a name="json-as-sink"></a>JSON 作为接收器

复制活动***\*接收器\**** 部分支持以下属性。

| Property      | 说明                                                  | 必选 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为 **JSONSink**。 | 是      |
| formatSettings | 一组属性。 请参阅下面的“JSON 写入设置”表。**** | 否       |
| storeSettings | 有关如何将数据写入到数据存储的一组属性。 每个基于文件的连接器在 `storeSettings` 下都有其自身支持的写入设置。 **请在连接器文章 -> 复制活动属性部分中查看详细信息**。 | 否       |

`formatSettings` 下支持的 **JSON 写入设置**：

| Property      | 说明                                                  | 必选                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | 格式类型设置必须设置为**JsonWriteSettings**。 | 是                                                   |
| filePattern |指示每个 JSON 文件中存储的数据模式。 允许的值为：**setOfObjects** 和 **arrayOfObjects**。 **默认**值为 **setOfObjects**。 请参阅 [JSON 文件模式](#json-file-patterns)部分，详细了解这些模式。 |否 |

### <a name="json-file-patterns"></a>JSON 文件模式

Copy 活动可自动检测并分析以下 JSON 文件模式。 

- **类型 I：setOfObjects**

    每个文件包含单个对象，或者以行分隔/串连的多个对象。 
    在复制活动接收器中选择此选项时，复制活动将生成单个 JSON 文件，其中每行包含一个对象（以行分隔）。

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

## <a name="mapping-data-flow-properties"></a>映射数据流属性

JSON 文件类型可用作映射数据流的接收器和源。

### <a name="creating-json-structures-in-a-derived-column"></a>在派生列中创建 JSON 结构

您可以通过派生的列表达式生成器向数据流添加复杂列。 在派生列转换中，添加新列，并通过单击蓝色框打开表达式生成器。 要使列变得复杂，可以手动输入 JSON 结构或使用 UX 以交互方式添加子列。

#### <a name="using-the-expression-builder-ux"></a>使用表达式生成器 UX

在输出架构侧窗格中，将鼠标悬停在列上，然后单击加号图标。 选择 **"添加子列**"以使该列成为复杂类型。

![添加子列](media/data-flow/addsubcolumn.png "添加子列")

可以以同样的方式添加其他列和子列。 对于每个非复杂字段，可以在右侧的表达式编辑器中添加表达式。

![复杂列](media/data-flow/complexcolumn.png "复杂列")

#### <a name="entering-the-json-structure-manually"></a>手动输入 JSON 结构

要手动添加 JSON 结构，请添加新列并在编辑器中输入表达式。 表达式遵循以下常规格式：

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

如果为名为"复杂列"的列输入此表达式，则它将以以下 JSON 一词写入接收器：

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

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>完整的分层定义示例手动脚本
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

### <a name="source-format-options"></a>源格式选项

使用 JSON 数据集作为数据流中的源，可以设置五个附加设置。 可在 **"源选项**"选项卡中的 **"JSON 设置**手风琴"下找到这些设置。  

![JSON 设置](media/data-flow/json-settings.png "JSON 设置")

#### <a name="default"></a>默认

默认情况下，JSON 数据以以下格式读取。

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>单一文档

如果选择了**单个文档**，则映射数据流将读取每个文件中的一个 JSON 文档。 

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

#### <a name="unquoted-column-names"></a>未引用的列名称

如果选择了**未引用的列名称**，则映射数据流将读取未由引号包围的 JSON 列。 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>有评论

如果 JSON 数据具有 C 或C++样式注释，请选择 **"有注释**"。

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>单次报价

如果 JSON 字段和值使用单引号而不是双引号，请选择 **"单引号**"。

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>反斜杠逃跑

如果背斜杠用于转义 JSON 数据中的字符，请选择 **"单引号**"。

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="next-steps"></a>后续步骤

- [复制活动概述](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [查找活动](control-flow-lookup-activity.md)
- [获取元数据活动](control-flow-get-metadata-activity.md)
