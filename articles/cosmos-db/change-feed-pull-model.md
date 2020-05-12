---
title: 更改源请求模型
description: 了解如何使用 Azure Cosmos DB 更改源请求模型来读取更改源以及请求模型和更改源处理器之间的差异
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/10/2020
ms.reviewer: sngun
ms.openlocfilehash: 0e6e243ceb73ca2a1180e59ba6c6b4095ed6069a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116707"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>更改 Azure Cosmos DB 中的源请求模型

使用更改源请求模型，可以按自己的节奏使用 Azure Cosmos DB 更改源。 与[更改源处理器](change-feed-processor.md)一样，可以使用更改源请求模型并行处理跨多个更改源使用者所做的更改。

> [!NOTE]
> 更改源请求模型目前仅在[Azure Cosmos DB .NET SDK 中处于预览阶段](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview)。 预览版尚不适用于其他 SDK 版本。

## <a name="consuming-an-entire-containers-changes"></a>使用整个容器的更改

您可以 `FeedIterator` 使用请求模型创建来处理更改源。 最初创建时 `FeedIterator` ，可以在中指定一个可选的 `StartTime` `ChangeFeedRequestOptions` 。 如果未指定， `StartTime` 将为当前时间。

`FeedIterator`分为两种风格。 除了返回实体对象的以下示例之外，还可以获取支持的响应 `Stream` 。 流使你可以在不先进行反序列化的情况下读取数据，而是在客户端资源上保存。

下面是一个示例，用于获取 `FeedIterator` 返回实体对象的，在本例中为 `User` 对象：

```csharp
FeedIterator<User> iteratorWithPOCOS = container.GetChangeFeedIterator<User>();
```

下面是一个示例，用于获取 `FeedIterator` 返回的 `Stream` ：

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator();
```

使用 `FeedIterator` ，你可以轻松地按自己的节奏处理整个容器的更改源。 下面是一个示例：

```csharp
FeedIterator<User> iteratorForTheEntireContainer= container.GetChangeFeedIterator(new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

while (iteratorForTheEntireContainer.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="consuming-a-partition-keys-changes"></a>使用分区键的更改

在某些情况下，您可能只需要处理特定分区键的更改。 你可以获取 `FeedIterator` 特定分区键的，并以相同的方式处理整个容器的更改：

```csharp
FeedIterator<User> iteratorForThePartitionKey = container.GetChangeFeedIterator(new PartitionKey("myPartitionKeyValueToRead"), new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

while (iteratorForThePartitionKey.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>使用 FeedRange 进行并行化

在[更改源处理器](change-feed-processor.md)中，工作自动分布到多个使用者。 在更改源请求模型中，可以使用 `FeedRange` 并行处理更改源的处理。 `FeedRange`表示一系列分区键值。

下面是一个示例，演示如何获取容器的范围列表：

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

获取容器的 FeedRanges 列表时， `FeedRange` 每个[物理分区](partition-data.md#physical-partitions)会获得一个列表。

`FeedRange`然后，你可以创建 `FeedIterator` 来并行处理跨多个计算机或线程的更改源。 与上面演示如何获取整个容器的单个示例不同 `FeedIterator` ，你可以使用 `FeedRange` 来获取多个 FeedIterators，它可以并行处理更改源。

如果要使用 FeedRanges，需要具有一个可获取 FeedRanges 并将其分发到这些计算机的 orchestrator 进程。 此分发可以是：

* 使用 `FeedRange.ToJsonString` 并分发此字符串值。 使用者可以将此值用于`FeedRange.FromJsonString`
* 如果分布进程正在进行，则传递 `FeedRange` 对象引用。

下面的示例演示如何使用两个与并行读取的假设单独的计算机从容器的更改源开始读取：

计算机1：

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<Person>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorA.HasMoreResults)
{
   FeedResponse<User> users = await iteratorA.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

计算机2：

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ranges[1], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorB.HasMoreResults)
{
   FeedResponse<User> users = await iteratorB.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="saving-continuation-tokens"></a>保存继续标记

您可以 `FeedIterator` 通过创建一个继续标记来保存的位置。 继续标记是一个字符串值，用于跟踪 FeedIterator 的上次处理的更改。 这允许 `FeedIterator` 稍后在此点继续。 以下代码将在创建容器后，通读更改源。 当没有更多更改可用时，它会保留一个继续标记，以便以后可以恢复更改源使用。

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

string continuation = null;

while (iterator.HasMoreResults)
{
   FeedResponse<User> users = await iterator.ReadNextAsync();
   continuation = users.ContinuationToken;

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(continuation);
```

## <a name="comparing-with-change-feed-processor"></a>与更改源处理器进行比较

许多情况下，可以使用[更改源处理器](change-feed-processor.md)或请求模型处理更改源。 请求模型的继续标记和更改源处理器的租约容器均为更改源中最后处理的一项（或一批项）的 "书签"。
但是，不能将继续标记转换为租赁容器（或反之）。

在这些情况下，应考虑使用请求模型：

- 要对更改源中的现有数据执行一次性读取
- 只需读取特定分区键中的更改
- 您不希望使用推送模型并想要按自己的节奏使用更改源

下面是更改源处理器与请求模型之间的一些主要差异：

|  | 更改源处理器| 请求模型 |
| --- | --- | --- |
| 跟踪当前点处理更改源 | 租约（存储在 Azure Cosmos DB 容器中） | 继续标记（存储在内存中或手动保留） |
| 能够重播过去的更改 | 是，具有推送模型 | 是，具有提取模型|
| 轮询将来的更改 | 基于用户指定的自动检查更改`WithPollInterval` | 手动 |
| 处理整个容器中的更改 | 是，并跨相同容器跨多个线程/计算机自动并行并行| 是，并使用 FeedTokens 手动并行化 |
| 仅处理单个分区键中的更改 | 不支持 | 是|
| 支持级别 | 正式发布 | 预览 |

## <a name="next-steps"></a>后续步骤

* [更改源概述](change-feed.md)
* [使用更改源处理器](change-feed-processor.md)
* [触发 Azure Functions](change-feed-functions.md)