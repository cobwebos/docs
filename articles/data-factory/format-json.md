---
title: Azure 数据工厂中的 JSON 格式
description: 本主题介绍如何在 Azure 数据工厂中处理 JSON 格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: jingwang
ms.openlocfilehash: 7dac8d21e3b45307284ece15ca5ddbcc69db909b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260639"
---
# <a name="json-format-in-azure-data-factory"></a>Azure 数据工厂中的 JSON 格式

如果要**分析 json 文件或将数据写入 json 格式**，请遵循此文。 

以下连接器支持 JSON 格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [azure 文件存储](connector-azure-file-storage.md)、[文件系统](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和[SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 JSON 数据集支持的属性列表。

| properties         | 说明                                                  | 必选 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 数据集的 type 属性必须设置为**Json**。 | 是      |
| location         | 文件的位置设置。 每个基于文件的连接器都具有其自己的位置类型和 `location`下支持的属性。 **请参阅连接器文章-> 数据集属性 "部分中的详细信息**。 | 是      |
| encodingName     | 用于读取/写入测试文件的编码类型。 <br>允许的值如下： "UTF-8"、"UTF-16"、"UTF-16BE"、"UTF-32"、"32BE"、"US-ASCII"、"UTF-7"、"BIG5"、"EUC-JP"、"EUC-KR"、"GB2312"、"GB18030"、"JOHAB"、"SHIFT-JIS"、"CP875"、"CP866"、"IBM00858"、"IBM037"、"IBM273"、"IBM437"、"IBM500"、""、""、"IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149"、"ISO-2022-JP"、"ISO-2022-KR"、"ISO-8859-1"、"ISO-8859-2"、"ISO-8859-3"、"ISO-8859-4"、"ISO-8859-5"、"ISO-8859-6"、"ISO-8859-7"、"ISO-8859-8"、"ISO-8859-9"、"ISO-8859-13"、"ISO-8859-15"、"WINDOWS-874"、"WINDOWS-1250"、"WINDOWS-1251"、"WINDOWS-1252 "、" WINDOWS-1253 "、" WINDOWS-1254 "、" WINDOWS-1255 "、" WINDOWS-1256 "、" WINDOWS-1257 "、" WINDOWS-1258 "。| 否       |
| compressionCodec | 用于读取/写入文本文件的压缩编解码器。 <br>允许的值为**bzip2**、 **gzip**、 **deflate**、 **ZipDeflate**、 **snappy**或**lz4**。 保存文件时要使用的。 <br>注意当前复制活动不支持 "snappy" & "lz4"。<br>请注意，使用复制活动解压缩 ZipDeflate 文件并写入到基于文件的接收器数据存储时，文件将被提取到文件夹： `<path specified in dataset>/<folder named as source zip file>/`。 | 否       |
| compressionLevel | 压缩比。 <br>允许的值为 "**最佳**" 或 "**最快**"。<br>- **速度最快：** 压缩操作应该尽快完成，即使生成的文件未以最佳方式压缩。<br>- **最佳**：应以最佳方式压缩压缩操作，即使操作需要更长的时间才能完成。 有关详细信息，请参阅 [Compression Level](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx)（压缩级别）主题。 | 否       |

下面是 Azure Blob 存储中的 JSON 数据集的示例：

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
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 JSON 源和接收器支持的属性列表。

### <a name="json-as-source"></a>JSON 作为源

复制活动***\*源\**** 部分支持以下属性。

| properties      | 说明                                                  | 必选 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为**JSONSource**。 | 是      |
| storeSettings | 有关如何从数据存储区中读取数据的一组属性。 在 `storeSettings`下，每个基于文件的连接器都有其自己支持的读取设置。 **请参阅连接器文章-> 复制活动属性部分中的详细信息**。 | 否       |

### <a name="json-as-sink"></a>JSON 作为接收器

复制活动***\*接收器\**** 部分支持以下属性。

| properties      | 说明                                                  | 必选 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 复制活动源的 type 属性必须设置为**JSONSink**。 | 是      |
| formatSettings | 一组属性。 请参阅下面的**JSON 写入设置**表。 | 否       |
| storeSettings | 如何将数据写入数据存储区的一组属性。 每个基于文件的连接器在 `storeSettings`下有自己的受支持的写入设置。 **请参阅连接器文章-> 复制活动属性部分中的详细信息**。 | 否       |

`formatSettings`下支持的**JSON 写入设置**：

| properties      | 说明                                                  | 必选                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | FormatSettings 的类型必须设置为**JsonWriteSettings**。 | 是                                                   |
| filePattern |指示每个 JSON 文件中存储的数据模式。 允许的值为：**setOfObjects** 和 **arrayOfObjects**。 **默认**值为 **setOfObjects**。 请参阅 [JSON 文件模式](#json-file-patterns)部分，详细了解这些模式。 |否 |

### <a name="json-file-patterns"></a>JSON 文件模式

复制活动可以自动检测和分析 JSON 文件的以下模式。 

- **类型 I：setOfObjects**

    每个文件包含单个对象，或者以行分隔/串连的多个对象。 
    在复制活动接收器中选择此选项时，复制活动将生成一个 JSON 文件，其中每行包含一个对象（以行分隔）。

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

JSON 文件类型可用作接收器和映射数据流中的源。

### <a name="creating-json-structures-in-a-derived-column"></a>在派生列中创建 JSON 结构

您可以通过派生列表达式生成器将复杂列添加到数据流。 在派生列转换中，通过单击蓝色框来添加一个新列并打开 "表达式生成器"。 若要使列复杂化，可以手动输入 JSON 结构，或使用 UX 以交互方式添加个子列。

#### <a name="using-the-expression-builder-ux"></a>使用表达式生成器 UX

在 "输出架构" 侧窗格中，将鼠标悬停在某一列上，并单击加号图标。 选择 "**添加 subcolumn** "，使列成为复杂类型。

![添加 subcolumn](media/data-flow/addsubcolumn.png "添加 Subcolumn")

您可以通过相同的方式添加更多的列和个子列。 对于每个非复杂字段，可以在右侧的表达式编辑器中添加表达式。

![复杂列](media/data-flow/complexcolumn.png "复杂列")

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

如果选择了 "**单个文档**"，则映射数据流会从每个文件读取一个 JSON 文档。 

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

#### <a name="unquoted-column-names"></a>不带引号的列名

如果选择了不带**引号的列名称**，则映射数据流将读取未括在引号中的 JSON 列。 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>包含注释

如果 JSON 数据具有 C 或C++样式注释，请选择 "**具有注释**"。

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>单引号

如果 JSON 字段和值使用单引号而不是双引号，则选择 "**单引号**"。

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>反斜杠转义

如果使用反斜杠对 JSON 数据中的字符进行转义，则选择 "**单引号**"。

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="next-steps"></a>后续步骤

- [复制活动概述](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
