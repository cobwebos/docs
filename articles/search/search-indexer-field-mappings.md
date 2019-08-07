---
title: 使用索引器进行自动索引的字段映射 - Azure 搜索
description: 针对字段名称和数据表示的差异配置帐户的 Azure 搜索索引器字段映射。
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 771a6e413cd08a338da41c09cd6a0da35e28e5e4
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840657"
---
# <a name="field-mappings-and-transformations-using-azure-search-indexers"></a>使用 Azure 搜索索引器的字段映射和转换

使用 Azure 搜索索引器时, 有时会发现输入数据与目标索引的架构不完全匹配。 在这些情况下, 可以使用**字段映射**在索引过程中调整数据的形状。

在某些情况下，字段映射会很有用：

* 数据源具有一个名为`_id`的字段, 但 Azure 搜索不允许字段名称以下划线开头。 您可以使用字段映射来有效地重命名字段。
* 您希望从同一数据源数据填充索引中的多个字段。 例如, 你可能想要将不同的分析器应用于这些字段。
* 您希望使用来自多个数据源的数据填充索引字段, 并且每个数据源都使用不同的字段名称。
* 需要对数据进行 Base64 编码或解码。 字段映射支持多个**映射函数**，包括用于 Base64 编码和解码的函数。

> [!NOTE]
> Azure 搜索索引器的字段映射功能提供了一种简单的方法来将数据字段映射到索引字段, 并提供用于数据转换的几个选项。 更复杂的数据可能需要预先处理才能将其调整为易于编制索引的形式。
>
> Microsoft Azure 数据工厂是一个功能强大的基于云的解决方案, 可用于导入和转换数据。 你还可以编写代码以在编制索引之前转换源数据。 有关代码示例, 请参阅[模型关系数据](search-example-adventureworks-modeling.md)和[模型多级小平面](search-example-adventureworks-multilevel-faceting.md)。
>

## <a name="set-up-field-mappings"></a>设置字段映射

字段映射由 3 部分组成：

