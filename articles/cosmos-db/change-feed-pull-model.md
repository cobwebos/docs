---
title: 更改源请求模型
description: 了解如何使用 Azure Cosmos DB 更改源拉取模型来读取更改源，并了解拉取模型与更改源处理器之间的差异
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/10/2020
ms.reviewer: sngun
ms.openlocfilehash: 0e6e243ceb73ca2a1180e59ba6c6b4095ed6069a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116707"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Azure Cosmos DB 中的更改源拉取模型

使用更改源拉取模型，你可以按自己的节奏使用 Azure Cosmos DB 更改源。 正如你使用[更改源处理器](change-feed-processor.md)所做的那样，你可以使用更改源拉取模型来并行处理多个更改源使用者之间的更改。

> [!NOTE]
> 更改源拉取模型当前[仅在 Azure Cosmos DB .NET SDK 中提供了预览版](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview)。 该预览版尚不可用于其他 SDK 版本。

## <a name="consuming-an-entire-containers-changes"></a>使用整个容器的更改

你可以创建一个 `FeedIterator` 来使用拉取模型处理更改源。 最初创建 `FeedIterator` 时，可以在 `ChangeFeedRequestOptions` 中指定一个可选 `StartTime`。 如果未指定，则 `StartTime` 将是当前时间。

`FeedIterator` 有两种形式。 除了下述可返回实体对象的示例之外，还可以获取提供 `Stream` 支持的响应。 利用流，你可以在不先将数据反序列化的情况下读取数据，从而节省客户端资源。

下面的示例用于获取一个可返回实体对象（在本例中为 `User` 对象）的 `FeedIterator`：

```csharp
FeedIterator<User> iteratorWithPOCOS = container.GetChangeFeedIterator<User>();
```

下面的示例用于获取一个可返回 `Stream` 的 `FeedIterator`：

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator();
```

使用 `FeedIterator`，可以轻松地按自己的节奏处理整个容器的更改源。 下面是一个示例：

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

在某些情况下，你可能只需要处理特定分区键的更改。 可以获取特定分区键的 `FeedIterator`，并采用处理整个容器的方式来处理更改：

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

## <a name="using-feedrange-for-parallelization"></a>使用 FeedRange 实现并行化

在[更改源处理器](change-feed-processor.md)中，工作自动分布到多个使用者。 在更改源拉取模型中，可以使用 `FeedRange` 来并行处理更改源。 `FeedRange` 表示分区键值的一个范围。

下面的示例展示了如何获取容器的范围列表：

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

获取容器的 FeedRange 列表时，每个[物理分区](partition-data.md#physical-partitions)你都会获得一个 `FeedRange`。

然后可以使用 `FeedRange` 创建一个 `FeedIterator`，以便跨多个计算机或线程并行处理更改源。 与上面展示了如何获取整个容器的单个 `FeedIterator` 的示例不同，你可以使用 `FeedRange` 获取多个 FeedIterator，以便并行处理更改源。

若要使用 FeedRange，需要通过一个业务流程协调程序进程来获取 FeedRange 并将其分发到那些计算机。 该分发可能存在以下情况：

* 使用 `FeedRange.ToJsonString` 并分发此字符串值。 使用者可以将此值用于 `FeedRange.FromJsonString`
* 如果分发正在进行，则传递 `FeedRange` 对象引用。

下面的示例展示了如何使用两个并行读取的单独的虚构计算机从容器的更改源开头进行读取：

计算机 1：

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

计算机 2：

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

可以通过创建一个继续标记来保存 `FeedIterator` 的位置。 继续标记是一个字符串值，用于跟踪 FeedIterator 上次处理的更改。 这允许 `FeedIterator` 稍后在此位置继续。 以下代码将读取更改源中自容器创建以来的内容。 当没有更多更改可用时，它会保留一个继续标记，以便以后可以继续使用更改源。

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

许多情况下，既可以使用[更改源处理器](change-feed-processor.md)又可以使用拉取模型来处理更改源。 拉取模型的继续标记和更改源处理器的租约容器都是更改源中最后处理的项（或一批项）的“书签”。
但是，不能将继续标记转换为租赁容器（反之亦然）。

以下情况应考虑使用拉取模型：

- 希望一次性读取更改源中的现有数据
- 仅希望读取特定分区键的更改
- 不希望使用推送模型，而是想要按自己的节奏使用更改源

下面是更改源处理器与拉取模型之间的一些主要差异：

|  | 更改源处理器| 拉取模型 |
| --- | --- | --- |
| 在处理更改源时跟踪当前位置 | 租赁（存储在 Azure Cosmos DB 容器中） | 继续标记（存储在内存中或手动进行保存） |
| 能够重播过去的更改 | 是（在使用推送模型的情况下） | 是（在使用拉取模型的情况下）|
| 轮询将来的更改 | 基于用户指定的 `WithPollInterval` 自动检查更改 | 手动 |
| 处理整个容器中的更改 | 是的，自动并行处理从同一容器使用更改的多个线程/机器| 是，使用 FeedToken 手动并行化 |
| 仅处理单个分区键的更改 | 不支持 | 是|
| 支持级别 | 正式发布 | 预览 |

## <a name="next-steps"></a>后续步骤

* [更改源概述](change-feed.md)
* [使用更改源处理器](change-feed-processor.md)
* [触发 Azure Functions](change-feed-functions.md)