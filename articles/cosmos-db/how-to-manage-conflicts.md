---
title: 在 Azure Cosmos DB 中管理区域之间的冲突
description: 了解如何在 Azure Cosmos DB 中通过创建“以最后写入者为准”或自定义冲突解决策略来管理冲突
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: mjbrown
ms.openlocfilehash: 8f109bef1c7ebb3ac77c58357ad3cb6064e8afb3
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82869955"
---
# <a name="manage-conflict-resolution-policies-in-azure-cosmos-db"></a>管理 Azure Cosmos DB 中的冲突解决策略

对于多区域写入，当多个客户端写入同一项时，可能会发生冲突。 发生冲突时，可以通过使用不同的冲突解决策略来解决冲突。 本文介绍如何管理冲突解决策略。

## <a name="create-a-last-writer-wins-conflict-resolution-policy"></a>创建“以最后写入者为准”冲突解决策略

这些示例介绍如何使用“以最后写入者为准”冲突解决策略设置一个容器。 “以最后写入者为准”的默认路径是时间戳字段或 `_ts` 属性。 对于 SQL API，也可以将其设置为数值类型的用户定义路径。 如果发生冲突，最高值优先。 如果路径未设置或无效，则它默认为 `_ts`。 使用此策略解决的冲突不会显示在冲突源中。 此策略可供所有 API 使用。

### <a name="net-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection lwwCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.lwwCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.LastWriterWins,
          ConflictResolutionPath = "/myCustomId",
      },
  });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.lwwCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.LastWriterWins,
            ResolutionPath = "/myCustomId",
        }
    });
```
---

### <a name="java-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-java"></a>Java SDK

# <a name="java-async-sdk"></a>[Java 异步 SDK](#tab/async)

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

# <a name="java-sync-sdk"></a>[Java 同步 SDK](#tab/sync)

```java
DocumentCollection lwwCollection = new DocumentCollection();
lwwCollection.setId(this.lwwCollectionName);
ConflictResolutionPolicy lwwPolicy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
lwwCollection.setConflictResolutionPolicy(lwwPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, lwwCollection);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const { container: lwwContainer } = await database.containers.createIfNotExists(
  {
    id: this.lwwContainerName,
    conflictResolutionPolicy: {
      mode: "LastWriterWins",
      conflictResolutionPath: "/myCustomId"
    }
  }
);
```

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-python"></a>Python SDK

```python
udp_collection = {
    'id': self.udp_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'LastWriterWins',
        'conflictResolutionPath': '/myCustomId'
    }
}
udp_collection = self.try_create_document_collection(
    create_client, database, udp_collection)