1. `sourceFieldName`，它表示数据源中的字段。 此属性是必需项。
2. 可选的 `targetFieldName`，它表示搜索索引中的字段。 如果已省略，则使用数据源中相同的名称。
3. 可选的 `mappingFunction`，它可以使用几个预定义函数中的一个来转换数据。 函数的完整列表[如下](#mappingFunctions)。

字段映射将添加到`fieldMappings`索引器定义的数组中。

## <a name="map-fields-using-the-rest-api"></a>使用 REST API 映射字段

使用[Create 索引器](https://docs.microsoft.com/rest/api/searchservice/create-Indexer)API 请求创建新的索引器时, 可以添加字段映射。 您可以使用[更新索引器](https://docs.microsoft.com/rest/api/searchservice/update-indexer)API 请求来管理现有索引器的字段映射。

例如, 下面介绍了如何将源字段映射到具有不同名称的目标字段:

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

源字段可以在多个字段映射中引用。 下面的示例演示如何 "分叉" 字段, 将相同的源字段复制到两个不同的索引字段:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure 搜索使用不区分大小写的比较，来解析字段映射中的字段和函数名称。 此操作很方便（大小写无需全都正确），但这表示数据源或索引无法具有仅大小写不同的字段。  
>
>

## <a name="map-fields-using-the-net-sdk"></a>使用 .NET SDK 映射字段

使用[FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping)类在 .net SDK 中定义字段映射, 该类具有属性`SourceFieldName`和`TargetFieldName`、和可选`MappingFunction`引用。

可以在构造索引器时指定字段映射, 或在以后直接设置`Indexer.FieldMappings`属性。

下面C#的示例在构造索引器时设置字段映射。

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>字段映射函数

字段映射函数在字段存储到索引中之前, 转换该字段的内容。 目前支持以下映射函数:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>进行 base64encode 处理函数

执行输入字符串的 *URL 安全* Base64 编码。 假定输入采用 UTF-8 进行编码。

#### <a name="example---document-key-lookup"></a>示例-文档键查找

只有 URL 安全字符才能出现在 Azure 搜索文档密钥中 (因为客户必须能够使用[查找 API](https://docs.microsoft.com/rest/api/searchservice/lookup-document)来处理文档)。 如果键的 "源" 字段包含 URL 不安全字符, 则可以使用`base64Encode`函数在索引时进行转换。

在搜索时检索编码的密钥时, 可以使用`base64Decode`函数来获取原始键值, 并使用它来检索源文档。

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

如果没有为映射函数包含 parameters 属性, 则默认值`{"useHttpServerUtilityUrlTokenEncode" : true}`为。

Azure 搜索支持两个不同的 Base64 编码。 编码和解码同一字段时, 应使用相同的参数。 有关详细信息, 请参阅[base64 编码选项](#base64details)以决定要使用哪些参数。

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode 函数

执行输入字符串的 Base64 解码。 输入假定为*URL 安全*Base64 编码的字符串。

#### <a name="example---decode-blob-metadata-or-urls"></a>示例-解码 blob 元数据或 Url

源数据中可能包含 Base64 编码的字符串, 如 blob 元数据字符串或 web Url, 您希望将这些字符串作为纯文本进行搜索。 填充搜索索引时`base64Decode` , 可以使用函数将编码的数据转换回常规字符串。

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

如果不包括 parameters 属性, 则默认值`{"useHttpServerUtilityUrlTokenEncode" : true}`为。

Azure 搜索支持两个不同的 Base64 编码。 编码和解码同一字段时, 应使用相同的参数。 有关更多详细信息, 请参阅[base64 编码选项](#base64details)以决定要使用哪些参数。

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>base64 编码选项

Azure 搜索支持两个不同的 Base64 编码:**HTTPSERVERUTILITY url 标记**, 以及**不带填充的 url 安全 Base64 编码**。 以后应使用相同的编码选项对在索引过程中采用 base64 编码的字符串进行解码, 否则结果与原始的结果不匹配。

如果分别`useHttpServerUtilityUrlTokenEncode`将`useHttpServerUtilityUrlTokenDecode`编码和解码的或参数设置为`base64Decode` `true`, 则`base64Encode`行为类似于[HttpServerUtility, httpserverutility.urltokenencode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx)的行为类似于[HttpServerUtility. UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx)。

如果未使用完整 .NET Framework (即, 使用 .net Core 或其他框架) 来生成用于模拟 Azure 搜索行为的密钥值, 则应将和`useHttpServerUtilityUrlTokenEncode` `useHttpServerUtilityUrlTokenDecode`设置为`false`。 根据所使用的库, base64 编码和解码函数可能与 Azure 搜索使用的不同。

下表比较了对字符串 `00>00?00` 进行不同的 base64 编码的结果。 若要确定 base64 函数所需的其他处理（如有），请对字符串 `00>00?00` 应用库编码函数，然后比较输出和预期的输出 `MDA-MDA_MDA`。

| 编码 | Base64 编码输出 | 库编码后的其他处理 | 库解码前的其他处理 |
| --- | --- | --- | --- |
| 带填充的 Base64 | `MDA+MDA/MDA=` | 使用 URL 安全字符并删除填充 | 使用标准 base64 字符并添加填充 |
| 不带填充的 Base64 | `MDA+MDA/MDA` | 使用 URL 安全字符 | 使用标准 base64 字符 |
| 带填充的 URL 安全 Base64 | `MDA-MDA_MDA=` | 删除填充 | 添加填充 |
| 不带填充的 URL 安全 Base64 | `MDA-MDA_MDA` | None | 无 |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition 函数

使用指定的分隔符拆分字符串字段，并在所生成拆分的指定位置处选取令牌。

此函数使用以下参数:

* `delimiter`：在拆分输入字符串时，用作分隔符的字符串。
* `position`：在拆分输入字段串后要选取的位置，以零为底的整数。

例如，如果输入是 `Jane Doe`，`delimiter` 是 `" "`（空格）并且 `position` 是 0，则结果为 `Jane`；如果 `position` 是 1，则结果是 `Doe`。 如果位置引用的令牌不存在，则会返回错误。

#### <a name="example---extract-a-name"></a>示例-提取名称

数据源包含 `PersonName` 字段，并且想要为其编制索引作为两个单独的 `FirstName` 和 `LastName` 字段。 可以使用此函数来拆分将空格字符用作分隔符的输入。

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToStringCollection 函数

将已格式化为 JSON 字符串数组的字符串转换为可用于填充索引中 `Collection(Edm.String)` 字段的字符串数组。

例如，如果输入字符串是 `["red", "white", "blue"]`，类型 `Collection(Edm.String)` 的目标字段由 `red`、`white` 和 `blue` 这三个值填充。 对于无法分析为 JSON 字符串数组的输入值，则会返回错误。

#### <a name="example---populate-collection-from-relational-data"></a>示例-填充关系数据中的集合

Azure SQL Database 不具有用于在 azure 搜索中自然映射到`Collection(Edm.String)`字段的内置数据类型。 若要填充字符串集合字段, 可以将源数据预处理为 JSON 字符串数组, 然后使用`jsonArrayToStringCollection`映射函数。

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

有关将关系数据转换为索引集合字段的详细示例, 请参阅[模型关系数据](search-example-adventureworks-modeling.md)。

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlEncode 函数

此函数可用于对字符串进行编码, 使其为 "URL 安全"。 与包含 URL 中不允许的字符的字符串一起使用时, 此函数会将这些 "不安全" 字符转换为等效的字符实体。 此函数使用 UTF-8 编码格式。

#### <a name="example---document-key-lookup"></a>示例-文档键查找

`urlEncode`如果只转换 URL 不安全字符, 则`base64Encode`可以将函数用作函数的替代, 同时将其他字符保留原样。

例如, 输入字符串为`<hello>` -然后, 类型`(Edm.String)`的目标字段将填充值`%3chello%3e`

在搜索时检索编码的密钥时, 可以使用`urlDecode`函数来获取原始键值, 并使用它来检索源文档。

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

 ### <a name="urldecode-function"></a>urlDecode 函数

 此函数使用 UTF-8 编码格式将 URL 编码的字符串转换为已解码的字符串。

 ### <a name="example---decode-blob-metadata"></a>示例-解码 blob 元数据

 某些 Azure 存储客户端会自动对 blob 元数据进行 url 编码 (如果它包含非 ASCII 字符)。 但是, 如果要使此类元数据可搜索 (作为纯文本), 则在填充`urlDecode`搜索索引时, 可以使用函数将编码的数据转换回常规字符串。

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```