---
title: Azure Cosmos DB Gremlin 响应标头
description: 有关帮助进一步进行故障排除的服务器响应元数据的参考文档
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 95677f4c45c0213de5ffac5521bac1c6bf7294e4
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2020
ms.locfileid: "72755080"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Azure Cosmos DB Gremlin 服务器响应标头
本文介绍了执行请求时 Cosmos DB Gremlin 服务器返回给调用方的标头。 这些标头可用于排查请求性能问题，生成与 Cosmos DB 服务进行本机集成的应用程序，以及简化客户支持。

请记住，如果依赖这些标头，应用程序到其他 Gremlin 实现的可移植性将会受到限制。 好处是能够与 Cosmos DB Gremlin 更紧密地集成。 这些标头不属于 TinkerPop 标准。

## <a name="headers"></a>标头

| 标头 | 类型 | 示例值 | 如果包含 | 说明 |
| --- | --- | --- | --- | --- |
| **x-ms-request-charge** | double | 11.3243 | Success 和 Failure | 部分响应消息使用的集合量或数据库吞吐量，以[请求单位数（RU/秒或 RU）](request-units.md)表示。 此标头存在于具有多个块的请求的每次延续中。 它反映特定响应区块的费用。 仅对于由单个响应区块的请求而言，此标头将与遍历的总成本相匹配。 但对于大多数复杂的遍历而言，此值表示部分成本。 |
| **x-ms-total-request-charge** | double | 423.987 | Success 和 Failure | 整个请求使用的集合量或数据库吞吐量，以[请求单位数（RU/秒或 RU）](request-units.md)表示。 此标头存在于具有多个块的请求的每次延续中。 它表示自请求开始后的累积费用。 最后一个区块中此标头的值表示完整请求费用。 |
| **x-ms-server-time-ms** | double | 13.75 | Success 和 Failure | 此标头用于延迟故障排除目的。 它表示 Cosmos DB Gremlin 服务器执行并生成部分响应消息所花费的时间（以毫秒为单位）。 应用程序可以使用此标头的值并将其与整体请求延迟进行比较来计算网络延迟开销。 |
| **x-ms-total-server-time-ms** | double | 130.512 | Success 和 Failure | Cosmos DB Gremlin 服务器执行整个遍历所花费的总时间（以毫秒为单位）。 此标头包含在每个部分响应中。 它表示自请求开始后的累积执行时间。 最后一个响应表示总执行时间。 此标头可用于区分作为延迟来源的客户端和服务器。 可将客户端上的遍历执行时间与此标头的值进行比较。 |
| **x-ms-status-code** | long | 200 | Success 和 Failure | 标头表示请求完成或终止的内部原因。 建议在应用程序中检查此标头的值并采取纠正措施。 |
| **x-ms-substatus-code** | long | 1003 | 仅限故障 | Cosmos DB 是在统一存储层之上构建的多模型数据库。 此标头包含当高可用性堆栈的较低层中发生故障时有关故障原因的其他见解。 建议在应用程序中存储此标头，并在联系 Cosmos DB 客户支持人员时使用此标头。 Cosmos DB 工程师可以使用此标头的值快速进行故障排除。 |
| **x-ms-retry-after-ms** | 字符串 (TimeSpan) | "00:00:03.9500000" | 仅限故障 | 此标头是 .NET [TimeSpan](https://docs.microsoft.com/dotnet/api/system.timespan) 类型的字符串表示形式。 此值仅包含在由于耗尽预配吞吐量而失败的请求中。 在指定的时间段后，应用程序应再次重新提交遍历。 |
| **x-ms-activity-id** | 字符串 (Guid) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Success 和 Failure | 标头包含请求的唯一服务器端标识符。 服务器将为每个请求分配一个唯一标识符用于跟踪。 应用程序应该记录服务器针对请求返回的活动标识符，客户在联系客户支持人员时可以使用这些标识符。 Cosmos DB 支持人员可在 Cosmos DB 服务遥测数据中按这些标识符查找特定的请求。 |

## <a name="status-codes"></a>状态代码

下面列出了服务器返回的最常见状态代码。

| 状态 | 说明 |
| --- | --- |
| **401** | 当身份验证密码与 Cosmos DB 帐户密钥不匹配时，将返回错误消息 `"Unauthorized: Invalid credentials provided"`。 在 Azure 门户中导航到你的 Cosmos DB Gremlin 帐户，并确认密钥正确。|
| **404** | 尝试同时删除和更新相同的边或顶点的并发操作。 错误消息 `"Owner resource does not exist"` 指示，在 `/dbs/<database name>/colls/<collection or graph name>` 格式的连接参数中指定的数据库或集合不正确。|
| **408** | `"Server timeout"` 表示遍历花费的时间超过 **30 秒**，因此被服务器取消。 通过以下方式优化遍历，使之快速运行：在每个遍历跃点上筛选顶点或边缘，以缩小搜索范围。|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."` 如果图中已存在带标识符的顶点或边缘，通常会出现这种情况。| 
| **412** | 状态代码带有补充性的错误消息 `"PreconditionFailedException": One of the specified pre-condition is not met`。 此错误表示在读取边缘或顶点与将它在修改后写回存储区这两个操作之间存在开放式并发控制冲突。 此错误通常发生在修改属性后，例如 `g.V('identifier').property('name','value')`。 Gremlin 引擎会读取顶点，修改顶点，然后将其写回。 如果另一个并行运行的遍历尝试写入同一顶点或边缘，该顶点或边缘将收到此错误。 应用程序应再次向服务器提交遍历。| 
| **429** | 请求受到了限制，应在达到 x-ms-retry-after-ms 中的值后重试 | 
| **500** | 包含 `"NotFoundException: Entity with the specified id does not exist in the system."` 的错误消息指示已使用相同的名称重新创建数据库和/或集合。 当更改传播并使不同 Cosmos DB 组件中的缓存失效时，此错误将在 5 分钟内消失。 若要避免此问题，请每次都使用唯一的数据库名称和集合名称。| 
| **1000** | 当服务器成功分析了消息但无法执行时，将返回此状态代码。 这通常表示查询存在问题。| 
| **1001** | 当服务器完成遍历执行但无法将响应序列化回到客户端时，将返回此代码。 当遍历生成太大或不符合 TinkerPop 协议规范的复杂结果时，可能会发生此错误。 应用程序在遇到此错误时应简化遍历。 | 
| **1003** | 当遍历超过允许的内存限制时，将返回 `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"`。 每个遍历的内存限制为 **2 GB**。| 
| **1004** | 此状态代码表示图形请求格式不正确。 如果请求反序列化失败、将非值类型反序列化为值类型，或请求了不受支持的 Gremlin 操作，则请求的格式可能不正确。 应用程序不应重试该请求，因为该请求不会成功。 | 
| **1007** | 通常，此状态代码会连同错误消息 `"Could not process request. Underlying connection has been closed."` 一起返回。 如果客户端驱动程序尝试使用服务器正在关闭的连接，则可能会发生这种情况。 应用程序应在不同的连接上重试遍历。
| **1008** | Cosmos DB Gremlin 服务器可以终止连接以重新平衡群集中的流量。 客户端驱动程序应处理这种情况，并仅使用活动的连接将请求发送到服务器。 客户端驱动程序偶尔检测不到该连接已关闭。 当应用程序遇到错误 `"Connection is too busy. Please retry after sometime or open more connections."` 时，应在另一个连接上重试遍历。

## <a name="samples"></a>示例

基于 Gremlin.Net 的、可读取一个状态属性的示例客户端应用程序：

```csharp
// Following example reads a status code and total request charge from server response attributes.
// Variable "server" is assumed to be assigned to an instance of a GremlinServer that is connected to Cosmos DB account.
using (GremlinClient client = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
{
  ResultSet<dynamic> responseResultSet = await GremlinClientExtensions.SubmitAsync<dynamic>(client, requestScript: "g.V().count()");
  long statusCode = (long)responseResultSet.StatusAttributes["x-ms-status-code"];
  double totalRequestCharge = (double)responseResultSet.StatusAttributes["x-ms-total-request-charge"];

  // Status code and request charge are logged into application telemetry.
}
```

演示如何从 Gremlin Java 客户端读取状态属性的示例：

```java
try {
  ResultSet resultSet = this.client.submit("g.addV().property('id', '13')");
  List<Result> results = resultSet.all().get();

  // Process and consume results

} catch (ResponseException re) {
  // Check for known errors that need to be retried or skipped
  if (re.getStatusAttributes().isPresent()) {
    Map<String, Object> attributes = re.getStatusAttributes().get();
    int statusCode = (int) attributes.getOrDefault("x-ms-status-code", -1);

    // Now we can check for specific conditions
    if (statusCode == 409) {
        // Handle conflicting writes
      }
    }

    // Check if we need to delay retry
    if (attributes.containsKey("x-ms-retry-after-ms")) {
      // Read the value of the attribute as is
      String retryAfterTimeSpan = (String) attributes.get("x-ms-retry-after-ms"));

      // Convert the value into actionable duration
            LocalTime locaTime = LocalTime.parse(retryAfterTimeSpan);
            Duration duration = Duration.between(LocalTime.MIN, locaTime);

      // Perform a retry after "duration" interval of time has elapsed
    }
  }
}

```

## <a name="next-steps"></a>后续步骤
* [Azure Cosmos DB 的 HTTP 状态代码](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [常见的 Azure Cosmos DB REST 响应标头](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [TinkerPop 图形驱动程序提供程序要求]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
