---
title: "Durable Functions 的单一实例 - Azure"
description: "如何使用 Azure Functions 的 Durable Functons 扩展中的单一实例。"
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: e82cc53d53a6d0296aaab2c3a76ad4e2f6c12c54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Durable Functions 中的单一实例业务流程协调程序 (Azure Functions)

对于后台作业或参与者样式的业务流程，你通常需要确保特定的业务流程协调程序一次只有一个实例在运行。 这可以在 [Durable Functions](durable-functions-overview.md) 中通过在创建业务流程协调程序时为其分配特定的实例 ID 来实现。

## <a name="singleton-example"></a>单一实例示例

以下 C# 示例显示了一个 HTTP 触发器函数，该函数创建一个单一实例后台作业业务流程。 它使用已知的实例 ID 来确保只存在一个实例。

```cs
[FunctionName("EnsureSingletonTrigger")]
public static async Task<HttpResponseMessage> Ensure(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    // Ensure only one instance is ever running at a time
    const string OrchestratorName = "MySingletonOrchestrator";
    const string InstanceId = "MySingletonInstanceId";

    var existingInstance = await starter.GetStatusAsync(InstanceId);
    if (existingInstance == null)
    {
        log.Info($"Creating singleton instance with ID = {InstanceId}...");
        await starter.StartNewAsync(OrchestratorName, InstanceId, input: null);
    }

    return starter.CreateCheckStatusResponse(req, InstanceId);
}
```

默认情况下，实例 ID 是随机生成的 GUID。 但请注意，在本例中，触发器函数使用一个值为 `MySingletonInstanceId` 的预定义 `InstanceId` 变量来为业务流程协调程序函数预分配实例 ID。 这使得触发器可以通过调用 [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) 来检查并查看该已知实例是否已在运行。

业务流程协调程序函数的实现细节实际上无关紧要。 它可以是一个启动并完成的常规业务流程协调程序函数，也可以是一个永远运行的业务流程协调程序函数（即[永久业务流程](durable-functions-eternal-orchestrations.md)）。 重点是一次只有一个实例在运行。

> [!NOTE]
> 如果该单一实例业务流程实例终止，则无法使用同一 ID 重新创建它。 在那种情况下，应当准备好使用新的实例 ID 重新创建它。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何调用子业务流程](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [运行示例单一实例](durable-functions-counter.md)
