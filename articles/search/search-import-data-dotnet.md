---
title: 将数据加载到 C# (.NET SDK) 中的 Azure 搜索索引 - Azure 搜索
description: 了解如何使用 C# 示例代码和 .NET SDK 将数据上传到 Azure 搜索中的全文可搜索索引。
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: d2d54d1425bbb67a3f5ba1b6081a9f74ff87f4d6
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286901"
---
# <a name="quickstart-2---load-data-to-an-azure-search-index-using-c"></a>快速入门：2 - 使用 C# 将数据加载到 Azure 搜索索引

本文介绍如何使用 C# 和 [.NET SDK](https://aka.ms/search-sdk) 将数据导入到 [Azure 搜索索引](search-what-is-an-index.md)。 执行以下任务可将文档推送到索引：

> [!div class="checklist"]
> * 创建 [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) 对象以连接到搜索索引。
> * 创建包含要添加、修改或删除的文档的 [`IndexBatch`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 对象。
> * 在 `SearchIndexClient` 上调用 `Documents.Index` 方法以将文档上传到索引。

## <a name="prerequisites"></a>先决条件

[创建 Azure 搜索索引](search-create-index-dotnet.md)和 `SearchServiceClient` 对象，如[“创建客户端”](search-create-index-dotnet.md#CreateSearchServiceClient)中所示。


## <a name="create-a-client"></a>创建客户端
若要导入数据，则需要 `SearchIndexClient` 类的一个实例。 有几种方法可用于创建此类，包括使用已创建的 `SearchServiceClient` 实例。 

如以下示例所示，可使用 `SearchServiceClient` 实例并调用其 `Indexes.GetClient` 方法。 此代码片段从名为 `serviceClient` 的 `SearchServiceClient` 中获取名为“hotels”的索引的 `SearchIndexClient`。

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

`SearchIndexClient` 具有 `Documents` 属性。 此属性提供在索引中添加、修改、删除或查询文档所需的所有方法。

> [!NOTE]
> 在典型的搜索应用程序中，单独处理查询和索引。 虽然 `Indexes.GetClient` 很方便，因为可重复使用 `SearchCredentials` 等对象，但更可靠的方法是直接创建 `SearchIndexClient`，这样就可传入查询密钥而不是管理密钥。 此做法与[最小特权原则](https://en.wikipedia.org/wiki/Principle_of_least_privilege)一致，可帮助使应用程序更安全。 可在下一个练习中构造 `SearchIndexClient`。 有关密钥的详细信息，请参阅[创建和管理 Azure 搜索服务的 API 密钥](search-security-api-keys.md)。
> 
> 

<a name="construct-indexbatch"></a>

## <a name="construct-indexbatch"></a>构造 IndexBatch

若要使用 .NET SDK 导入数据，请将数据打包到 `IndexBatch` 对象中。 `IndexBatch` 封装 `IndexAction` 对象的集合，其中每个对象均包含一个文档和一个属性，用于指示 Azure 搜索要对该文档执行什么操作（上传、合并、删除和 mergeOrUpload 等）。 有关索引操作的详细信息，请参阅[索引操作：上传、合并、mergeOrUpload、删除](search-what-is-data-import.md#indexing-actions)。

假设你已知道要对文档执行哪些操作，现在可以构造 `IndexBatch` 了。 以下示例演示如何创建包含几个不同操作的 Batch。 此示例使用一个名为 `Hotel` 的自定义类，它映射到“hotels”索引中的文档。

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

## <a name="call-documentsindex"></a>调用 Documents.Index
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

注意对 `Index` 的调用周围的 `try`/`catch` catch 块处理编制索引可能出现的重要错误情况。 如果 Azure 搜索服务无法为 Batch 中的某些文档编制索引，`Documents.Index` 将引发 `IndexBatchException`。 如果在服务负载过大时为文档编制索引，可能会发生这种情况。 **强烈建议在代码中显式处理这种情况。**  可以延迟为失败的文档编制索引，并重试，也可以像此示例一样记录并继续执行，还可以执行其他操作，具体取决于应用程序对数据一致性的要求。

最后，上面示例中的代码延迟了两秒钟。 编制索引在 Azure 搜索服务中异步进行，因此，示例应用程序需要等待很短时间，以确保文档可用于搜索。 此类延迟通常仅在演示、测试和示例应用程序中是必需的。

有关文档处理的详细信息，请参阅[“.NET SDK 如何处理文档”](search-howto-dotnet-sdk.md#how-dotnet-handles-documents)。


## <a name="next-steps"></a>后续步骤
填充 Azure 搜索索引后，下一步是发出查询，搜索文档。 

> [!div class="nextstepaction"]
> [在 C# 中查询 Azure 搜索索引](search-query-dotnet.md)
