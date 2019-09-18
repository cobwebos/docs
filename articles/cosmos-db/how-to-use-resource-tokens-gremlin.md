---
title: 使用 Gremlin 的 Azure Cosmos DB 资源令牌
description: 了解如何创建资源令牌并使用它们访问图形数据库
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/06/2019
ms.author: olignat
ms.openlocfilehash: fcb18fb14cf787713735da07ca2048d0853fa46c
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2019
ms.locfileid: "70807008"
---
# <a name="azure-cosmos-db-resource-tokens-with-gremlin"></a>使用 Gremlin 的 Azure Cosmos DB 资源令牌
本文介绍如何通过 Gremlin SDK 使用[Cosmos DB 资源令牌](secure-access-to-data.md)访问图形数据库。

## <a name="create-a-resource-token"></a>创建资源令牌

TinkerPop Gremlin SDK 没有用于创建资源令牌的 API。 资源令牌是一个 Cosmos DB 概念。 若要创建资源令牌，请下载 [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)。 如果你的应用程序需要创建资源令牌并使用它们来访问图形数据库，则它需要 2 个单独的 SDK。

资源标记之上的对象模型层次结构：
- **Cosmos DB 帐户** - 具有与之关联的 DNS 的顶级实体，例如 `contoso.gremlin.cosmos.azure.com`
  - **Cosmos DB 数据库**
    - **User**
      - 权限 
        - *Token* -“权限”  对象的一个属性，该属性表示允许或拒绝的操作。

资源令牌具有格式 `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`。 此字符串对于客户端是不透明的，应按原样使用，而无任何修改或解释。

```csharp
// Notice that document client is created against .NET SDK end-point rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // Token will not be returned during ReadPermissionReedAsync() call.
  // This call will succeed only if database id, user id and permission id already exist. 
  // Note that <database id> is not a database name, it is a base64 string that represents database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>使用资源令牌
构造 `GremlinServer` 类时，资源令牌可直接用作“password”属性。

```csharp
// Gremlin application needs to be given a resource token. It can't discover the token on its own.
// Token can be obtained for a given permission using Cosmos DB SDK or passed into the application as command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure gremlin servier to use resource token rather than master key
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

这种方法在所有 TinkerPop Gremlin SDK 中都有效。

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>限制

单个 Gremlin 帐户可以颁发无限数量的令牌，但 1 小时内只能并发使用最多 100 个令牌   。 如果应用程序超过每小时的令牌限制，则将拒绝身份验证请求并显示错误消息 `"Exceeded allowed resource token limit of 100 that can be used concurrently"`。 关闭带有特定标记的活动连接以便为新令牌释放槽不会有成效。 Cosmos DB Gremlin 数据库引擎将跟踪身份验证请求前一小时内的不同令牌。

## <a name="permission"></a>权限

使用资源令牌时应用程序遇到的常见错误是 `"Insufficient permissions provided in the authorization header for the corresponding request. Please retry with another authorization header."`。 当 Gremlin 遍历尝试写入边缘或顶点但资源令牌仅授予 `Read` 权限时，会返回此错误。 检查遍历是否包含以下任何步骤：`.addV()`、`.addE()`、`.drop()` 或 `.property()`。

## <a name="next-steps"></a>后续步骤
* Azure Cosmos DB 中[基于角色的访问控制](role-based-access-control.md)
* [了解如何保护对 Azure Cosmos DB 中数据的访问](secure-access-to-data.md)
