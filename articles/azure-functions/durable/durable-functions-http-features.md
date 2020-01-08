---
title: Durable Functions 中的 HTTP 功能-Azure Functions
description: 了解 Azure Functions Durable Functions 扩展中的集成 HTTP 功能。
author: cgillum
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 1c8f56810edb39db66cbb83750e5cff02e22662a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433288"
---
# <a name="http-features"></a>HTTP 功能

Durable Functions 提供了若干功能，使你可以轻松地将持久业务流程和实体合并到 HTTP 工作流。 本文将详细介绍其中一些功能。

## <a name="exposing-http-apis"></a>公开 HTTP Api

可以使用 HTTP 请求调用和管理业务流程和实体。 Durable Functions 扩展公开了内置 HTTP Api。 它还提供了 Api，用于与 HTTP 触发的函数中的业务流程和实体交互。

### <a name="built-in-http-apis"></a>内置 HTTP Api

Durable Functions 扩展会自动将一组 HTTP Api 添加到 Azure Functions 主机。 借助这些 Api，无需编写任何代码即可与业务流程和实体交互和管理它们。

支持以下内置 HTTP Api。

* [启动新业务流程](durable-functions-http-api.md#start-orchestration)
* [查询业务流程实例](durable-functions-http-api.md#get-instance-status)
* [终止业务流程实例](durable-functions-http-api.md#terminate-instance)
* [将外部事件发送到业务流程](durable-functions-http-api.md#raise-event)
* [清除业务流程历史记录](durable-functions-http-api.md#purge-single-instance-history)
* [向实体发送操作事件](durable-functions-http-api.md#signal-entity)
* [获取实体的状态](durable-functions-http-api.md#get-entity)
* [查询实体的列表](durable-functions-http-api.md#list-entities)

有关 Durable Functions 扩展公开的所有内置 HTTP Api 的完整说明，请参阅[HTTP api 一文](durable-functions-http-api.md)。

### <a name="http-api-url-discovery"></a>HTTP API URL 发现

[业务流程客户端绑定](durable-functions-bindings.md#orchestration-client)公开了可生成便利 HTTP 响应负载的 api。 例如，它可以创建一个响应，其中包含指向特定业务流程实例的管理 Api 的链接。 下面的示例演示了一个 HTTP 触发器函数，该函数演示如何对新的业务流程实例使用此 API：

#### <a name="precompiled-c"></a>预编译 C#

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

#### <a name="c-script"></a>C# 脚本

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

#### <a name="javascript-with-functions-20-or-later-only"></a>仅限函数为2.0 或更高版本的 JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

#### <a name="functionjson"></a>Function.json

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

使用之前所示的 HTTP 触发器函数启动业务流程协调程序函数可以使用任何 HTTP 客户端。 以下卷命令启动一个名为 `DoWork`的业务流程协调程序函数：

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

接下来是 `abc123` 作为其 ID 的业务流程的示例响应。 为了清楚起见，已经删除了一些详细信息。

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

在上面的示例中，以 `Uri` 结尾的每个字段都对应于一个内置 HTTP API。 您可以使用这些 Api 来管理目标业务流程实例。

> [!NOTE]
> Webhook Url 的格式取决于运行的 Azure Functions 主机版本。 前面的示例适用于2.0 主机 Azure Functions。

有关所有内置 HTTP Api 的说明，请参阅[HTTP API 参考](durable-functions-http-api.md)。

### <a name="async-operation-tracking"></a>异步操作跟踪

前面提到的 HTTP 响应旨在通过 Durable Functions 实现长时间运行的 HTTP 异步 API。 此模式有时称为*轮询使用者模式*。 客户端/服务器流工作方式如下：

1. 客户端发出 HTTP 请求以启动长时间运行的进程，如业务流程协调程序函数。
1. 目标 HTTP 触发器返回一个具有值 "statusQueryGetUri" 的位置标头的 HTTP 202 响应。
1. 客户端轮询 Location 标头中的 URL。 客户端继续查看具有位置标头的 HTTP 202 响应。
1. 当实例完成或失败时，Location 标头中的终结点将返回 HTTP 200。

此协议允许协调长时间运行的进程与外部客户端或可轮询 HTTP 终结点的服务，并遵循 Location 标头。 此模式的客户端和服务器实现内置于 Durable Functions HTTP Api 中。

> [!NOTE]
> 默认情况下，[Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)提供的所有基于 HTTP 的操作都支持标准异步操作模式。 使用此功能，可在逻辑应用工作流中嵌入长时间运行的持久函数。 可以在[Azure 逻辑应用工作流操作和触发器文档](../../logic-apps/logic-apps-workflow-actions-triggers.md)中了解有关逻辑应用对异步 HTTP 模式的支持的更多详细信息。

> [!NOTE]
> 可以从任何函数类型（而不只是 HTTP 触发的函数）进行与业务流程的交互。

有关如何使用客户端 Api 管理业务流程和实体的详细信息，请参阅[实例管理一文](durable-functions-instance-management.md)。

## <a name="consuming-http-apis"></a>使用 HTTP Api

如业务流程[协调程序函数代码约束](durable-functions-code-constraints.md)中所述，orchestrator 函数无法直接执行 i/o 操作。 相反，它们通常调用执行 i/o 操作的[活动函数](durable-functions-types-features-overview.md#activity-functions)。

从 Durable Functions 2.0 开始，业务流程可以通过使用[业务流程触发器绑定](durable-functions-bindings.md#orchestration-trigger)，以本机方式使用 HTTP api。

> [!NOTE]
> JavaScript 中尚不提供直接从 orchestrator 函数调用 HTTP 终结点的功能。

下面的示例代码演示了C#一个使用**CallHttpAsync** .net API 发出出站 HTTP 请求的业务流程协调程序函数：

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

使用 "调用 HTTP" 操作，可以在业务流程协调程序函数中执行以下操作：

* 直接从业务流程函数调用 HTTP Api，但会在以后提到一些限制。
* 自动支持客户端 HTTP 202 状态轮询模式。
* 使用[Azure 托管标识](../../active-directory/managed-identities-azure-resources/overview.md)对其他 Azure 终结点进行授权 HTTP 调用。

直接从 orchestrator 函数使用 HTTP Api 的能力旨在为一组特定的常见方案提供便利。 您可以使用活动功能自行实现所有这些功能。 在许多情况下，活动功能可能会给您带来更大的灵活性。

### <a name="http-202-handling"></a>HTTP 202 处理

"调用 HTTP" API 可自动实现轮询使用者模式的客户端。 如果调用的 API 返回带有 Location 标头的 HTTP 202 响应，则协调器函数会自动轮询位置资源，直到收到202以外的响应。 此响应将是返回到业务流程协调程序函数代码的响应。

> [!NOTE]
> Orchestrator 函数本身还支持服务器端轮询使用者模式，如[异步操作跟踪](#async-operation-tracking)中所述。 此支持意味着一个函数应用中的业务流程可以轻松地协调其他函数应用中的业务流程协调程序函数。 这类似于[子业务流程](durable-functions-sub-orchestrations.md)概念，但支持跨应用通信。 此支持对于微服务样式的应用程序开发特别有用。

### <a name="managed-identities"></a>托管标识

Durable Functions 本身支持对接受 Azure Active Directory （Azure AD）令牌进行授权的 Api 调用。 此支持使用[Azure 托管标识](../../active-directory/managed-identities-azure-resources/overview.md)来获取这些令牌。

下面的代码是 .NET orchestrator 函数的一个示例。 此函数通过使用 Azure 资源管理器[虚拟机 REST API](https://docs.microsoft.com/rest/api/compute/virtualmachines)使经过身份验证的调用重新启动虚拟机。

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

在前面的示例中，`tokenSource` 参数配置为获取[Azure 资源管理器](../../azure-resource-manager/management/overview.md)Azure AD 令牌。 令牌由资源 URI `https://management.core.windows.net`标识。 该示例假设当前函数应用在本地运行，或者已部署为具有托管标识的函数应用。 假设本地标识或托管标识具有管理指定资源组中的 Vm 的权限 `myRG`。

在运行时，配置的令牌源会自动返回 OAuth 2.0 访问令牌。 然后，源会将令牌作为持有者令牌添加到传出请求的 Authorization 标头。 由于以下原因，此模型是手动将授权标头添加到 HTTP 请求的一项改进：

* 令牌刷新会自动处理。 无需担心令牌已过期。
* 标记永远不会存储在持久业务流程状态。
* 无需编写任何代码即可管理令牌采集。

可以在[预C#编译的 RestartVMs 示例](https://github.com/Azure/azure-functions-durable-extension/blob/v2/samples/v2/precompiled/RestartVMs.cs)中找到更完整的示例。

托管标识并不局限于 Azure 资源管理。 你可以使用托管标识访问任何接受 Azure AD 持有者令牌的 API，包括来自 Microsoft 的 Azure 服务和来自合作伙伴的 web 应用。 合作伙伴的 web 应用甚至可以是另一个函数应用。 有关支持使用 Azure AD 进行身份验证的 Microsoft Azure 服务列表，请参阅[支持 Azure AD 身份验证的 azure 服务](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

### <a name="limitations"></a>限制

对调用 HTTP Api 的内置支持是一项便利功能。 并非适用于所有情况。

业务流程协调程序函数发送的 HTTP 请求以及它们的响应将作为队列消息进行序列化和永久保存。 此排队行为可确保 HTTP 调用[对于业务流程重播是可靠和安全的](durable-functions-orchestrations.md#reliability)。 但是，队列行为也有一些限制：

* 与本机 HTTP 客户端相比，每个 HTTP 请求都涉及到额外的延迟。
* 无法装入队列消息的大型请求或响应消息可能会显著降低协调程序性能。 将消息负载卸载到 blob 存储的开销可能会导致性能下降。
* 不支持流式处理、分块和二进制负载。
* 自定义 HTTP 客户端的行为的功能受到限制。

如果这些限制中有任何一个可能会影响你的使用情况，请考虑改用活动函数和特定于语言的 HTTP 客户端库来发出出站 HTTP 调用。

> [!NOTE]
> 如果你是 .NET 开发人员，你可能想知道为什么此功能使用**DurableHttpRequest**和**DurableHttpResponse**类型，而不是内置的 .net **HttpRequestMessage**和**HttpResponseMessage**类型。
>
> 这是有意设计的。 主要原因是自定义类型有助于确保用户不会对内部 HTTP 客户端支持的行为做出正确假设。 特定于 Durable Functions 的类型还可以简化 API 设计。 它们还可以更轻松地创建可用的特殊功能，如[托管标识集成](#managed-identities)和[轮询使用者模式](#http-202-handling)。 

### <a name="extensibility-net-only"></a>扩展性（仅适用于 .NET）

可以使用[Azure Functions .net 依赖项注入](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection)自定义业务流程的内部 HTTP 客户端的行为。 此功能可用于进行较小的行为更改。 它还可用于通过注入 mock 对象对 HTTP 客户端进行单元测试。

下面的示例演示如何使用依赖关系注入禁用调用外部 HTTP 终结点的业务流程协调程序函数的 SSL 证书验证。

```csharp
public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        // Register own factory
        builder.Services.AddSingleton<
            IDurableHttpMessageHandlerFactory,
            MyDurableHttpMessageHandlerFactory>();
    }
}

public class MyDurableHttpMessageHandlerFactory : IDurableHttpMessageHandlerFactory
{
    public HttpMessageHandler CreateHttpMessageHandler()
    {
        // Disable SSL certificate validation (not recommended in production!)
        return new HttpClientHandler
        {
            ServerCertificateCustomValidationCallback =
                HttpClientHandler.DangerousAcceptAnyServerCertificateValidator,
        };
    }
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解持久性实体](durable-functions-entities.md)
