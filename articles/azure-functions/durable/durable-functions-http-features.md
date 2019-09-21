---
title: Durable Functions 中的 HTTP 功能-Azure Functions
description: 了解 Azure Functions Durable Functions 扩展中的集成 HTTP 功能。
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 5a9143643b1a1cabb32903933dbd68d665d0424f
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71171129"
---
# <a name="http-features"></a>HTTP 功能

Durable Functions 提供了若干功能，使你可以轻松地将持久业务流程和实体合并到 HTTP 工作流。 本文介绍其中一些功能的详细信息。

## <a name="exposing-http-apis"></a>公开 HTTP Api

可以使用 HTTP 请求调用和管理业务流程和实体。 Durable Functions 扩展公开了内置 HTTP Api，还提供了 Api，用于与 HTTP 触发的函数中的业务流程和实体交互。

### <a name="built-in-http-apis"></a>内置 HTTP Api

Durable Functions 扩展会自动将一组 HTTP Api 添加到 Azure Functions 主机。 通过这些 Api，无需编写任何代码即可与业务流程和实体进行交互和管理。

支持以下内置 HTTP Api。

* [启动新业务流程](durable-functions-http-api.md#start-orchestration)
* [查询业务流程实例](durable-functions-http-api.md#get-instance-status)
* [终止业务流程实例](durable-functions-http-api.md#terminate-instance)
* [将外部事件发送到业务流程](durable-functions-http-api.md#raise-event)
* [清除业务流程历史记录](durable-functions-http-api.md#purge-single-instance-history)
* [向实体发送操作事件](durable-functions-http-api.md#signal-entity)
* [查询实体的状态](durable-functions-http-api.md#query-entity)

有关 Durable Functions 扩展公开的所有内置 HTTP Api 的完整说明，请参阅[HTTP api](durable-functions-http-api.md)一文。

### <a name="http-api-url-discovery"></a>HTTP API URL 发现

[业务流程客户端绑定](durable-functions-bindings.md#orchestration-client)公开了可用于生成便利 HTTP 响应负载的 api。 例如，它可以创建一个响应，其中包含指向特定业务流程实例的管理 Api 的链接。 下面是一个示例 HTTP 触发器函数示例，演示了如何将此 API 用于新的业务流程实例：

#### <a name="precompiled-c"></a>预编译 C#

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

#### <a name="c-script"></a>C# 脚本

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

#### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

#### <a name="functionjson"></a>Function.json

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

使用上述 HTTP 触发器函数启动业务流程协调程序函数可以使用任何 HTTP 客户端。 以下卷曲命令启动一个名为`DoWork`的业务流程协调程序函数。

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

下面是`abc123`作为 ID 的业务流程的示例响应（为清楚起见，移除了一些详细信息）：

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

上一示例中的每个字段都对应于内置HTTPapi。`~Uri` 这些 Api 可用于管理目标业务流程实例。

> [!NOTE]
> Webhook URL 的格式可能会有所不同，具体取决于所运行 Azure Functions 主机的版本。 上面的示例适用于 Azure Functions 2.0 主机。

有关所有内置 HTTP Api 的说明，请参阅[HTTP API 参考](durable-functions-http-api.md)文档。

### <a name="async-operation-tracking"></a>异步操作跟踪

前面提到的 HTTP 响应旨在通过 Durable Functions 实现长时间运行的 HTTP 异步 API。 此模式有时称为*轮询使用者模式*。 客户端/服务器流工作方式如下：

1. 客户端发出 HTTP 请求，启动长时间运行的进程，例如业务流程协调程序函数。
2. 目标 HTTP 触发器返回 HTTP 202 响应，其中包含带有 `statusQueryGetUri` 值的 `Location` 标头。
3. 客户端轮询 `Location` 标头中的 URL。 可继续看到包含 `Location` 标头的 HTTP 202 响应。
4. 实例完成（或失败）后，`Location` 标头中的终结点返回 HTTP 200。

此协议允许通过外部客户端或支持轮询 HTTP 终结点并遵循 `Location` 标头的服务协调长时间运行的进程。 此模式的客户端和服务器实现内置于 Durable Functions HTTP Api 中。

> [!NOTE]
> 默认情况下，[Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)提供的所有基于 HTTP 的操作都支持标准异步操作模式。 使用此功能，可在逻辑应用工作流中嵌入长时间运行的持久函数。 有关异步 HTTP 模式的逻辑应用支持的更多详细信息，请参阅 [Azure 逻辑应用工作流操作和触发器文档](../../logic-apps/logic-apps-workflow-actions-triggers.md)。

> [!NOTE]
> 可以从任何函数类型（而不仅仅是 HTTP 触发的函数）进行与业务流程的交互。

有关如何使用客户端 Api 管理业务流程和实体的详细信息，请参阅[实例管理](durable-functions-instance-management.md)一文。

## <a name="consuming-http-apis"></a>使用 HTTP Api

不允许业务流程协调程序函数直接执行 i/o，如[协调程序函数代码约束](durable-functions-code-constraints.md)中所述。 相反，orchestrator 函数通常调用执行 i/o 操作的[活动函数](durable-functions-types-features-overview.md#activity-functions)。

从 Durable Functions 2.0 开始，业务流程能够使用[业务流程触发器绑定](durable-functions-bindings.md#orchestration-trigger)以本机方式使用 HTTP api。

> [!NOTE]
> JavaScript 中尚不提供直接从 orchestrator 函数调用 HTTP 终结点的功能。

下面的示例代码演示了C#一个使用`CallHttpAsync` .net API 发出出站 HTTP 请求的业务流程协调程序函数：

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

通过 "调用 HTTP" 操作，你可以在业务流程协调程序函数中执行以下操作：

* 直接从业务流程函数调用 HTTP Api （稍后会提到一些限制）。
* 自动支持客户端 HTTP 202 状态轮询模式。
* 使用[Azure 托管标识](../../active-directory/managed-identities-azure-resources/overview.md)对其他 Azure 终结点进行授权 HTTP 调用。

直接从 orchestrator 函数使用 HTTP Api 的能力旨在为一组特定的常见方案提供便利。 您可以使用活动功能自行实现所有这些功能。 在许多情况下，活动功能可能会给您带来更大的灵活性。

### <a name="http-202-handling"></a>HTTP 202 处理

"调用 HTTP" API 可自动实现*轮询使用者模式*的客户端。 如果调用的 API 返回带有`Location`标头的 HTTP 202 响应，则该业务流程协调程序函数将自动轮询该`Location`资源，直到有一个非202响应返回为止。 非202响应将是返回到业务流程协调程序函数代码的响应。

> [!NOTE]
> Orchestrator 函数本身还支持服务器端*轮询使用者模式*，如[异步操作跟踪](#async-operation-tracking)中所述。 这意味着一个函数应用中的业务流程可以轻松地协调其他函数应用中的业务流程协调程序函数。 这类似于[子业务流程](durable-functions-sub-orchestrations.md)概念，但支持跨应用通信。 这对于微服务样式的应用程序开发特别有用。

### <a name="managed-identities"></a>托管标识

Durable Functions 本机支持调用接受 Azure AD 的授权令牌的 Api。 此支持使用[Azure 托管标识](../../active-directory/managed-identities-azure-resources/overview.md)来获取这些令牌。

下面的代码是一个 .NET orchestrator 函数的示例，该函数使经过身份验证的调用能够使用 Azure 资源管理器[虚拟机 REST API](https://docs.microsoft.com/rest/api/compute/virtualmachines)重新启动虚拟机。

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

在上面的示例中， `tokenSource`将参数配置为获取[Azure 资源管理器](../../azure-resource-manager/resource-group-overview.md)Azure AD 令牌（由 "资源 URI" `https://management.core.windows.net`标识）。 该示例假设当前函数应用在本地运行，或者已使用托管标识部署为 Azure Functions 应用。 假定本地标识或托管标识具有管理指定资源组`myRG`中的 vm 的权限。

在运行时，配置的令牌源会自动返回 OAuth 2.0 访问令牌，并将其作为持有者令牌`Authorization`添加到传出请求的标头。 此模型是将授权标头手动添加到 HTTP 请求的改进，原因如下：

* 令牌刷新会自动处理。 无需担心令牌已过期。
* 标记永远不会存储在持久业务流程状态。
* 无需编写任何代码即可处理令牌采集。

可在[预编译C#的 "RestartVMs" 示例](https://github.com/Azure/azure-functions-durable-extension/blob/v2/samples/v2/precompiled/RestartVMs.cs)中找到更完整的示例。

托管标识并不局限于 Azure 资源管理。 托管标识可用于访问任何接受 Azure AD 持有者令牌（包括第一方 Azure 服务或第三方 web 应用程序）的 API。 第三方 web 应用甚至可以是另一个函数应用。 有关支持使用 Azure AD 进行身份验证的第一方 Azure 服务的列表，请参阅[支持 Azure AD 身份验证的 Azure 服务](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

### <a name="limitations"></a>限制

对调用 HTTP Api 的内置支持是一项便利功能，并不适合所有方案。 业务流程协调程序函数发送的 HTTP 请求将作为队列消息进行序列化并持久保存。 此排队行为可确保 HTTP 调用[对于业务流程重播是可靠和安全的](durable-functions-orchestrations.md#reliability)。 但是，此排队行为也意味着：

* 与本机 HTTP 客户端相比，每个 HTTP 请求都涉及到额外的延迟。
* 无法装入队列消息的大型请求或响应消息可能会显著降低协调程序性能。 潜在的性能下降是由于将消息负载卸载到 blob 存储的系统开销。
* 不支持流式处理、分块和二进制负载。
* 自定义 HTTP 客户端的行为的功能受到限制。

如果这些限制中有任何可能会影响你的使用情况，请考虑改用活动函数和特定于语言的 HTTP 客户端库来发出出站 HTTP 调用。

> [!NOTE]
> 如果你是 .net 开发人员，则可能想知道此功能为何使用`DurableHttpRequest`和`DurableHttpResponse`类型，而不是内置 .net `HttpRequestMessage`和`HttpResponseMessage`。 此设计选择是故意的。 主要原因是自定义类型有助于确保用户不会对内部 HTTP 客户端支持的行为做出正确假设。 利用持久性特定的类型，还可以简化 API 设计，更轻松地将特殊功能（如[托管标识集成](#managed-identities)和[轮询使用者模式](#http-202-handling)）浅化。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解持久性实体](durable-functions-entities.md)
