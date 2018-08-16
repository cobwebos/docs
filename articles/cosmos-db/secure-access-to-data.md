---
title: 了解如何保护对 Azure Cosmos DB 中数据的访问 | Microsoft Docs
description: 了解有关 Azure Cosmos DB 中的访问控制概念，包括主密钥、只读密钥、用户和权限。
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2017
ms.author: rafats
ms.openlocfilehash: a8ea53b8f9b705078ac4ab56faeb60b8ba51e72e
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038088"
---
# <a name="securing-access-to-azure-cosmos-db-data"></a>保护对 Azure Cosmos DB 数据的访问
本文概述了如何保护对 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 中存储的数据的访问。

Azure Cosmos DB 使用两种类型的密钥来验证用户身份并提供对其数据和资源的访问权限。 

|密钥类型|资源|
|---|---|
|[主密钥](#master-keys) |用于管理资源：数据库帐户、数据库、用户和权限|
|[资源令牌](#resource-tokens)|用于应用程序资源：容器、文档、附件、存储过程、触发器和 UDF|

<a id="master-keys"></a>

## <a name="master-keys"></a>主密钥 

主密钥提供对数据库帐户中所有管理资源的访问权限。 主密钥：  
- 提供对帐户、数据库、用户和权限的访问权限。 
- 无法用于提供对容器和文档的精细访问权限。
- 在创建帐户过程中创建。
- 随时可重新生成。

每个帐户包括两个主密钥：主要密钥和辅助密钥。 使用两个密钥的目的是为了能够重新生成或轮换密钥，从而可以持续访问帐户和数据。 

Cosmos DB 帐户除了有两个主密钥以外，还有两个只读密钥。 这些只读密钥只允许针对帐户执行读取操作。 只读密钥不提供对资源的读取权限。

可以使用 Azure 门户检索和重新生成主要、辅助、只读和读写主密钥。 有关说明，请参阅[查看、复制和重新生成访问密钥](manage-account.md#keys)。

![Azure 门户中的访问控制 (IAM) - 演示 NoSQL 数据库安全性](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

轮换主密钥的过程相当简单。 导航到 Azure 门户并检索辅助密钥，在应用程序中将主要密钥替换为该辅助密钥，然后在 Azure 门户中轮换主要密钥即可。

![Azure 门户中的主密钥轮换 - 演示 NoSQL 数据库安全性](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>有关使用主密钥的代码示例

以下代码示例演示如何使用 Cosmos DB 帐户终结点和主密钥来实例化 DocumentClient 并创建数据库。 

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);

client = new DocumentClient(new Uri(endpointUrl), authorizationKey);

// Create Database
Database database = await client.CreateDatabaseAsync(
    new Database
    {
        Id = databaseName
    });
```

<a id="resource-tokens"></a>

## <a name="resource-tokens"></a>资源令牌

资源令牌提供对数据库中应用程序资源的访问权限。 资源令牌：
- 提供对特定容器、分区键、文档、附件、存储过程、触发器和 UDF 的访问权限。
- 向[用户](#users)授予对特定资源的[权限](#permissions)时创建。
- 通过 POST、GET 或 PUT 调用操作权限资源时重新创建。
- 使用专门针对用户、资源和权限构造的哈希资源令牌。
- 生存期受到可自定义的有效期的约束。 默认的有效期限为一小时。 但是，可将令牌生存期显式指定为最长五个小时。
- 可以安全替代主密钥。 
- 使客户端能够根据授予的权限读取、写入和删除 Cosmos DB 帐户中的资源。

如果想要为不能通过主密钥得到信任的客户端提供对 Cosmos DB 帐户中资源的访问权限，可以使用资源令牌（通过创建 Cosmos DB 用户和权限获取）。  

Cosmos DB 资源令牌提供一种安全的替代方案，使客户端能够根据授予的权限读取、写入和删除 Cosmos DB 帐户中的资源，而无需主密钥或只读密钥。

以下是典型的设计模式，通过它可以请求、生成资源令牌并将其提供给客户端：

1. 设置中间层服务，以用于移动应用程序共享用户照片。 
2. 中间层服务拥有 Cosmos DB 帐户的主密钥。
3. 照片应用安装在最终用户移动设备上。 
4. 登录时，照片应用使用中间层服务建立用户的标识。 这种标识建立机制完全由应用程序决定。
5. 一旦建立标识，中间层服务就会基于标识请求权限。
6. 中间层服务将资源令牌发送回手机应用。
7. 手机应用可以继续使用该资源令牌，以该资源令牌定义的权限，按照该资源令牌允许的间隔直接访问 Cosmos DB 资源。 
8. 资源令牌到期后，后续请求将收到 401 未经授权的异常。  此时，手机应用会重新建立标识，并请求新的资源令牌。

    ![Azure Cosmos DB 资源令牌工作流](./media/secure-access-to-data/resourcekeyworkflow.png)

资源令牌的生成和管理由本机 Cosmos DB 客户端库处理；但是，如果使用 REST，必须构造请求/身份验证标头。 有关为 REST 创建身份验证标头的详细信息，请参阅 [Cosmos DB 资源的访问控制](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources)或 [SDK 源代码](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js)。

有关用于生成或代理资源令牌的中间层服务的示例，请参阅 [ResourceTokenBroker 应用](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers)。

<a id="users"></a>

## <a name="users"></a>用户
Cosmos DB 用户与 Cosmos DB 数据库关联。  每个数据库可以包含零个或多个 Cosmos DB 用户。  以下代码示例演示如何创建 Cosmos DB 用户资源。

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> 每个 Cosmos DB 用户具有一个 PermissionsLink 属性，该属性可用于检索与该用户关联的[权限](#permissions)列表。
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>权限
Cosmos DB 权限资源与 Cosmos DB 用户关联。  每个用户可能包含零个或多个 Cosmos DB 权限。  权限资源提供对用户在尝试访问某个特定应用程序资源时需要的安全令牌的访问权限。
权限资源提供两种可用的访问级别：

* 所有：用户对资源拥有完全权限。
* 只读：用户只能读取资源的内容，但无法对资源执行写入、更新或删除操作。

> [!NOTE]
> 为了运行 Cosmos DB 存储过程，用户必须具有对会在其中运行存储过程的容器的所有权限。
> 
> 

### <a name="code-sample-to-create-permission"></a>有关创建权限的代码示例

以下代码示例演示如何创建权限资源、读取权限资源的资源令牌以及将权限与上面创建的[用户](#users)关联。

```csharp
// Create a permission.
Permission docPermission = new Permission
{
    PermissionMode = PermissionMode.Read,
    ResourceLink = UriFactory.CreateDocumentCollectionUri("db", "collection"),
    Id = "readperm"
};
  
docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission);
Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
```

如果为集合指定了分区键，则除 ResourceLink 以外，集合、文档和附件资源的权限，还必须包含 ResourcePartitionKey。

### <a name="code-sample-to-read-permissions-for-user"></a>有关读取用户权限的代码示例

为了方便地获取与特定用户关联的所有权限资源，Cosmos DB 使权限源对每个用户对象均可用。  下面的代码片段演示如何检索与上面创建的用户关联的权限、构造权限列表以及代表用户实例化新 DocumentClient。

```csharp
//Read a permission feed.
FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
  UriFactory.CreateUserUri("db", "myUser"));
 List<Permission> permList = new List<Permission>();

foreach (Permission perm in permFeed)
{
    permList.Add(perm);
}

DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);
```

## <a name="delete-or-export-user-data"></a>删除或导出用户数据
用户可使用 Azure Cosmos DB 搜索、选择、修改和删除数据库或集合中的任何个人数据。 Azure Cosmos DB 提供可用于查找和删除个人数据的 API，但用户应负责使用该 API 并定义擦除个人数据必需的逻辑。 每个多模型 API（SQL API、MongoDB API、Gremlin API、Cassandra API、表 API）都包含不同的语言 SDK，这些 SDK 提供了各种用于搜索和删除个人数据的方法。 还可启用[生存时间 (TTL)](time-to-live.md)功能在指定时间段后自动删除数据，不会产生任何额外费用。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>后续步骤
* 若要详细了解 Cosmos DB 数据库安全性，请参阅 [Cosmos DB：数据库安全性](database-security.md)。
* 若要了解如何管理主密钥和只读密钥，请参阅[如何管理 Azure Cosmos DB 帐户](manage-account.md#keys)。
* 若要了解如何构造 Azure Cosmos DB 授权令牌，请参阅 [Azure Cosmos DB 资源的访问控制](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources)。
