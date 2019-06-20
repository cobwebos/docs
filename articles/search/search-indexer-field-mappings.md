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
ms.openlocfilehash: 3546e342b535a122ea4ed3f844cd5e28a76d551a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147795"
---
# <a name="field-mappings-and-transformations-using-azure-search-indexers"></a>字段映射和转换使用 Azure 搜索索引器

在使用 Azure 搜索索引器，你有时会发现输入的数据不完全匹配的目标索引架构。 在这些情况下，你可以使用**字段映射**进行调整索引过程中，你的数据。

在某些情况下，字段映射会很有用：

* 数据源中有一个名为字段`_id`，但 Azure 搜索不允许以下划线开头的字段名称。 字段映射，可有效地重命名字段。
* 你想要填充从相同的数据源数据中的索引中的多个字段。 例如，你可能想要将不同的分析器应用于这些字段。
* 你想要填充索引字段使用多个数据源中的数据和每个数据源使用不同的字段名称。
* 需要对数据进行 Base64 编码或解码。 字段映射支持多个**映射函数**，包括用于 Base64 编码和解码的函数。

> [!NOTE]
> Azure 搜索索引器字段映射功能提供了一种将数据字段映射到索引的字段，使用数据转换的几个选项的简单方法。 更复杂的数据可能需要预处理，以便重塑，使其易于索引的窗体。
>
> Microsoft Azure 数据工厂是功能强大的基于云的解决方案用于导入和转换数据。 此外可以编写代码，以在索引之前转换源数据。 有关代码示例，请参阅[关系数据建模](search-example-adventureworks-modeling.md)并[建模多层分面](search-example-adventureworks-multilevel-faceting.md)。
>

## <a name="set-up-field-mappings"></a>设置字段映射

字段映射由 3 部分组成：

1. `sourceFieldName`，它表示数据源中的字段。 此属性是必需项。
2. 可选的 `targetFieldName`，它表示搜索索引中的字段。 如果已省略，则使用数据源中相同的名称。
3. 可选的 `mappingFunction`，它可以使用几个预定义函数中的一个来转换数据。 函数的完整列表[如下](#mappingFunctions)。

字段映射将添加到`fieldMappings`数组索引器定义。

## <a name="map-fields-using-the-rest-api"></a>使用 REST API 的字段映射

创建新索引器使用时，可以添加字段映射[创建索引器](https://docs.microsoft.com/rest/api/searchservice/create-Indexer)API 请求。 你可以管理现有的索引器使用的字段映射[更新索引器](https://docs.microsoft.com/rest/api/searchservice/update-indexer)API 请求。

例如，下面介绍了如何将源字段映射到具有不同名称的目标域：

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

可以在多个字段映射引用的源字段。 下面的示例演示如何"分叉"字段，将相同的源字段复制到两个不同的索引字段：

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

## <a name="map-fields-using-the-net-sdk"></a>使用.NET SDK 的字段映射

使用在.NET SDK 中定义的字段映射[FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping)类，该类具有属性`SourceFieldName`并`TargetFieldName`，和一个可选`MappingFunction`引用。

构造索引器时或更高版本通过直接设置，可以指定字段映射`Indexer.FieldMappings`属性。

以下C#的示例构造一个索引器时设置字段映射。

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

字段映射函数转换之前存储在索引中的字段的内容。 当前支持以下映射函数：

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>进行 base64encode 处理函数

执行输入字符串的 *URL 安全* Base64 编码。 假定输入采用 UTF-8 进行编码。

#### <a name="example---document-key-lookup"></a>示例-查找文档密钥

只能使用 URL 安全字符可以出现在 Azure 搜索文档密钥中 (因为客户必须能够解决文档使用[查找 API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) )。 如果你的密钥的源字段包含 URL 不安全字符，则可以使用`base64Encode`函数将在创建索引时对其进行转换。

当在搜索时检索已编码的密钥时，然后可以使用`base64Decode`函数获取的原始键值，并使用它来检索源文档。

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

如果映射函数，不包含参数属性，则默认值为`{"useHttpServerUtilityUrlTokenEncode" : true}`。

Azure 搜索支持两种不同的 Base64 编码。 应使用相同的参数时编码和解码的相同字段。 有关详细信息，请参阅[base64 编码选项](#base64details)来决定要使用哪些参数。

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode 函数

执行输入字符串的 Base64 解码。 输入被假定为*URL 安全*Base64 编码的字符串。

#### <a name="example---decode-blob-metadata-or-urls"></a>示例-解码 blob 元数据或 Url

您的源数据可能包含 Base64 编码的字符串，例如 blob 元数据字符串或你想要以明文形式可搜索的 web Url。 可以使用`base64Decode`函数时要旋转的编码的数据返回到常规字符串填充搜索索引。

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

如果不包含参数属性，则默认值为`{"useHttpServerUtilityUrlTokenEncode" : true}`。

Azure 搜索支持两种不同的 Base64 编码。 应使用相同的参数时编码和解码的相同字段。 有关更多详细信息，请参阅[base64 编码选项](#base64details)来决定要使用哪些参数。

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>base64 编码选项

Azure 搜索支持两种不同的 Base64 编码：**HttpServerUtility URL 令牌**，并**无填充 URL 安全 Base64 编码**。 在索引期间采用 base64 编码的字符串应更高版本进行解码使用相同的编码选项，否则结果不会与原始值匹配。

如果`useHttpServerUtilityUrlTokenEncode`或`useHttpServerUtilityUrlTokenDecode`参数进行编码和解码分别设置为`true`，然后`base64Encode`的行为类似于[HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx)和`base64Decode`的行为类似于[HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx)。

如果未使用完整的.NET Framework （即，正在使用.NET Core 或另一个框架） 若要生成的键值来模拟 Azure 搜索的行为，则应设置`useHttpServerUtilityUrlTokenEncode`并`useHttpServerUtilityUrlTokenDecode`到`false`。 具体取决于您使用的库，从 Azure 搜索使用的可能不同的 base64 编码和解码的函数。

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

#### <a name="example---extract-a-name"></a>示例-提取的名称

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

#### <a name="example---populate-collection-from-relational-data"></a>示例-填充从关系数据的集合

Azure SQL 数据库不具有能自然映射到的内置数据类型`Collection(Edm.String)`Azure 搜索中的字段。 若要填充字符串集合字段，您可以预先处理源数据作为 JSON 字符串数组，然后使用`jsonArrayToStringCollection`映射函数。

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

将关系数据转换为索引集合字段的详细示例，请参阅[关系数据建模](search-example-adventureworks-modeling.md)。
