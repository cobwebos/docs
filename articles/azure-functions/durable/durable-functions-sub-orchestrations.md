---
title: Durable Functions 的子业务流程 - Azure
description: 如何从 Azure Functions 的 Durable Functions 扩展中的业务流程中调用业务流程。
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: df4cfd8cdf720dd085c3f14ad518c557f270ffa4
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2018
ms.locfileid: "53340855"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Durable Functions 中的子业务流程 (Azure Functions)

除了调用活动函数之外，业务流程协调程序函数还可以调用其他业务流程协调程序函数。 例如，可以基于业务流程协调程序函数库构建更大的业务流程。 或者，你可以并行运行某个业务流程协调程序函数的多个实例。

一个业务流程协调程序函数可以通过在 .NET 中调用 [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) 或 [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) 方法，或者在 JavaScript 中调用 `callSubOrchestrator` 或 `callSubOrchestratorWithRetry` 方法来调用另一个业务流程协调程序函数。 [错误处理和修正](durable-functions-error-handling.md#automatic-retry-on-failure)一文提供了有关自动重试的更多信息。

从调用方的角度来看，子业务流程协调程序函数的行为与活动函数相同。 它们可以返回值，引发异常，并且父业务流程协调程序函数可以等待它们。

## <a name="example"></a>示例

以下示例说明了一个需要预配多台设备的 IoT（物联网）方案。 对于每台设备，都需要执行一个特定的业务流程，这可能类似于以下内容：

### <a name="c"></a>C#

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string deviceId = context.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await context.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await context.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await context.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceId = context.df.getInput();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    const sasUrl = yield context.df.callActivity("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    yield context.df.callActivity("SendPackageUrlToDevice", { id: deviceId, url: sasUrl });

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.df.waitForExternalEvent("DownloadCompletedAck");

    // Step 4: ...
});
```

此业务流程协调程序函数可以按现样用于一次性设备预配，也可以用作大型业务流程的一部分。 在后一种情况下，父业务流程协调程序函数可以使用 `CallSubOrchestratorAsync` (C#) 或 `callSubOrchestrator` (JavaScript) API 调度 `DeviceProvisioningOrchestration` 的实例。

下面是一个示例，它展示了如何并行运行多个业务流程协调程序函数。

### <a name="c"></a>C#

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string[] deviceIds = await context.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = context.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    for (const deviceId of deviceIds) {
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.push(provisionTask);
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解什么是任务中心以及如何配置它们](durable-functions-task-hubs.md)
