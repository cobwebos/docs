---
title: 索引器中的字段映射
titleSuffix: Azure Cognitive Search
description: 针对字段名称和数据表示的差异配置帐户索引器中的字段映射。
manager: nitinme
author: mattmsft
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fa815d9fb653ee61d647023f7867549aa8d655aa
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005800"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>使用 Azure 认知搜索索引器进行字段映射和转换

使用 Azure 认知搜索索引器时，你有时会发现，输入数据与目标索引的架构不完全匹配。 在这种情况下，可以在索引编制过程中使用**字段映射**来调整数据的形状。

在某些情况下，字段映射会很有用：

* 数据源具有名为 `_id` 的字段，但 Azure 认知搜索不允许字段名称以下划线开头。 使用字段映射可以有效地为字段重命名。
* 你希望使用同一数据源数据填充索引中的多个字段。 例如，你可能想要将不同的分析器应用到这些字段。
* 你希望使用多个数据源中的数据填充索引字段，而每个数据源使用不同的字段名称。
* 需要对数据进行 Base64 编码或解码。 字段映射支持多个**映射函数**，包括用于 Base64 编码和解码的函数。

> [!NOTE]
> 索引器中的字段映射是将数据字段映射到索引字段的一种简单方法，可实现轻型数据转换。 更复杂的数据可能需要预先处理才能将其调整为有利于索引的形式。 可以考虑的一个选项是[Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/)。

## <a name="set-up-field-mappings"></a>设置字段映射

字段映射由 3 部分组成：

