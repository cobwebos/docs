---
title: Azure Cosmos DB 找不到异常的疑难解答
description: 了解如何诊断和修复 "未找到" 异常。
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 1d778b4330389d23b0fe7179a005abfbd7d66d5c
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871099"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found-exceptions"></a>诊断并解决 Azure Cosmos DB 找不到异常
HTTP 状态代码 404 表示资源不再存在。

## <a name="expected-behavior"></a>预期行为
在许多有效方案中，应用程序需要代码404并正确处理方案。

## <a name="a-not-found-exception-was-returned-for-an-item-that-should-exist-or-does-exist"></a>对于应存在或存在的项，返回了 "找不到" 异常
如果项应存在或存在，则可能会返回状态代码404的可能原因。

### <a name="race-condition"></a>争用条件
有多个 SDK 客户端实例且读取在写入之前发生。

#### <a name="solution"></a>解决方案：
1. Azure Cosmos DB 的默认帐户一致性是会话一致性。 创建或更新项时，响应将返回一个会话令牌，该令牌可在 SDK 实例之间传递，以保证读取请求从具有该更改的副本中读取。
1. 将 [一致性级别](consistency-levels-choosing.md) 更改为 [更高的级别](consistency-levels-tradeoffs.md)。

### <a name="invalid-partition-key-and-id-combination"></a>分区键和 ID 组合无效
分区键和 ID 组合无效。

#### <a name="solution"></a>解决方案：
修复导致错误组合的应用程序逻辑。 

### <a name="invalid-character-in-an-item-id"></a>项 ID 中有无效字符
项将插入到 Azure Cosmos DB 中，其中的项 ID 包含 [无效字符](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks) 。

#### <a name="solution"></a>解决方案：
将 ID 更改为不包含特殊字符的其他值。 如果更改 ID 不是一个选项，则可以对 ID 进行 Base64 编码，以对特殊字符进行转义。

可以使用 RID 值而不是基于名称的引用来替换已插入到 ID 的容器中的项。
```c#
// Get a container reference that uses RID values.
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// Invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // Choose a new ID that doesn't contain special characters.
        // If that isn't possible, then Base64 encode the ID to escape the special characters.
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes);

        // Update the item with the new ID value by using the RID-based container reference.
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validating the new ID can be read by using the original name-based container reference.
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="time-to-live-purge"></a>生存时间清除
该项具有 [ (TTL) ](https://docs.microsoft.com/azure/cosmos-db/time-to-live) 属性集的生存时间。 由于 TTL 属性已过期，因此清除了该项。

#### <a name="solution"></a>解决方案：
更改 TTL 属性以防止清除该项。

### <a name="lazy-indexing"></a>惰性索引编制
尚未捕获 [延迟索引](index-policy.md#indexing-mode) 。

#### <a name="solution"></a>解决方案：
等待索引捕获或更改索引策略。

### <a name="parent-resource-deleted"></a>父资源已删除
项所在的数据库或容器已被删除。

#### <a name="solution"></a>解决方案：
1. 请[还原](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore#backup-retention-period)父资源或重新创建资源。
1. 创建新资源来替换已删除的资源。

## <a name="next-steps"></a>后续步骤
* [诊断并解决](troubleshoot-dot-net-sdk.md) 使用 AZURE COSMOS DB .net SDK 时遇到的问题。
* 了解 [.net v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.net v2](performance-tips.md)的性能准则。