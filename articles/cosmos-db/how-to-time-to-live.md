---
title: 在 Azure Cosmos DB 中配置和管理生存时间
description: 了解如何在 Azure Cosmos DB 中配置和管理容器和项的生存时间
author: anfeldma-ms
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: anfeldma
ms.openlocfilehash: 11f5615d44cef4b6717dc9fe2004a64cf2f800ba
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124119"
---
# <a name="configure-time-to-live-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中配置生存时间

在 Azure Cosmos DB 中，可以选择在容器级别配置生存时间 (TTL)，也可以在为容器完成设置之后，在项级别重写生存时间。 可以通过 Azure 门户或特定于语言的 SDK 配置容器的 TTL。 可以通过 SDK 配置项级别 TTL 重写。

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>使用 Azure 门户在容器上启用生存时间

通过以下步骤在没有到期时间的容器上启用生存时间。 启用此项即可在项级别重写 TTL。 也可通过输入非零值（代表秒）来设置 TTL。

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 创建新的 Azure Cosmos 帐户或选择现有的帐户。

3. 打开“数据资源管理器”窗格  。

4. 选择一个现有的容器，将其展开并修改以下值：

   * 打开“规模和设置”窗口。 
   * 在“设置”下找到“生存时间”。  
   * 选择“启用(无默认值)”或选择“启用”，然后设置一个 TTL 值  
   * 单击“保存”  以保存更改。

   ![在 Azure 门户中配置生存时间](./media/how-to-time-to-live/how-to-time-to-live-portal.png)

* 当 DefaultTimeToLive 为 null 时，生存时间为“关”
* 当 DefaultTimeToLive 为 -1 时，“生存时间”设置为“开”（无默认值）
* 当 DefaultTimeToLive 具有任何其他整数值（0 除外）时，“生存时间”设置为“开”

## <a name="enable-time-to-live-on-a-container-using-azure-cli-or-powershell"></a>使用 Azure CLI 或 PowerShell 在容器上启用生存时间

若要在容器上创建或启用 TTL，请参阅

* [使用 Azure CLI 创建具有 TTL 的容器](manage-with-cli.md#create-a-container-with-ttl)
* [使用 PowerShell 创建具有 TTL 的容器](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>使用 SDK 在容器上启用生存时间

### <a name="net-sdk"></a><a id="dotnet-enable-noexpiry"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// Create a new container with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// Create a new container with TTL enabled and without any expiration value
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = -1 //(never expire by default)
});
```
---

### <a name="java-sdk"></a><a id="java-enable-noexpiry"></a>Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4 （Maven：： azure-cosmos）

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(-1);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 （Maven：： azure-cosmos）

```java
CosmosContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(-1);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

## <a name="set-time-to-live-on-a-container-using-sdk"></a>使用 SDK 在容器上设置生存时间

若要在容器上设置生存时间，需提供一个非零正数来指示时间段（以秒为单位）。 在项的上次修改的时间戳 (`_ts`) 过后，将会删除容器中的所有值，具体取决于配置的 TTL 值。

### <a name="net-sdk"></a><a id="dotnet-enable-withexpiry"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24 // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition;
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
});
```
---

### <a name="java-sdk"></a><a id="java-enable-defaultexpiry"></a>Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4 （Maven：： azure-cosmos）

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 （Maven：： azure-cosmos）

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

### <a name="nodejs-sdk"></a><a id="nodejs-enable-withexpiry"></a>NodeJS SDK

```javascript
const containerDefinition = {
          id: "sample container1",
        };

async function createcontainerWithTTL(db: Database, containerDefinition: ContainerDefinition, collId: any, defaultTtl: number) {
      containerDefinition.id = collId;
      containerDefinition.defaultTtl = defaultTtl;
      await db.containers.create(containerDefinition);
}
```

## <a name="set-time-to-live-on-an-item"></a>在某个项上设置生存时间

除了在容器上设置默认的生存时间，还可以为项设置生存时间。 在项级别设置生存时间将重写该容器中项的默认 TTL。

* 若要在项上设置 TTL，则需要提供一个非零正数，该数字表示自项上次的修改时间戳 (`_ts`) 之后，项过期所经过的时间段（以秒为单位）。

* 如果项没有 TTL 字段，则默认情况下，设置到容器的 TTL 将应用到项。

* 如果在容器级别禁用了 TTL，在项上的 TTL 字段会被忽略，直到在容器上再次启用 TTL。

### <a name="azure-portal"></a><a id="portal-set-ttl-item"></a>Azure 门户

使用以下步骤在项上启用生存时间：

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 创建新的 Azure Cosmos 帐户或选择现有的帐户。

3. 打开“数据资源管理器”窗格****。

4. 选择一个现有的容器，将其展开并修改以下值：

   * 打开“规模和设置”窗口。****
   * 在“设置”下找到“生存时间”。********
   * 选择 **"开" （无默认值）** 或选择 **"** 开" 并设置 TTL 值。 
   * 单击 **“确定”**，保存这些更改。

5. 接下来，导航到要为其设置生存时间的项，添加 `ttl` 属性并选择“更新”****。 

   ```json
   {
    "id": "1",
    "_rid": "Jic9ANWdO-EFAAAAAAAAAA==",
    "_self": "dbs/Jic9AA==/colls/Jic9ANWdO-E=/docs/Jic9ANWdO-EFAAAAAAAAAA==/",
    "_etag": "\"0d00b23f-0000-0000-0000-5c7712e80000\"",
    "_attachments": "attachments/",
    "ttl": 10,
    "_ts": 1551307496
   }
   ```

### <a name="net-sdk-any"></a><a id="dotnet-set-ttl-item"></a>.NET SDK（任何）

```csharp
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }
    [JsonProperty(PropertyName="cid")]
    public string CustomerId { get; set; }
    // used to set expiration policy
    [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
    public int? ttl { get; set; }

    //...
}
// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder
{
    Id = "SO05",
    CustomerId = "CO18009186470",
    ttl = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days
};
```

### <a name="nodejs-sdk"></a><a id="nodejs-set-ttl-item"></a>NodeJS SDK

```javascript
const itemDefinition = {
          id: "doc",
          name: "sample Item",
          key: "value",
          ttl: 2
        };