1. `sourceFieldName`，它表示数据源中的字段。 此属性是必需项。
2. 可选的 `targetFieldName`，它表示搜索索引中的字段。 如果已省略，则使用数据源中相同的名称。
3. 可选的 `mappingFunction`，它可以使用几个预定义函数中的一个来转换数据。 这可以同时应用于输入和输出字段映射。 函数的完整列表[如下](#mappingFunctions)。

字段映射将添加到索引器定义的 `fieldMappings` 数组中。

## <a name="map-fields-using-the-rest-api"></a>使用 REST API 映射字段

使用[创建索引器](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) API 请求创建新的索引器时，可以添加字段映射。 可以使用[更新索引器](https://docs.microsoft.com/rest/api/searchservice/update-indexer) API 请求来管理现有索引器的字段映射。

例如，下面演示了如何将一个源字段映射到具有不同名称的目标字段：

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

可以在多个字段映射中引用一个源字段。 以下示例演示如何“分叉”字段 - 将同一个源字段复制到两个不同的索引字段：

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure 认知搜索使用不区分大小写的比较，来解析字段映射中的字段和函数名称。 此操作很方便（大小写无需全都正确），但这表示数据源或索引无法具有仅大小写不同的字段。  
>
>

## <a name="map-fields-using-the-net-sdk"></a>使用 .NET SDK 映射字段

在 .NET SDK 中，使用 [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) 类定义字段映射，该类包含属性 `SourceFieldName` 和 `TargetFieldName`，以及可选的 `MappingFunction` 引用。

可以在构造索引器时指定字段映射，以后也可以通过直接设置 `Indexer.FieldMappings` 属性来指定字段映射。

以下 C# 示例在构造索引器时设置字段映射。

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

字段映射函数在将字段存储到索引中之前转换该字段的内容。 目前支持以下映射函数：

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Encode 函数

执行输入字符串的 *URL 安全* Base64 编码。 假定输入采用 UTF-8 进行编码。

#### <a name="example---document-key-lookup"></a>示例 - 文档键查找

Azure 认知搜索文档键中只能使用 URL 安全字符（因为客户必须能够使用[查找 API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) 来寻址文档）。 如果键的源字段包含 URL 不安全的字符，在编制索引时，你可以使用 `base64Encode` 函数来转换该字段。 但是，文档键（转换前后）的长度不能超过 1,024 个字符。

在搜索时检索编码的键时，可以使用 `base64Decode` 函数获取原始键值，然后使用该值来检索源文档。

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

如果未包含映射函数的 parameters 属性，该属性的默认值为 `{"useHttpServerUtilityUrlTokenEncode" : true}`。

Azure 认知搜索支持两种不同的 Base64 编码： 在编码和解码同一字段时，应使用相同的参数。 在决定要使用哪些参数时，请参阅 [base64 编码选项](#base64details)了解详细信息。

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode 函数

执行输入字符串的 Base64 解码。 假设输入是 URL 安全的 Base64 编码字符串。 

#### <a name="example---decode-blob-metadata-or-urls"></a>示例 - 解码 Blob 元数据或 URL

源数据可能包含 Base64 编码的字符串（例如 Blob 元数据字符串或 Web URL），你希望这些字符串可作为纯文本进行搜索。 可以在填充搜索索引时，使用 `base64Decode` 函数将编码的数据转换回到常规字符串。

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

如果未包含 parameters 属性，该属性的默认值为 `{"useHttpServerUtilityUrlTokenEncode" : true}`。

Azure 认知搜索支持两种不同的 Base64 编码。 在编码和解码同一字段时，应使用相同的参数。 在决定要使用哪些参数时，请参阅 [base64 编码选项](#base64details)了解更多详细信息。

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>base64 编码选项

Azure 认知搜索支持 URL 安全的 base64 编码和正常的 base64 编码。 在索引编制期间经过 base64 编码的字符串在以后应使用相同的编码选项进行解码，否则结果将与原始字符串不匹配。

如果将用于编码或解码的 `useHttpServerUtilityUrlTokenEncode` 或 `useHttpServerUtilityUrlTokenDecode` 参数分别设置为 `true`，则 `base64Encode` 的行为与 [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) 类似，`base64Decode` 的行为与 [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx) 类似。

> [!WARNING]
> 如果使用 `base64Encode` 来生成密钥值，则必须将 `useHttpServerUtilityUrlTokenEncode` 设置为 true。 只能将 URL 安全的 base64 编码用于密钥值。 请参阅[命名规则（Azure 认知搜索）](https://docs.microsoft.com/rest/api/searchservice/naming-rules)，了解对密钥值中字符的整套限制。

Azure 认知搜索中的 .NET 库采用完整的 .NET 框架来提供内置编码。 `useHttpServerUtilityUrlTokenEncode`和`useHttpServerUtilityUrlTokenDecode`选项利用此内置功能。 如果使用 .NET Core 或其他框架，建议将这些选项设置为 `false` 并直接调用框架的编码和解码函数。

下表比较了对字符串 `00>00?00` 进行不同的 base64 编码的结果。 若要确定 base64 函数所需的其他处理（如有），请对字符串 `00>00?00` 应用库编码函数，然后比较输出和预期的输出 `MDA-MDA_MDA`。

| 编码 | Base64 编码输出 | 库编码后的其他处理 | 库解码前的其他处理 |
| --- | --- | --- | --- |
| 带填充的 Base64 | `MDA+MDA/MDA=` | 使用 URL 安全字符并删除填充 | 使用标准 base64 字符并添加填充 |
| 不带填充的 Base64 | `MDA+MDA/MDA` | 使用 URL 安全字符 | 使用标准 base64 字符 |
| 带填充的 URL 安全 Base64 | `MDA-MDA_MDA=` | 删除填充 | 添加填充 |
| 不带填充的 URL 安全 Base64 | `MDA-MDA_MDA` | 无 | 无 |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition 函数

使用指定的分隔符拆分字符串字段，并在所生成拆分的指定位置处选取令牌。

此函数使用以下参数：

* `delimiter`：在拆分输入字符串时，用作分隔符的字符串。
* `position`：在拆分输入字段串后要选取的位置，以零为底的整数。

例如，如果输入是 `Jane Doe`，`delimiter` 是 `" "`（空格）并且 `position` 是 0，则结果为 `Jane`；如果 `position` 是 1，则结果是 `Doe`。 如果位置引用的令牌不存在，则会返回错误。

#### <a name="example---extract-a-name"></a>示例 - 提取名称

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

#### <a name="example---populate-collection-from-relational-data"></a>示例 - 使用关系数据填充集合

Azure SQL 数据库不具有能自然映射到 Azure 认知搜索中 `Collection(Edm.String)` 字段的内置数据类型。 若要填充字符串集合字段，可将源数据预处理成 JSON 字符串数组，然后使用 `jsonArrayToStringCollection` 映射函数。

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlEncode 函数

此函数可用于对字符串进行编码，使其是“URL 安全的”。 与包含 URL 中不允许的字符的字符串一起使用时，此函数会将这些“不安全”字符转换为字符实体等效项。 此函数使用 UTF-8 编码格式。

#### <a name="example---document-key-lookup"></a>示例 - 文档键查找

如果只转换 URL 不安全字符，而将其他字符保留原样，则可以使用 `urlEncode` 函数来代替 `base64Encode` 函数。

例如，如果输入字符串是 `<hello>` - 则 `(Edm.String)` 类型的目标字段中将填充值 `%3chello%3e`

在搜索时检索编码的键时，可以使用 `urlDecode` 函数获取原始键值，然后使用该值来检索源文档。

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

 此函数使用 UTF-8 编码格式将 URL 编码的字符串转换为解码的字符串。

 ### <a name="example---decode-blob-metadata"></a>示例 - 解码 Blob 元数据

 如果 Blob 元数据包含非 ASCII 字符，某些 Azure 存储客户端会自动对这些元数据进行 URL 编码。 但是，若要使此类元数据可搜索（作为纯文本），可以在填充搜索索引时，使用 `urlDecode` 函数将编码的数据转换回到常规字符串。

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
 
 <a name="fixedLengthEncodeFunction"></a>
 
 ### <a name="fixedlengthencode-function"></a>fixedLengthEncode 函数
 
 此函数将任意长度的字符串转换为固定长度的字符串。
 
 ### <a name="example---map-document-keys-that-are-too-long"></a>示例 - 映射过长的文档键
 
当遇到文档键长度超过 1024 个字符的错误时，可以应用此函数来减少文档键的长度。

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "metadata_storage_path",
    "targetFieldName" : "your key field",
    "mappingFunction" : {
      "name" : "fixedLengthEncode"
    }
  }]
 ```
