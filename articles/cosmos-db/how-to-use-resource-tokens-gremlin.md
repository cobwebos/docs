---
title: 通过 Gremlin SDK 使用 Azure Cosmos DB 资源令牌
description: 了解如何创建资源令牌并使用它们访问图形数据库。
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/06/2019
ms.openlocfilehash: 443b6ea2583c7c8a1c633cf1825e83cc02bd168c
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756067"
---
# <a name="use-azure-cosmos-db-resource-tokens-with-the-gremlin-sdk"></a>通过 Gremlin SDK 使用 Azure Cosmos DB 资源令牌

本文介绍如何通过 Gremlin SDK 使用 [Azure Cosmos DB 资源令牌](secure-access-to-data.md)访问图形数据库。

## <a name="create-a-resource-token"></a>创建资源令牌

Apache TinkerPop Gremlin SDK 没有用于创建资源令牌的 API。 术语“资源令牌”  是一个 Azure Cosmos DB 概念。 若要创建资源令牌，请下载 [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)。 如果你的应用程序需要创建资源令牌并使用它们来访问图形数据库，则需要两个单独的 SDK。

下面的大纲演示了资源标记之上的对象模型层次结构：

- **Azure Cosmos DB 帐户** - 具有关联 DNS 的顶级实体（例如，`contoso.gremlin.cosmos.azure.com`）。
  - **Azure Cosmos DB 数据库**
    - **User**
      - 权限 
        - **令牌** - 表示允许或拒绝哪些操作的权限对象属性。

资源令牌使用以下格式：`"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`。 此字符串对于客户端是不透明的，应按原样使用，而无任何修改或解释。

```csharp
// Notice that document client is created against .NET SDK endpoint, rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // The token isn't returned during the ReadPermissionReedAsync() call.
  // The call succeeds only if database id, user id, and permission id already exist. 
  // Note that <database id> is not a database name. It is a base64 string that represents the database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>.
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>使用资源令牌
构造 GremlinServer 类时，可以将资源令牌直接用作“password”属性。

```csharp
// The Gremlin application needs to be given a resource token. It can't discover the token on its own.
// You can obtain the token for a given permission by using the Azure Cosmos DB SDK, or you can pass it into the application as a command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure the Gremlin server to use a resource token rather than a master key.
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
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

// The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>限制

使用单个 Gremlin 帐户，可以颁发无限数量的令牌。 但是，在 1 小时内最多只能同时使用 100 个令牌。 如果应用程序超过每小时令牌限制，身份验证请求将被拒绝，你将收到以下错误消息：“超出了允许的资源令牌限制，最多可以同时使用 100 个。” 关闭使用特定令牌的活动连接以便为新令牌释放槽不起作用。 Azure Cosmos DB Gremlin 数据库引擎在身份验证请求之前的一小时内跟踪唯一令牌。

## <a name="permission"></a>权限

应用程序在使用资源令牌时遇到的常见错误是“授权标头中为相应请求提供的权限不足。 请使用另一个授权标头重试”。 当 Gremlin 遍历尝试写入边缘或顶点但资源令牌仅授予“读取”  权限时，会返回此错误。 检查遍历是否包含以下任何步骤：.addV()  、.addE()  、.drop()  或 .property()  。

## <a name="next-steps"></a>后续步骤
* Azure Cosmos DB 中[基于角色的访问控制](role-based-access-control.md)
* [了解如何保护对 Azure Cosmos DB 中数据的访问](secure-access-to-data.md)
