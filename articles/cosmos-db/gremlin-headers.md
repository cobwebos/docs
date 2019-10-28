---
title: Azure Cosmos DB Gremlin 响应标头
description: 用于实现额外疑难解答的服务器响应元数据的参考文档
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 95677f4c45c0213de5ffac5521bac1c6bf7294e4
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755080"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Azure Cosmos DB Gremlin 服务器响应标头
本文介绍了请求执行时 Cosmos DB Gremlin server 返回给调用方的标头。 这些标头可用于排查请求性能问题，生成与 Cosmos DB 服务进行本机集成并简化客户支持的应用程序。

请记住，在这些标头上依赖于您将应用程序的可移植性限制到其他 Gremlin 实现。 返回时，与 Cosmos DB Gremlin 更紧密的集成。 这些标头不是 TinkerPop 标准。

## <a name="headers"></a>标头

| 标头 | Type | 示例值 | 包含时 | 说明 |
| --- | --- | --- | --- | --- |
| **x-ms-request-charge** | double | 11.3243 | Success 和 Failure | 部分响应消息的[请求单位（RU/s 或 RU）](request-units.md)使用的集合或数据库吞吐量。 此标头存在于具有多个块的请求的每个继续符中。 它反映特定响应区块的费用。 仅适用于由单个响应块区的请求此标头与遍历的总成本匹配。 但对于大多数复杂的遍历，此值表示部分成本。 |
| **x-ms-total-request-charge** | double | 423.987 | Success 和 Failure | 整个请求的[请求单位（RU/s 或 RU）](request-units.md)使用的集合或数据库吞吐量。 此标头存在于具有多个块的请求的每个继续符中。 它表示自请求开始后的累计费用。 最后一个区块中此标头的值指示完整请求费用。 |
| **x ms-服务器-时间-毫秒** | double | 13.75 | Success 和 Failure | 包括此标头是为了进行延迟故障排除。 它表示 Cosmos DB Gremlin 服务器执行并生成部分响应消息所需的时间（以毫秒为单位）。 使用此标头的值，并将其与整体请求延迟应用程序进行比较，可以计算网络延迟开销。 |
| **x-ms-总服务器-时间-毫秒** | double | 130.512 | Success 和 Failure | Cosmos DB Gremlin 服务器执行整个遍历所用的总时间（以毫秒为单位）。 此标头包含在每个部分的响应中。 它表示自请求开始后的累计执行时间。 最后一个响应指示总执行时间。 此标头可用于区分客户端和服务器作为延迟源。 您可以将客户端上的遍历执行时间与此标头的值进行比较。 |
| **x-ms-status-code** | long | 200 | Success 和 Failure | 标头指示请求完成或终止的内部原因。 建议应用程序查看此标头的值并采取纠正措施。 |
| **x-毫秒-子状态-代码** | long | 1003 | 仅故障 | Cosmos DB 是在统一存储层之上构建的多模型数据库。 此标头包含在高可用性堆栈的较低层中发生故障时的有关失败原因的其他见解。 建议应用程序存储此标头，并在联系 Cosmos DB 客户支持时使用。 此标头的值有助于 Cosmos DB 工程师快速进行故障排除。 |
| **x-ms-retry-after-ms** | string （TimeSpan） | "00：00： 03.9500000" | 仅故障 | 此标头是 .NET [TimeSpan](https://docs.microsoft.com/dotnet/api/system.timespan)类型的字符串表示形式。 此值将仅包括在由于预配的吞吐量耗尽导致的请求失败中。 应用程序应在指示的时间段后再次重新提交遍历。 |
| **x-ms-activity-id** | 字符串（Guid） | "A9218E01-3A3A-4716-9636-5BD86B056613" | Success 和 Failure | 标头包含请求的唯一服务器端标识符。 服务器为每个请求分配一个唯一标识符，以便进行跟踪。 应用程序应该记录服务器返回的活动标识符，请求客户可能需要与客户支持人员联系。 Cosmos DB 支持人员可以在 Cosmos DB 服务遥测中查找这些标识符发出的特定请求。 |

## <a name="status-codes"></a>状态代码

下面列出了服务器返回的最常见状态代码。

| 状态 | 说明 |
| --- | --- |
| **401** | 身份验证密码与 Cosmos DB 帐户密钥不匹配时，将返回 `"Unauthorized: Invalid credentials provided"` 错误消息。 导航到 Azure 门户中的 Cosmos DB Gremlin 帐户，并确认密钥是正确的。|
| **404** | 尝试同时删除和更新同一边缘或顶点的并发操作。 错误消息 `"Owner resource does not exist"` 指示，在 `/dbs/<database name>/colls/<collection or graph name>` 格式的连接参数中指定的数据库或集合不正确。|
| **408** | `"Server timeout"` 指示遍历超过**30 秒**，已被服务器取消。 通过在每个遍历点上筛选顶点或边缘来缩小搜索范围，优化遍历以快速运行。|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."` 这种情况通常发生在关系图中已存在具有标识符的顶点或边缘。| 
| **412** | 状态代码将 `"PreconditionFailedException": One of the specified pre-condition is not met` 的错误消息进行求反。 此错误表示读取边缘或顶点并在修改后将它写回存储区之间存在开放式并发控制冲突。 发生此错误时，最常见的情况是修改了属性，例如 `g.V('identifier').property('name','value')`。 Gremlin 引擎将读取该顶点，对其进行修改，然后将其写回。 如果并行运行的另一个遍历尝试写入同一顶点或一个边缘，其中一个将收到此错误。 应用程序应再次向服务器提交遍历。| 
| **429** | 请求已中止，并且应在 **-毫秒后重试**的值之后重试| 
| **500** | 包含 `"NotFoundException: Entity with the specified id does not exist in the system."` 的错误消息指示数据库和/或集合已使用相同的名称重新创建。 当更改传播并使不同 Cosmos DB 组件中的缓存失效时，此错误将在 5 分钟内消失。 若要避免此问题，请每次都使用唯一的数据库名称和集合名称。| 
| **1000** | 当服务器成功分析消息但无法执行时，将返回此状态代码。 这通常表示查询存在问题。| 
| **1001** | 当服务器完成遍历执行但未能将响应序列回客户端时，将返回此代码。 当遍历生成复杂的结果时，如果该结果太大或不符合 TinkerPop 协议规范，则会发生此错误。 当应用程序遇到此错误时，应简化遍历。 | 
| **1003** | 当遍历超出允许的内存限制时，将返回 `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"`。 内存限制为每个遍历**2 GB** 。| 
| **1004** | 此状态代码指示图形请求格式不正确。 当请求无法反序列化、非值类型被反序列化为值类型或请求不受支持的 gremlin 操作时，请求可能格式不正确。 应用程序不应重试请求，因为它不会成功。 | 
| **1007** | 此状态代码通常返回，并 `"Could not process request. Underlying connection has been closed."` 错误消息。 如果客户端驱动程序尝试使用服务器正在关闭的连接，则可能会发生这种情况。 应用程序应重试遍历不同的连接。
| **1008** | Cosmos DB Gremlin 服务器可以终止连接以重新平衡群集中的流量。 客户端驱动程序应该会处理这种情况并仅使用实时连接将请求发送到服务器。 偶尔，客户端驱动程序可能不会检测到该连接已关闭。 当应用程序遇到错误时，`"Connection is too busy. Please retry after sometime or open more connections."` 应重试遍历不同的连接。

## <a name="samples"></a>示例

基于 Gremlin.Net 的示例客户端应用程序，它读取一个状态属性：

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

演示如何从 Gremlin java 客户端读取状态属性的示例：

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
* [常见 Azure Cosmos DB REST 响应标头](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [TinkerPop Graph 驱动程序提供程序要求]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
