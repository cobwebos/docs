---
title: 更改源请求模型
description: 了解如何使用 Azure Cosmos DB 更改源拉取模型来读取更改源，并了解拉取模型与更改源处理器之间的差异
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/09/2020
ms.reviewer: sngun
ms.openlocfilehash: b056c12f51c6e36a806f2bba0f5efe9ea9498798
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90015630"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Azure Cosmos DB 中的更改源拉取模型

使用更改源拉取模型，你可以按自己的节奏使用 Azure Cosmos DB 更改源。 正如你使用[更改源处理器](change-feed-processor.md)所做的那样，你可以使用更改源拉取模型来并行处理多个更改源使用者之间的更改。

> [!NOTE]
> 更改源拉取模型当前[仅在 Azure Cosmos DB .NET SDK 中提供了预览版](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.13.0-preview)。 该预览版尚不可用于其他 SDK 版本。

## <a name="comparing-with-change-feed-processor"></a>与更改源处理器进行比较

许多情况下，既可以使用[更改源处理器](change-feed-processor.md)又可以使用拉取模型来处理更改源。 拉取模型的继续标记和更改源处理器的租约容器都是更改源中最后处理的项（或一批项）的“书签”。

但是，不能将继续标记转换为租赁容器（反之亦然）。

> [!NOTE]
> 在大多数情况下，如果需要从更改源中读取数据，最简单的方法是使用 [更改源处理器](change-feed-processor.md)。

以下情况应考虑使用拉取模型：

- 读取特定分区键的更改
- 控制客户端接收更改以进行处理的速度
- 对更改源中的现有数据执行一次性读取 (例如，执行数据迁移) 

下面是更改源处理器与拉取模型之间的一些主要差异：

|功能  | 更改源处理器| 拉取模型 |
| --- | --- | --- |
| 在处理更改源时跟踪当前位置 | 租赁（存储在 Azure Cosmos DB 容器中） | 继续标记（存储在内存中或手动进行保存） |
| 能够重播过去的更改 | 是（在使用推送模型的情况下） | 是（在使用拉取模型的情况下）|
| 轮询将来的更改 | 基于用户指定的 `WithPollInterval` 自动检查更改 | 手动 |
| 处理整个容器中的更改 | 是的，自动并行处理从同一容器使用更改的多个线程/机器| 是，使用 FeedToken 手动并行化 |
| 仅处理单个分区键的更改 | 不支持 | 是|
| 支持级别 | 正式发布 | 预览 |

## <a name="consuming-an-entire-containers-changes"></a>使用整个容器的更改

你可以创建一个 `FeedIterator` 来使用拉取模型处理更改源。 最初创建时 `FeedIterator` ，必须指定一个必需的值， `ChangeFeedStartFrom` 该值由读取更改的起始位置和所需的值组成 `FeedRange` 。 `FeedRange`是一系列分区键值，并指定将使用特定的更改源读取的项 `FeedIterator` 。

您可以选择指定 `ChangeFeedRequestOptions` 以设置 `PageSizeHint` 。 `PageSizeHint`是将在单个页面中返回的最大项数。

`FeedIterator` 有两种形式。 除了下述可返回实体对象的示例之外，还可以获取提供 `Stream` 支持的响应。 利用流，你可以在不先将数据反序列化的情况下读取数据，从而节省客户端资源。

下面的示例用于获取一个可返回实体对象（在本例中为 `User` 对象）的 `FeedIterator`：

```csharp
FeedIterator<User> InteratorWithPOCOS = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning());
```

下面的示例用于获取一个可返回 `Stream` 的 `FeedIterator`：

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Beginning());
```

如果没有提供 `FeedRange` `FeedIterator` ，则可以按自己的节奏处理整个容器的更改源。 下面的示例将从当前时间开始读取所有更改：

```csharp
FeedIterator iteratorForTheEntireContainer = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Now());

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

在某些情况下，你可能只需要处理特定分区键的更改。 可以获取特定分区键的 `FeedIterator`，并采用处理整个容器的方式来处理更改。

```csharp
FeedIterator<User> iteratorForPartitionKey = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(FeedRange.FromPartitionKey(new PartitionKey("PartitionKeyValue"))));

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

然后可以使用 `FeedRange` 创建一个 `FeedIterator`，以便跨多个计算机或线程并行处理更改源。 与上面演示如何获取 `FeedIterator` 整个容器或单个分区键的的示例不同，可以使用 FeedRanges 获取多个 FeedIterators，它可以并行处理更改源。

若要使用 FeedRange，需要通过一个业务流程协调程序进程来获取 FeedRange 并将其分发到那些计算机。 该分发可能存在以下情况：

* 使用 `FeedRange.ToJsonString` 并分发此字符串值。 使用者可以将此值用于 `FeedRange.FromJsonString`
* 如果分发正在进行，则传递 `FeedRange` 对象引用。

下面的示例展示了如何使用两个并行读取的单独的虚构计算机从容器的更改源开头进行读取：

计算机 1：

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[0]));
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
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[1]));
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
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning());

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
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.ContinuationToken(continuation));
```

只要 Cosmos 容器仍然存在，FeedIterator 的继续标记将永不过期。

## <a name="next-steps"></a>后续步骤

* [更改源概述](change-feed.md)
* [使用更改源处理器](change-feed-processor.md)
* [触发 Azure Functions](change-feed-functions.md)