---
title: 上传数据（.NET - Azure 搜索）| Microsoft Docs
description: 了解如何使用 .NET SDK 将数据上传到 Azure 搜索中的索引。
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/13/2017
ms.openlocfilehash: fb8fb153c7393af0e084cb96ee2f5e82fba5b220
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="upload-data-to-azure-search-using-the-net-sdk"></a>使用 .NET SDK 将数据上传到 Azure 搜索
> [!div class="op_single_selector"]
> * [概述](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

本文说明如何使用 [Azure 搜索 .NET SDK](https://aka.ms/search-sdk) 将数据导入到 Azure 搜索索引中。

在开始本演练前，应已 [创建 Azure 搜索索引](search-what-is-an-index.md)。 本文还假定已创建 `SearchServiceClient` 对象，如 [使用 .NET SDK 创建 Azure 搜索索引](search-create-index-dotnet.md#CreateSearchServiceClient)中所示。

> [!NOTE]
> 本文中的所有示例代码均用 C# 编写。 可以 [在 GitHub 上](http://aka.ms/search-dotnet-howto)找到完整的源代码。 也可参阅 [Azure 搜索 .NET SDK](search-howto-dotnet-sdk.md)，以便更详细地了解示例代码。

要使用 .NET SDK 将文档推送到索引，需要执行以下操作：

1. 创建 `SearchIndexClient` 对象以连接到搜索索引。
2. 创建包含要添加、修改或删除的文档的 `IndexBatch` 。
3. 调用 `SearchIndexClient` 的 `Documents.Index` 方法，将 `IndexBatch` 发送到搜索索引。

## <a name="create-an-instance-of-the-searchindexclient-class"></a>创建 SearchIndexClient 类的实例
要使用 Azure 搜索 .NET SDK 将数据导入到索引中，需要创建 `SearchIndexClient` 类的实例。 可以自己构造此实例，但如果已有可调用其 `Indexes.GetClient` 方法的 `SearchServiceClient` 实例会更容易。 例如，下面是如何从名为 `serviceClient` 的 `SearchServiceClient` 获取名为“hotels”的索引的 `SearchIndexClient`：

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> 在典型的搜索应用程序中，索引管理和填充由搜索查询中的一个单独的组件处理。 `Indexes.GetClient` 对于填充索引很方便，因为使用它则无需提供另一个 `SearchCredentials`。 它通过向新 `SearchIndexClient` 传递用于创建 `SearchServiceClient` 的管理密钥来实现此目的。 但是，在执行查询的应用程序中，最好直接创建 `SearchIndexClient` ，这样可以传入查询密钥而不是管理密钥。 这与 [最小特权原则](https://en.wikipedia.org/wiki/Principle_of_least_privilege) 一致，可帮助使应用程序更安全。 可在 [Azure 搜索 REST API 参考](https://docs.microsoft.com/rest/api/searchservice/)中了解管理密钥和查询密钥的详细信息。
> 
> 

`SearchIndexClient` 具有 `Documents` 属性。 此属性提供在索引中添加、修改、删除或查询文档所需的所有方法。

## <a name="decide-which-indexing-action-to-use"></a>确定要使用的索引操作
要使用 .NET SDK 导入数据，需要将数据打包到 `IndexBatch` 对象中。 `IndexBatch` 封装 `IndexAction` 对象的集合，其中每个对象均包含一个文档和一个属性，用于指示 Azure 搜索要对该文档上执行什么操作（上传、合并、删除等）。 根据选择的以下操作，每个文档必须仅包含某些特定的字段：

| 操作 | 说明 | 每个文档必需的字段 | 说明 |
| --- | --- | --- | --- |
| `Upload` |`Upload` 操作类似于“upsert”，如果文档是新文档，则插入；如果文档已经存在，则进行更新/替换。 |关键字段以及要定义的任何其他字段 |更新/替换现有文档时，会将请求中未指定的任何字段设置为 `null`。 即使该字段之前设置为了非 null 值也是如此。 |
| `Merge` |使用指定的字段更新现有文档。 如果索引中不存在该文档，merge 会失败。 |关键字段以及要定义的任何其他字段 |merge 中指定的任何字段都将替换文档中的现有字段。 这包括 `DataType.Collection(DataType.String)`类型的字段。 例如，如果文档包含值为 `["budget"]` 的字段 `tags`，并且已使用值 `["economy", "pool"]` 对 `tags` 执行合并，则 `tags` 字段的最终值将为 `["economy", "pool"]`。 而不会是 `["budget", "economy", "pool"]`。 |
| `MergeOrUpload` |如果索引中已存在具有给定关键字段的文档，则此操作的行为类似于 `Merge`。 如果该文档不存在，则它的行为类似于对新文档进行 `Upload` 。 |关键字段以及要定义的任何其他字段 |- |
| `Delete` |从索引中删除指定文档。 |仅关键字段 |所指定关键字段以外的所有字段都会被忽略。 如果要从文档中删除单个字段，请改用 `Merge`，只需将该字段显式设置为 null。 |

可以指定要用于 `IndexBatch` 和 `IndexAction` 类的各个静态方法的操作，如下一部分中所示。

## <a name="construct-your-indexbatch"></a>构造 IndexBatch
知道要对文档执行哪些操作后，就可以构造 `IndexBatch`了。 以下示例演示如何创建包含几个不同操作的 Batch。 请注意，此示例使用一个名为 `Hotel` 的自定义类，它映射到“hotels”索引中的文档。

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

此示例使用 `Upload`、`MergeOrUpload` 和 `Delete` 作为搜索操作，这些操作由 `IndexAction` 类中调用的方法指定。

假定“hotels”这一示例索引已填充了许多文档。 注意，使用 `MergeOrUpload` 时不必指定所有可能的文档字段，使用 `Delete` 时仅指定文档关键字段 `HotelId`。

另请注意，在单个索引请求中最多只能包含 1000 个文档。

> [!NOTE]
> 在此示例中，要将不同的操作应用到不同的文档。 如果要对批次中的所有文档执行相同的操作，请不要调用 `IndexBatch.New`，可以使用 `IndexBatch` 的其他静态方法。 例如，可以通过调用 `IndexBatch.Merge`、`IndexBatch.MergeOrUpload` 或 `IndexBatch.Delete` 创建批次。 这些方法接受文档的集合（在此示例中为 `Hotel` 类型的对象），而不是 `IndexAction` 对象。
> 
> 

## <a name="import-data-to-the-index"></a>将数据导入到索引
获得已初始化的 `IndexBatch` 对象后，便可以通过对 `SearchIndexClient` 对象调用 `Documents.Index` 将其发送到索引。 以下示例说明如何调用 `Index`，以及需要执行的一些额外步骤：

```csharp
try
{
    indexClient.Documents.Index(batch);
}
catch (IndexBatchException e)
{
    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
    // the batch. Depending on your application, you can take compensating actions like delaying and
    // retrying. For this simple demo, we just log the failed document keys and continue.
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

注意对 `Index` 的调用周围的 `try`/`catch` catch 块处理编制索引可能出现的重要错误情况。 如果 Azure 搜索服务无法为 Batch 中的某些文档编制索引，`Documents.Index` 将引发 `IndexBatchException`。 如果在服务负载过大时为文档编制索引，可能会发生这种情况。 **强烈建议在代码中显式处理这种情况。** 可以延迟为失败的文档编制索引，并重试，也可以像此示例一样记录并继续执行，还可以执行其他操作，具体取决于应用程序对数据一致性的要求。

最后，上面示例中的代码延迟了两秒钟。 编制索引在 Azure 搜索服务中异步进行，因此，示例应用程序需要等待很短时间，以确保文档可用于搜索。 此类延迟通常仅在演示、测试和示例应用程序中是必需的。

<a name="HotelClass"></a>

### <a name="how-the-net-sdk-handles-documents"></a>.NET SDK 如何处理文档
用户可能想知道 Azure 搜索 .NET SDK 如何将用户定义的类（如 `Hotel`）的实例上传到索引。 为了帮助回答这个问题，让我们看一下 `Hotel` 类，该类映射到 [使用 .NET SDK 创建 Azure 搜索索引](search-create-index-dotnet.md#DefineIndex)中定义的索引架构：

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    // ToString() method omitted for brevity...
}
```

要注意的第一个问题是，`Hotel` 的每个公共属性均对应于索引定义中的一个字段，但有一个重要差异：每个字段的名称均以小写字母开头（“骆驼拼写法”），而 `Hotel` 的每个公共属性名称均以大写字母开头（“帕斯卡拼写法”）。 在执行目标架构不受应用程序开发人员控制的数据绑定的 .NET 应用程序中，这种情况很常见。 不必违反 .NET 命名准则将属性名设为 camel 大小写，而可以使用 `[SerializePropertyNamesAsCamelCase]` 属性指示 SDK 将属性名自动映射到 camel 大小写。

> [!NOTE]
> Azure 搜索 .NET SDK 使用 [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) 库将自定义模型对象序列化为 JSON 和从 JSON 反序列化。 如果需要，可以自定义此序列化。 可在[使用 JSON.NET 的自定义序列](search-howto-dotnet-sdk.md#JsonDotNet)中了解更多详细信息。 此类的一个示例是对上面的示例代码中的 `DescriptionFr` 属性使用 `[JsonProperty]` 特性。
> 
> 

有关 `Hotel` 类的第二个重要问题是公共属性的数据类型。 这些属性的 .NET 类型映射到它们在索引定义中的等效字段类型。 例如，`Category` 字符串属性映射到 `DataType.String` 类型的 `category` 字段。 `bool?` 和 `DataType.Boolean`、`DateTimeOffset?`和 `DataType.DateTimeOffset` 等之间存在相似的类型映射。 [Azure 搜索 .NET SDK 参考](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations#Microsoft_Azure_Search_IDocumentsOperations_GetWithHttpMessagesAsync__1_System_String_System_Collections_Generic_IEnumerable_System_String__Microsoft_Azure_Search_Models_SearchRequestOptions_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_)中的 `Documents.Get` 方法记录了类型映射的具体规则。

使用自己的类作为文档的这种功能可以在这两个方向上正常工作；此外，还可以检索搜索结果，并使用 SDK 自动将结果反序列化为所选类型，如 [下一篇文章](search-query-dotnet.md)中所示。

> [!NOTE]
> Azure 搜索 .NET SDK 还使用 `Document` 类支持动态类型化文档，该类是字段名称到字段值的键/值映射。 如果在设计时不知道索引架构，或者绑定到特定模型类不太方便，这很有用。 该 SDK 中处理文档的所有方法都有使用 `Document` 类的重载，以及采用泛型类型参数的强类型重载。 本文中的示例代码仅使用后者。
> 
> 

**为何应使用可为 null 的数据类型**

设计自己的模型类以映射到 Azure 搜索索引时，建议将值类型的属性（如 `bool` 和 `int`）声明为可以为 null（例如，`bool?` 而不是 `bool`）。 如果使用不可为 null 属性，必须 **保证** 索引中的所有文档的对应字段都不包含 null 值。 该 SDK 和 Azure 搜索服务都不会帮助强制实施此检查。

这不只是假想的问题：假设将新字段添加到 `DataType.Int32`类型的现有索引。 更新索引定义后，所有文档的该新字段都具有 null 值（因为 Azure 搜索中的所有类型都可以为 null）。 如果随后使用该字段具有不可为 null `int` 属性的模型类，则在尝试检索文档时会获得如下所示的 `JsonSerializationException`：

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

由于此原因，最佳做法是建议在模型类中使用可以为 null 的类型。

## <a name="next-steps"></a>后续步骤
填充 Azure 搜索索引后，即可发出查询，搜索文档。 有关详细信息，请参阅 [查询 Azure 搜索索引](search-query-overview.md) 。

