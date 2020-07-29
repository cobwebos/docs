---
title: Azure Cosmos DB 找不到异常的疑难解答
description: 如何诊断和修复未找到的异常
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f391772672904a1e2bdfa0a741d9f85a6edeea8b
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294039"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found"></a>找不到 Azure Cosmos DB 的诊断和故障排除
HTTP 状态代码404表示资源不再存在。

## <a name="expected-behavior"></a>预期行为
存在许多有效方案，其中应用程序应为404，并正确处理方案。

## <a name="not-found-was-returned-for-an-item-that-should-exist-or-does-exist"></a>对于应存在或存在的项，返回了 "找不到"
下面是在项应该或退出时返回状态代码404的可能原因。

### <a name="1-race-condition"></a>1. 争用条件
存在多个 SDK 客户端实例，在写入之前会发生读取。

#### <a name="solution"></a>解决方案：
1. Cosmos DB 的默认帐户一致性是会话一致性。 创建或更新项时，响应将返回一个会话令牌，该令牌可在 SDK 实例之间传递，以保证读取请求从具有该更改的副本中读取。
2. 将[一致性级别](consistency-levels-choosing.md)更改为[更高级别](consistency-levels-tradeoffs.md)

### <a name="2-invalid-partition-key-and-id-combination"></a>2. 分区键和 ID 组合无效
分区键和 ID 组合无效。

#### <a name="solution"></a>解决方案：
修复导致错误组合的应用程序逻辑。 

### <a name="3-invalid-character-in-item-id"></a>3. 项 ID 中的字符无效
项将插入到 Cosmos DB 中，其中的项 ID 包含[无效字符](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks)。

#### <a name="solution"></a>解决方案：
建议用户将 ID 更改为不包含特殊字符的其他值。 如果更改 ID 不是一个选项，则可以对 ID 进行 Base64 编码，以对特殊字符进行转义。

已在容器中插入的项可以使用 RID 值（而不是基于名称的引用）替换该 ID。
```c#
// Get a container reference that use RID values
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// List of invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // It recommend to chose a new ID that does not contain special characters, but
        // if that is not possible then it can be Base64 encoded to escape the special characters
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes);

        // Update the item with the new ID value using the rid based container reference
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validate the new ID can be read using the original name based contianer reference
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="4-time-to-live-ttl-purge"></a>4. 生存时间（TTL）清除
该项的[生存时间（TTL）](https://docs.microsoft.com/azure/cosmos-db/time-to-live)属性集已设置。 由于生存时间已过，因此清除了该项。

#### <a name="solution"></a>解决方案：
更改 "生存时间" 以防止项目被清除。

### <a name="5-lazy-indexing"></a>5. 延迟索引
尚未捕获[延迟索引](index-policy.md#indexing-mode)。

#### <a name="solution"></a>解决方案：
等待索引捕获或更改索引策略

### <a name="6-parent-resource-deleted"></a>6. 已删除父资源
项所在的数据库和/或容器已被删除。

#### <a name="solution"></a>解决方案：
1. [还原](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore#backup-retention-period)父资源或重新创建资源。
2. 创建新资源来替换已删除的资源

## <a name="next-steps"></a>后续步骤
* 使用 Azure Cosmos DB .NET SDK 时[诊断和解决](troubleshoot-dot-net-sdk.md)问题
* 了解[.Net V3](performance-tips-dotnet-sdk-v3-sql.md)和[.net V2](performance-tips.md)的性能准则