```

## <a name="create-a-custom-conflict-resolution-policy-using-a-stored-procedure"></a>使用存储过程创建自定义冲突解决策略

这些示例介绍如何使用自定义冲突解决策略设置一个容器，通过存储的过程来解决冲突。 这些冲突不会显示在冲突源中，除非存储过程中存在错误。 使用容器创建策略后，需要创建存储的过程。 下面的 .NET SDK 示例演示一个示例。 此策略仅在核心 (SQL) API 上受支持。

### <a name="sample-custom-conflict-resolution-stored-procedure"></a>自定义冲突解决存储过程示例

必须使用下面显示的函数签名实现自定义冲突解决存储过程。 函数名称不需要与使用容器注册存储过程时使用的名称匹配，但它确实可以简化命名。 下面介绍了此存储过程必须实现的参数。

- **incomingItem**：正在生成冲突的提交中要插入或更新的项。 对于删除操作为 null。
- **existingItem**：当前已提交的项。 此值在更新中为非 null，对于插入或删除是 null。
- **isTombstone**：布尔值，指示 incomingItem 是否与先前删除的项冲突。 如果为 true，existingItem 也为 null。
- **conflictingItems**：容器中所有项的已提交版本的数组，这些项与 ID 或任何其他唯一索引属性上的 incomingItem 冲突。

> [!IMPORTANT]
> 与任何存储过程一样，自定义冲突解决过程可以访问具有相同分区键的任何数据，并可以执行任何插入、更新或删除操作来解决冲突。

此存储过程示例通过从 `/myCustomId` 路径中选择最小值来解决冲突。

```javascript
function resolver(incomingItem, existingItem, isTombstone, conflictingItems) {
  var collection = getContext().getCollection();

  if (!incomingItem) {
      if (existingItem) {

          collection.deleteDocument(existingItem._self, {}, function (err, responseOptions) {
              if (err) throw err;
          });
      }
  } else if (isTombstone) {
      // delete always wins.
  } else {
      if (existingItem) {
          if (incomingItem.myCustomId > existingItem.myCustomId) {
              return; // existing item wins
          }
      }

      var i;
      for (i = 0; i < conflictingItems.length; i++) {
          if (incomingItem.myCustomId > conflictingItems[i].myCustomId) {
              return; // existing conflict item wins
          }
      }

      // incoming item wins - clear conflicts and replace existing with incoming.
      tryDelete(conflictingItems, incomingItem, existingItem);
  }

  function tryDelete(documents, incoming, existing) {
      if (documents.length > 0) {
          collection.deleteDocument(documents[0]._self, {}, function (err, responseOptions) {
              if (err) throw err;

              documents.shift();
              tryDelete(documents, incoming, existing);
          });
      } else if (existing) {
          collection.replaceDocument(existing._self, incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      } else {
          collection.createDocument(collection.getSelfLink(), incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      }
  }
}
```

### <a name="net-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection udpCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.udpCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
          ConflictResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver"),
      },
  });

//Create the stored procedure
await clients[0].CreateStoredProcedureAsync(
UriFactory.CreateStoredProcedureUri(this.databaseName, this.udpCollectionName, "resolver"), new StoredProcedure
{
    Id = "resolver",
    Body = File.ReadAllText(@"resolver.js")
});
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.udpCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.Custom,
            ResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver")
        }
    });

await container.Scripts.CreateStoredProcedureAsync(
    new StoredProcedureProperties("resolver", File.ReadAllText(@"resolver.js"))
);
```
---

### <a name="java-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-java"></a>Java SDK

# <a name="java-async-sdk"></a>[Java 异步 SDK](#tab/async)

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy("resolver");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

创建容器后，必须创建 `resolver` 存储过程。

# <a name="java-sync-sdk"></a>[Java 同步 SDK](#tab/sync)

```java
DocumentCollection udpCollection = new DocumentCollection();
udpCollection.setId(this.udpCollectionName);
ConflictResolutionPolicy udpPolicy = ConflictResolutionPolicy.createCustomPolicy(
        String.format("dbs/%s/colls/%s/sprocs/%s", this.databaseName, this.udpCollectionName, "resolver"));
udpCollection.setConflictResolutionPolicy(udpPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, udpCollection);
```
---

创建容器后，必须创建 `resolver` 存储过程。

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const { container: udpContainer } = await database.containers.createIfNotExists(
  {
    id: this.udpContainerName,
    conflictResolutionPolicy: {
      mode: "Custom",
      conflictResolutionProcedure: `dbs/${this.databaseName}/colls/${
        this.udpContainerName
      }/sprocs/resolver`
    }
  }
);
```

创建容器后，必须创建 `resolver` 存储过程。

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-python"></a>Python SDK

```python
udp_collection = {
    'id': self.udp_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'Custom',
        'conflictResolutionProcedure': 'dbs/' + self.database_name + "/colls/" + self.udp_collection_name + '/sprocs/resolver'
    }
}
udp_collection = self.try_create_document_collection(
    create_client, database, udp_collection)
```

创建容器后，必须创建 `resolver` 存储过程。

## <a name="create-a-custom-conflict-resolution-policy"></a>创建自定义冲突解决策略

这些示例介绍如何使用自定义冲突解决策略设置一个容器。 这些冲突会显示在冲突源中。

### <a name="net-sdk"></a><a id="create-custom-conflict-resolution-policy-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection manualCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.manualCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
      },
  });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.manualCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.Custom
        }
    });
```
---

### <a name="java-sdk"></a><a id="create-custom-conflict-resolution-policy-java"></a>Java SDK

# <a name="java-async-sdk"></a>[Java 异步 SDK](#tab/async)

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy();
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

# <a name="java-sync-sdk"></a>[Java 同步 SDK](#tab/sync)

```java
DocumentCollection manualCollection = new DocumentCollection();
manualCollection.setId(this.manualCollectionName);
ConflictResolutionPolicy customPolicy = ConflictResolutionPolicy.createCustomPolicy(null);
manualCollection.setConflictResolutionPolicy(customPolicy);
DocumentCollection createdCollection = client.createCollection(database.getSelfLink(), collection, null).getResource();
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const {
  container: manualContainer
} = await database.containers.createIfNotExists({
  id: this.manualContainerName,
  conflictResolutionPolicy: {
    mode: "Custom"
  }
});
```

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-python"></a>Python SDK

```python
database = client.ReadDatabase("dbs/" + self.database_name)
manual_collection = {
    'id': self.manual_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'Custom'
    }
}
manual_collection = client.CreateContainer(database['_self'], collection)
```

## <a name="read-from-conflict-feed"></a>从冲突源读取

这些示例介绍如何从容器的冲突源读取。 只有在未自动解决冲突或使用自定义冲突策略的情况下，冲突才显示在冲突源中。

### <a name="net-sdk"></a><a id="read-from-conflict-feed-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
FeedResponse<Conflict> conflicts = await delClient.ReadConflictFeedAsync(this.collectionUri);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
FeedIterator<ConflictProperties> conflictFeed = container.Conflicts.GetConflictQueryIterator();
while (conflictFeed.HasMoreResults)
{
    FeedResponse<ConflictProperties> conflicts = await conflictFeed.ReadNextAsync();
    foreach (ConflictProperties conflict in conflicts)
    {
        // Read the conflicted content
        MyClass intendedChanges = container.Conflicts.ReadConflictContent<MyClass>(conflict);
        MyClass currentState = await container.Conflicts.ReadCurrentAsync<MyClass>(conflict, new PartitionKey(intendedChanges.MyPartitionKey));

        // Do manual merge among documents
        await container.ReplaceItemAsync<MyClass>(intendedChanges, intendedChanges.Id, new PartitionKey(intendedChanges.MyPartitionKey));

        // Delete the conflict
        await container.Conflicts.DeleteAsync(conflict, new PartitionKey(intendedChanges.MyPartitionKey));
    }
}
```
---

### <a name="java-sdk"></a><a id="read-from-conflict-feed-java"></a>Java SDK

# <a name="java-async-sdk"></a>[Java 异步 SDK](#tab/async)

```java
FeedResponse<Conflict> response = client.readConflicts(this.manualCollectionUri, null)
                    .first().toBlocking().single();
for (Conflict conflict : response.getResults()) {
    /* Do something with conflict */
}
```
# <a name="java-async-sdk"></a>[Java 异步 SDK](#tab/sync)

```java
Iterator<Conflict> conflictsIterator = client.readConflicts(this.collectionLink, null).getQueryIterator();
while (conflictsIterator.hasNext()) {
    Conflict conflict = conflictsIterator.next();
    /* Do something with conflict */
}
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="read-from-conflict-feed-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const container = client
  .database(this.databaseName)
  .container(this.lwwContainerName);

const { result: conflicts } = await container.conflicts.readAll().toArray();
```

### <a name="python"></a><a id="read-from-conflict-feed-python"></a>Python

```python
conflicts_iterator = iter(client.ReadConflicts(self.manual_collection_link))
conflict = next(conflicts_iterator, None)
while conflict:
    # Do something with conflict
    conflict = next(conflicts_iterator, None)
```

## <a name="next-steps"></a>后续步骤

了解以下 Azure Cosmos DB 概念：

- [全球分布 - 揭秘](global-dist-under-the-hood.md)
- [如何在应用程序中配置多主数据库](how-to-multi-master.md)
- [配置多宿主客户端](how-to-manage-database-account.md#configure-multiple-write-regions)
- [在 Azure Cosmos DB 帐户中添加或删除区域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
- [如何在应用程序中配置多主机](how-to-multi-master.md)。
- [分区和数据分布](partition-data.md)
- [Azure Cosmos DB 中的索引](indexing-policies.md)