```

### <a name="java-sdk"></a><a id="java-set-ttl-item"></a>Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4 （Maven：： azure-cosmos）

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String getId() {return this.id;}
    public void setId(String new_id) {this.id = new_id;}
    public String getCustomerId() {return this.customerId;}
    public void setCustomerId(String new_cid) {this.customerId = new_cid;}
    public Integer getTtl() {return this.ttl;}
    public void setTtl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);

```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 （Maven：： azure-cosmos）

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public void id(String new_id) {this.id = new_id;}
    public String customerId() {return this.customerId;}
    public void customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public void ttl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);

```
---

## <a name="reset-time-to-live"></a>重置生存时间

可以通过在项上执行写入或更新操作，重置项的生存时间。 写入或更新操作会将 `_ts` 设置为当前时间，要到期的项的 TTL 就会重启。 若要更改项的 TTL，可以更新相关字段，就像更新任何其他字段那样。

### <a name="net-sdk"></a><a id="dotnet-extend-ttl-item"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = 60 * 30 * 30; // update time to live
response = await client.ReplaceDocumentAsync(readDocument);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = 60 * 30 * 30; // update time to live
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```
---

### <a name="java-sdk"></a><a id="java-enable-modifyitemexpiry"></a>Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4 （Maven：： azure-cosmos）

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
CosmosAsyncItemResponse<SalesOrder> itemResponse = container.readItem("SO05", new PartitionKey("CO18009186470"), SalesOrder.class)
        .flatMap(readResponse -> {
            SalesOrder salesOrder = readResponse.getItem();
            salesOrder.setTtl(60 * 30 * 30);
            return container.createItem(salesOrder);
}).block();
```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

SDK V3 （Maven：： azure-cosmos）

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
container.getItem("SO05", new PartitionKey("CO18009186470")).read()
        .flatMap(readResponse -> {
            SalesOrder salesOrder = null;
            try {
                salesOrder = readResponse.properties().getObject(SalesOrder.class);
            } catch (Exception err) {

            }
            salesOrder.ttl(60 * 30 * 30);
            return container.createItem(salesOrder);
}).block();
```
---

## <a name="turn-off-time-to-live"></a>禁用生存时间

如果已在项上设置生存时间，并且不再想要该项过期，则可以获取该项，删除 TTL 字段并替换服务器上的项。 从项中删除 TTL 字段时，分配给容器的默认 TTL 值会应用到项。 将 TTL 值设置为 -1 可以防止项过期，并且不会从容器继承 TTL 值。

### <a name="net-sdk"></a><a id="dotnet-turn-off-ttl-item"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = null; // inherit the default TTL of the container

response = await client.ReplaceDocumentAsync(readDocument);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = null; // inherit the default TTL of the container
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```
---

### <a name="java-sdk"></a><a id="java-enable-itemdefaultexpiry"></a>Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4 （Maven：： azure-cosmos）

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
CosmosAsyncItemResponse<SalesOrder> itemResponse = container.readItem("SO05", new PartitionKey("CO18009186470"), SalesOrder.class)
        .flatMap(readResponse -> {
            SalesOrder salesOrder = readResponse.getItem();
            salesOrder.setTtl(null);
            return container.createItem(salesOrder);
}).block();
```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 （Maven：： azure-cosmos）

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
container.getItem("SO05", new PartitionKey("CO18009186470")).read()
        .flatMap(readResponse -> {
            SalesOrder salesOrder = null;
            try {
                salesOrder = readResponse.properties().getObject(SalesOrder.class);
            } catch (Exception err) {

            }
            salesOrder.ttl(null);
            return container.createItem(salesOrder);
}).block();
```
---

## <a name="disable-time-to-live"></a>禁用生存时间

若要在容器上禁用生存时间并阻止后台进程查找过期项，则应删除容器上的 `DefaultTimeToLive` 属性。 删除此属性不同于将其设置为 -1。 将它设置为 -1 时，添加到容器的新项将永久生存。但是，可以在容器中的特定项上重写此值。 从容器中删除 TTL 属性后项将永不会过期，即使这些项已显式重写了以前的默认 TTL 值。

### <a name="net-sdk"></a><a id="dotnet-disable-ttl"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// Get the container, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// Get the container, update DefaultTimeToLive to null
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Disable TTL
containerResponse.Resource.DefaultTimeToLive = null;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```
---

### <a name="java-sdk"></a><a id="java-enable-disableexpiry"></a>Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4 （Maven：： azure-cosmos）

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
// Disable TTL
containerProperties.setDefaultTimeToLiveInSeconds(null);
// Update container settings
container.replace(containerProperties).block();
```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 （Maven：： azure-cosmos）

```java
CosmosContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
// Disable TTL
containerProperties.defaultTimeToLive(null);
// Update container settings
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

## <a name="next-steps"></a>后续步骤

在以下文章中详细了解生存时间：

* [生存时间](time-to-live.md)
